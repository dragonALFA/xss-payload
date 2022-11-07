There are times during a web application penetration test when Cross Site Scripting (XSS) has been identified with a trivial payload such as <script>alert(1)</script> or via a Burp Suite scan result and you want to take it further, but for various reasons it’s not as easy as you would like to inject a useful JavaScript payload. This could be due to output encoding or character blacklisting, which might turn all your single and double quotes into their HTML entity equivalent, or it could be that simply by a quirk of where the injection occurs only non-letter characters can be used. In these cases we usually turn to encoding to build up an obfuscated string which will hopefully bypass these restrictions and that the application can interpret and execute.

For example, an application might block certain keywords in a string, so you encode your payload as base64 and then execute it by decoding with atob() and passing that into eval(), assigned to the “onerror” event of an image, to bypass restrictions on scripts:

<img src=x onerror=eval(atob('YWxlcnQoJ0kgb25seSB3cml0ZSBsYW1lIFBvQ3MnKQ=='))/>
Burp Suite includes a few options for this, such as the “Decoder” tool, which can convert strings to and from base64, URL encoding, ASCII hex etc, and the often overlooked “Construct string” function which includes “Construct string in JavaScript” to create a string purely by using the String.fromCharCode() method.


Constructing a string using String.fromCharCode

This is fine for doing a quick fire and forget payload, but what happens when it doesn’t work and you want to make some changes? You slowly find yourself getting caught in your browser’s Developer Tools and making tweaks, then laboriously having to re-encode them to make your chosen bypass method work, then re-encapsulating them into whatever delivery method you were able to use to execute the XSS payload. It’s time consuming, boring and ends up leading to difficult to spot errors because you’re no longer looking at readable source code.

When the injection method becomes more complex, such as when testing blind XSS with something like 0xsobky’s XSS polyglot, having to encode a simple payload into a string begins to quickly become non-trivial.

There were no time-saving options for solving this problem so I decided to make one, and added it to our XSS Payloads repository. It works by setting three basic options for the payload you want to generate which combine to create an encoded string. They should be fairly logical once you get started, but in brief, they are as follows.

XSS Payload
This is what you want the actual action to be when the XSS is executed and includes two methods for inserting an external script (JQuery and document.createElement), two methods for invoking a URL request (XMLHttpRequest and Image), which are good for establishing execution has occurred when testing for blind XSS, and finally the option to simply write your own JavaScript snippet for when you just gotta have alert(1).

Selecting one of the two load script payloads automatically gives you the option of choosing from the scripts in the XSS payloads repository.
