---
permalink: /wowchemy.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

This "academic" theme seems to be making some noise on GH and there are some 5K stargazers on it. When I tried it, the documentation was a pack of signposts and arrows to use Netlify. Now, obviously open source contributors should be making a living and it is unfair for me to criticise any effort of releasing code without "proper" documentation. This would all be, I am sure, vehemently rejected by those contributors as my failure of getting it right or reading the docs right.

With that out of my chest, the template is just a standard one. However, if you have copied their `starter-academic` repo then you must watchout for:

1. Hugo version failing. If you cannot get it to parse anything such as `hugo new ...` then chances are your version of Hugo is different. I do not know much about Hugo so let this one pass and let you fight your own battle here.

2. The next issue you might come across is just finding where the whole "[documentation](https://wowchemy.com/docs/install-locally/)" is. All it says on their website, is clone the starter and cd into it and run `hugo server`. But that is not going to work I bet. The docs says:

* clone the repo git clone https://github.com/wowchemy/starter-academic.git
* cd into it (starter-academic)
* `hugo server`

The reason it might fail is that in the "default" config at `config/_default/config.toml` which has 
```yml
[module]
  [[module.imports]]
    path = "github.com/wowchemy/wowchemy-hugo-modules/wowchemy"

```
while the `themes` folder has `academic` in it. How do you solve this as you cannot add the above github line to submodule?

To do this properly:
* OK clone the repo `starter-academic`
* of course cd in there
* uncomment the three lines above in the `config/_default/config.toml
* clone the https://github.com/wowchemy/wowchemy-hugo-modules somewhere and then move the actual theme called "wowchemy" from that repo into your `themes/wowchemy` without worrying about deleting old "academic"
* just let Hugo know: `hugo server --theme=wowchemy`

Done.