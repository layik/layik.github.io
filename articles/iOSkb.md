---
permalink: /iOSkb.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

10th Apr 2019

# iOS Custom Keyboard Height
Apple has been doing a great job providing developer tools (experience) for iOS, and I have said this throughout the years I have worked with iOS. When Interface Builder was first out, it was literally nothing like the eclipse plugin on Android. However, we all know that when it comes to Open Source, you just have to be open source to beat it.

<img width="1024" alt="iOS custom keyboard docs" src="https://user-images.githubusercontent.com/408568/55861740-e9e2dd00-5b6e-11e9-9a00-659cc5f9234f.png">
<caption>"Documentation Archive" screenshot</caption>

When it comes [documentations](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html), now in two languages for iOS, will always be a tough one to crack without a mess. I used to stay within Xcode most of the time, but when it comes to restrictions vs what you like to achieve, you need to turn into the docs.

So, when I wanted to support iPhone X on Kurdish Sorani keyboard extension, there is little (days of googling) support to say how this could be done.

So, for the past however many years I have had an Apple developer account, I did not need to use my tickets for Code Level Support. This time, I had to. The anwer was not one that I expected. Something like, "oh you are missing xyz protocol" or something like that. But the helpful developer assistant is almost as unsure as I was.

Here is an extract of the email:

> I happened to have a test project (attached) that can change the height of a keyboard extension on my iPhone X + iOS 12. Could you please manage to run it on your device and see if it works for you?

I then followed it with a clarification. (S)he then replied with something along the lines of "should work": 

> If you see otherwise and are willing to provide a sample to demostrate the issue, I’d be happy to take a look.

Now, supporting a device like iPhone X/iPad X is quite important. I would have expected a much more robust, refer to the docs or a published example rather than "I happened to ...".

Hopefully the [example](https://github.com/layik/ioskb/commit/dce74a39a8e81c9c2cba3f057cc151d7ba3688e6) here can be used by others and I have taken their answer to StackOverflow too.

Finally, I would love to be in a position to use my rare language on system and I understand that we should not be reinventing any wheels. Until then, this is complete waste of time unless you are part of SwiftKey Co.


