---
permalink: /decklayer.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

## Custom layer code
For those who do not want to read all this, find the sample layer code here and hidden below:


<details>
<summary>Custom Layer</summary>
<p>

```js

import {Layer, project32, picking} from '@deck.gl/core';
import GL from '@luma.gl/constants';
import {Model, Geometry} from '@luma.gl/core';

const vs = `
  attribute vec3 positions;
  attribute vec3 instancePositions;
  attribute vec3 instancePositions64Low;
  attribute float instanceRadius;
  attribute vec4 instanceColors;
  attribute float instanceScale;
  attribute float instanceRotationAngle;
  attribute float instanceWidth;
  // for onHover to work must
  // delcare instancePickingColors here and assign it to
  // geometry.pickingColor
  // and then register DECKGL_FILTER_COLOR hook in
  // fragment shader
  attribute vec3 instancePickingColors;

  varying vec4 vColor;
  varying vec2 vPosition;

  vec2 rotate_by_angle(vec2 vertex, float angle) {
    float angle_radian = angle * PI / 180.0;
    float cos_angle = cos(angle_radian);
    float sin_angle = sin(angle_radian);
    mat2 rotationMatrix = mat2(cos_angle, -sin_angle, sin_angle, cos_angle);
    return rotationMatrix * vertex;
  }

  void main(void) {
    geometry.pickingColor = instancePickingColors;

    vec3 offsetCommon = positions * project_size(instanceRadius);
    offsetCommon = vec3(rotate_by_angle(offsetCommon.xy, instanceRotationAngle), 0);
    offsetCommon.x = offsetCommon.x * instanceWidth;
    // width first
    offsetCommon = offsetCommon * instanceScale;
    
    vec3 positionCommon = project_position(instancePositions, instancePositions64Low);
    // missx: So in your shader, positionCommon is the anchor position on the map. 
    // offsetCommon is the local coordinate relative to the anchor
    // You want to leave the anchor alone and rotate the offset

    gl_Position = project_common_position_to_clipspace(vec4(positionCommon + offsetCommon, 1.0));

    vPosition = positions.xy;
    vColor = instanceColors;
    DECKGL_FILTER_COLOR(vColor, geometry);

  }`;

const fs = `
  precision highp float;

  uniform float smoothRadius;

  varying vec4 vColor;
  varying vec2 vPosition;

  void main(void) {    
    float distToCenter = length(vPosition);

    if (distToCenter > 1.0) {
      discard;
    }

    float alpha = smoothstep(1.0, 1.0 - smoothRadius, distToCenter);
    gl_FragColor = vec4(vColor.rgb, vColor.a * alpha);
    DECKGL_FILTER_COLOR(gl_FragColor, geometry);
  }`;
const defaultProps = {
  // Center of each circle, in [longitude, latitude, (z)]
  getPosition: {type: 'accessor', value: x => x.position},
  // Radius of each circle, in meters
  getRadius: {type: 'accessor', value: 30},
  // Color of each circle, in [R, G, B, (A)]
  getColor: {type: 'accessor', value: [0, 0, 0, 255]},
  // Amount to soften the edges
  smoothRadius: {type: 'number', min: 0, value: 0.5},
  // Amount to scale bottom of arrow
  getScale: {type: 'accessor', value: 1},
  // Amount to rotate line with
  getRotationAngle: {type: 'accessor', value: 1},
  // Amount to thicken the line with
  getWidth: {type: 'accessor', value: 1},
};

export default class BarLayer extends Layer {
  getShaders() {
    return super.getShaders({vs, fs, modules: [project32, picking]});
  }

  initializeState() {
    this.getAttributeManager().addInstanced({
      instancePositions: {
        size: 3,
        type: GL.DOUBLE,
        accessor: 'getPosition'
      },
      instanceRadius: {
        size: 1,
        accessor: 'getRadius',
        defaultValue: 1
      },
      instanceColors: {
        size: 4,
        type: GL.UNSIGNED_BYTE,
        accessor: 'getColor',
        defaultValue: [1, 0, 0, 255]
      },
      smoothRadius: {
        size: 1,
        normalized: true,
        type: GL.UNSIGNED_BYTE,
        accessor: 'smoothRadius',
        defaultValue: [1, 0, 0, 255]
      },
      instanceScale: {
        size: 1,
        accessor: 'getScale',
        defaultValue: 1
      },
      instanceRotationAngle: {
        size: 1,
        accessor: 'getRotationAngle',
        defaultValue: 1
      },
      instanceWidth: {
        size: 1,
        accessor: 'getWidth',
        defaultValue: 1
      }
    })
  }
  updateState({props, oldProps, changeFlags}) {
    super.updateState({props, oldProps, changeFlags});
    
    if (changeFlags.extensionsChanged) {
      const {gl} = this.context;
      if (this.state.model) {
        this.state.model.delete();
      }
      this.setState({model: this._getModel(gl)});
    }
  }

  draw({uniforms}) {
    this.state.model
      .setUniforms(uniforms)
      .setUniforms({
        smoothRadius: this.props.smoothRadius
      })
      .draw();    
  }

  _getModel(gl) {
    const positions = [
      -.1, -1, 
      0.1, -1,
      -.1, 1,
      -.1, 1,
      0.1, 1,
      0.1, -1,
    ];
    return new Model(
      gl,
      Object.assign(this.getShaders(), {
        id: this.props.id,
        geometry: new Geometry({
          drawMode: GL.TRIANGLE_STRIP,
          vertexCount: 6,
          attributes: {
            positions: {size: 2, value: new Float32Array(positions)}
          }
        }),
        isInstanced: true
      })
    );
  }
}

BarLayer.layerName = 'BarLayer';
BarLayer.defaultProps = defaultProps;

```

