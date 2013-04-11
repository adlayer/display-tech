# Adlayer Thirdy Party Stack

## What is Adlayer
Adlayer is the best platform to manage delivery and measure the result of online advertising.

## How ads are loaded on the web ?

### How other guys do:
Most ad servers are placed like this:

```html
<script>
	OAS_AD('x07');
</script>

This is bad:

1. We are hurting the concept by web standards guys "in layers development".
2. This is a inline script (Dom monster says that it makes parsing slow)
3. The greater problem is that what this function is doing is actually:



The last part will load another script synchronous (blocking content).

### How we do:

```html
<head>
	<script async=true src="space"></script>
</head>

<body>
	<div class="adlayer_space" id="jdcsdc9d9csdc"></div>
</body>

Using the async method to load the script don't force us to put it in the end of body. We like it at head and downloads in background.

Basically what the space.js does is:
1. Load the content using JSONP
2. Render each space when the DOM is ready

The first part is really important because she don't have to wait for "domReady" to start make our asynchronous request (fast).

## How ads are displayed ?

### How they do:
Show the rendered data by google adsense.

### How we do:
Show an Adlayer adplacement

## Iframeless
Iframes isolates the content from thirdy-party which is a good thing when you don't trust the content.
All content placed in your page are inserted by you, so you should not be worried about that.

Iframes are also hard to customize, our tech were built for packability for extensibility, Iframes don't.

Actually exists a way to interact to iframe content using HTML5 postMessage.

But obviously it will not work on old browsers and looks stupid for some interaction.


## How data is tracked ?
We have the same origin police again to track things

So the options are: 
* JSONPI (JSON + Padding + Iframe)
* Pixel tracking

Why choose pixel tracking ?
JSONPI aka iframe POST allows you to get the response of server in the iframe content. 

Bu for us, the response does not matter, the user are not interested about if they have been tracked or not.


## Why to open source ?

## References
http://blog.adlayer.org/post/45140005964/blocos-asyncronous-vs-codigo-elegante

http://velocityconf.com/velocity2010/public/schedule/detail/15412

http://www.olark.com/spw/2011/10/lightningjs-safe-fast-and-asynchronous-third-party-javascript/

https://www.webkit.org/blog/1395/running-scripts-in-webkit/

http://www.slideshare.net/SlexAxton/breaking-the-cross-domain-barrier