<p>
</details>


## Intro to this read
I am hoping that if not code examples, then clarifications of how different JavaScript libraries have been brought together to build DeckGL layers would help you make progress through what was a daunting task for me.

## What is DeckGL?

From the [documentations](https://tgorkin.github.io/docs):
> deck.gl is designed to make visualization of large data sets simple. It enables users to quickly get impressive visual results with limited effort through composition of existing layers, while offering a complete architecture for packaging advanced WebGL based visualizations as reusable JavaScript layers.

The magic really happens with the layers. Therefore, if you are reading this blogpost to solve an issue whilst writing your custom layer, then you are in the right place. It is not an introduciton as the [documentation](https://tgorkin.github.io/docs) is there for that reason.

Subclassing a layer is best when,as the docs state, to change something but if you want your own shape, own variables to change the shape then custom layer is the way to go. And to get there, we first need to dissect a layer, from my point of view, better than the docs or other guides you may have come across.

An overvview of how the different libraries stack up is shown in the image below:
<img src="https://user-images.githubusercontent.com/408568/83644072-c4056c80-a5a8-11ea-815c-ce3e40f5663f.jpg" width="100%"/>

### WebGL
At this point, if you have no idea what WebGL is or never had a got at it, I believe you should spend some time and find your own favouritee tutorial to at least draw a boring triangle. I am no good at watching tutorials especially code based chunks but if you like videos I think this one is an honest take.

What you need to know and what most of the docs in DeckGL is not sign posted is which part of it does refer to WebGL and which parts refer to luma.gl (next). This was the source of most of my confusion which I must say stemmed from the fact that I did not want to learn all this to be able to draw a boring triangle layer on a slippy map using the DeckGL stack.

#### Vertx/Fragment shaders
I will not write more than this paragraph on shaders but you need to spend quite a bit of time time getting your head around shaders. This is where the actual magic happens and everythign is wrapped around these two concepts. The two functions are C/C++ like functions in GLSL (Graphics Library Shading Language) language without which you may not even be able to subclass layers.

### luma.gl

So again from its docs, which by the way is a core exported object even in the `deckgl.min.js` library luma is:
> luma.gl is a high-performance toolkit for WebGL-based data visualization. luma.gl is the core 3D rendering library in the vis.gl framework suite.

Why the need? Because there are many more. WebGL is, if you have watched the vide above or any of your tutorials, is notoriously difficult to use in 2020 even during lockdowns. Instead you will be able to say "hello trianlge" using luma.gl as follows:

```js
import {AnimationLoop, Model} from '@luma.gl/engine';
import {Buffer, clear} from '@luma.gl/webgl';

const loop = new AnimationLoop({
  onInitialize({gl}) {
    const positionBuffer = new Buffer(gl, new Float32Array([
      -0.5, -0.5,
      0.5, -0.5,
      0.0, 0.5
    ]));

    const colorBuffer = new Buffer(gl, new Float32Array([
      1.0, 0.0, 0.0,
      0.0, 1.0, 0.0,
      0.0, 0.0, 1.0
    ]));

    const vs = `
      attribute vec2 position;
      attribute vec3 color;

      varying vec3 vColor;

      void main() {
        vColor = color;
        gl_Position = vec4(position, 0.0, 1.0);
      }
    `;

    const fs = `
      varying vec3 vColor;

      void main() {
        gl_FragColor = vec4(vColor, 1.0);
      }
    `;

    const model = new Model(gl, {
      vs,
      fs,
      attributes: {
        position: positionBuffer,
        color: colorBuffer
      },
      vertexCount: 3
    });

    return {model};
  },

  onRender({gl, model}) {
    clear(gl, {color: [0, 0, 0, 1]});
    model.draw();
  }
});

loop.start();
```
[<img width="100%" alt="Screenshot 2020-06-03 at 15 13 33" src="https://user-images.githubusercontent.com/408568/83647451-cff32d80-a5ac-11ea-9c4e-9a4ab91df12b.png">]((https://luma.gl/examples/getting-started/hello-triangle))

See the live [demo](https://luma.gl/examples/getting-started/hello-triangle).

Now, going from this boring triangle to the one on the map is why this blogpost is written. Again the [demo](https://luma.gl/examples/showcase/geospatial/) from luma.gl docs.
<img width="100%" alt="boring triangle on slippy map" src="https://user-images.githubusercontent.com/408568/83646887-2875fb00-a5ac-11ea-95c4-20ec5acb2d32.png" />

### DeckGL
So using the wrapper of luma.gl around WebGL primitive functions we can then think in terms of DeckGL, which is a camera instance looking at optionally a tiled/slippy map with custom visualizations draped/superimposed on it.

#### Custom layer

So this will be a huge jump over the three libraries mentioned to be able to create a layer which can take various variables to reproduce the Washington Post election bar vis.

So the workflow is as follows working from your custom layer in DeckGL via luma.gl down to the WebGL. I cannot stress the importance of any of thesee but because you can subclass layers without digging this deep, maybe the two vertext and fragment shaders are the most important parts of the puzzle.

<img width="100%" alt="boring triangle on slippy map" src="https://user-images.githubusercontent.com/408568/83649280-ea2e0b00-a5ae-11ea-9f98-9a57a4229b12.jpg" />

```js
// *** partial code ****
import {Layer, project32, picking} from '@deck.gl/core';
import GL from '@luma.gl/constants';
import {Model, Geometry} from '@luma.gl/core';

const vertexShader = `
  attribute vec3 positions;
  attribute vec3 instancePositions;
  ....
  ....
  }`;

const fragmentShader = `
  precision highp float;
  ....
  ...
  }`;
const defaultProps = {
  // Center of each bar line, in [longitude, latitude, (z)]
  getPosition: {type: 'accessor', value: x => x.position},
  ....
};

class MyLayer extends Layer {
  
  getShaders() {
    return super.getShaders({vs, fs, modules: [project32, picking]});
  }

  initializeState() {
    this.getAttributeManager().addInstanced({
      instancePositions: {
        size: 3,
        type: GL.DOUBLE,
        accessor: 'getPosition'
      }
      ...
    })
  }
  updateState({props, oldProps, changeFlags}) {
    super.updateState({props, oldProps, changeFlags});
    
    if (changeFlags.extensionsChanged) {
      const {gl} = this.context;
      if (this.state.model) {
        this.state.model.delete();
      }
      // crucial line which keeps model updated
      this.setState({model: this._getModel(gl)});
    }
  }

  draw({uniforms}) {
    // the draw function which calls luma.gl
    this.state.model
      .setUniforms(uniforms)
      .setUniforms({
        smoothRadius: this.props.smoothRadius
      })
      .draw();    
  }

  _getModel(gl) {
    // draw a line using GL.TRIANGLE_STRIP 
    // calling WebGL via luma.gl
    const positions = [
      -.1, -1, 
      0.1, -1,
      -.1, 1,
      -.1, 1,
      0.1, 1,
      0.1, -1,
    ];
    return new Model(
      gl,
      Object.assign(this.getShaders(), {
        id: this.props.id,
        geometry: new Geometry({
          drawMode: GL.TRIANGLE_STRIP,
          vertexCount: 6,
          attributes: {
            positions: {size: 2, value: new Float32Array(positions)}
          }
        }),
        isInstanced: true
      })
    );
  }
}

MyLayer.layerName = 'MyLayer';
MyLayer.defaultProps = defaultProps;
```

<img width="100%" alt="wp bar vis deckgl layer" src="https://user-images.githubusercontent.com/408568/83650639-7a208480-a5b0-11ea-8a44-39d0dac0c31c.png" />

### Events on\<Event\>
We are in a WebGL environment, how do we propagate events up and down the stack? It is not easy. The DeckGL team has done it via colour coding or hooks such as `DECKGL_FILTER_COLOR`. This was not so clear in the [documentations](https://deck.gl/#/documentation/developer-guide/writing-custom-layers/picking) for me either. Therefore I had to seek help from the Slack Group and especially [Xiaoji](http://www.xiaoji-chen.com). The mechanism of customising what is passed through the hiararchy of the layers, is well documented but not the shader level hook registrations.

In the above chunk, there is a `geometry` object declared in DeckGL which is one of the parameters passed to the filter. This is all ignoring the important `gl_Position` and `gl_FragColor` parameters in the shader functions.

```glsl
const vertexShader = `
  attribute vec3 positions;
  attribute vec3 instancePositions;
  ....
  ....
  DECKGL_FILTER_COLOR(vColor, geometry);
  }`;

const fragmentShader = `
  precision highp float;
  ....
  ...
  DECKGL_FILTER_COLOR(gl_FragColor, geometry);
  }`;
```
I assume `DECKGL_FILTER_COLOR` [calls](https://tgorkin.github.io/docs/developer-guide/custom-layers/picking) the underlying luma.gl function, from [lumag.gl docs](https://luma.gl/docs/api-reference/shadertools/core-shader-modules)
> It is strongly recommended that picking_filterPickingColor is called last in a fragment shader, as the picking color (returned when picking is enabled) must not be modified in any way (and alpha must remain 1) or picking results will not be correct.