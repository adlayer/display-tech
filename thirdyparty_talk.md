# Adlayer Thirdy Party Stack

## What is Adlayer
Adlayer is the best platform to manage delivery and measure the result of online advertising.

## How ads are loaded on the web ?

### How other platforms do:
Most ad servers are placed like this:

```html
<script>
	OAS_AD('x07');
</script>
```

This is bad:

1. We are hurting the concept by web standards guys "in layers development".

[<img src="http://d.alistapart.com/understandingprogressiveenhancement/m-m.jpg" />](http://alistapart.com/article/understandingprogressiveenhancement)

2. This is a inline script ([Dom monster](http://mir.aculo.us/dom-monster/) says that it makes parsing slow)

<img src="https://raw.github.com/adlayer/display-tech/master/benchmarks/imgs/dom_monster_inline_javascript_tip.png" />

3. The biggest problem is that what this function is doing actually is:

```
document.write('<a href="'+OAS_url	+"click_nx.ads/"+OAS_sitepage+"/1"+OAS_rns+"@"+OAS_listpos+"!"+pos+"?"+OAS_query+'" target="'+OAS_target+'">');
document.write('<img src="'+OAS_url+"adstream_nx.ads/"+OAS_sitepage+"/1"+OAS_rns+"@"+OAS_listpos+"!"+pos+"?"+OAS_query+'" border="0"></a>');
```

Here's what [Jshint](http://www.jslint.com) says about ```document.write```:

<img src="https://raw.github.com/adlayer/display-tech/master/benchmarks/imgs/jshint_document_write_error.png" />

And here is what Douglas Crockford says about ```eval```:

<img src="https://raw.github.com/adlayer/display-tech/master/benchmarks/imgs/eval_is_evil.png" />

Some people are doing it even worse:

```html
OAS_rn=new String(Math.random());
OAS_rns=OAS_rn.substring(2,11);
document.write("<scr"+"ipt type='text/javascript' src='http://ads.example.com/Adserver/ads/adstream_jx.ads/example.com/example.com/home/1" + OAS_rns +"@Top1'><\/script>");
```

Which will obviously write:

```html
<script type="text/javascript" src="http://ads.example.com/Adserver/ads/adstream_jx.ads/example.com/example.com/home/1189969050@Top1"></script>
```

This recursive and synchronous script loading technique could block the page content for some time.

<img src="https://raw.github.com/adlayer/display-tech/master/benchmarks/imgs/recursive_sync_script_call.png" />

Sometimes this services also inject some code like this:

```javascript
var oasObj = {};
oasObj.tipo = "oas_flash";
oasObj.div = oas_div;
oasObj.width = 300;
oasObj.height = 100;
oasObj.wmode = "transparent";
```

By just assign values to variables in the ```global level``` disable this codes to run async and extends the chances of **conflict** with other ads of thirdy-party or with the site script variable.

----------------------------

### How Adlayer do:

```html
<head>
	<script async=true src="//api.adlayerjavascriptsdk.com/spaces.min.js"></script>
</head>

<body>
	<div class="adlayer_space" id="jdcsdc9d9csdc"></div>
</body>
```
Using the async method to load the script do not force you to put it in the end of body. We like it on the head of html because it's looks more organized and downloads the script in background.

Basically what the space.min.js do is:

1. Load the content using JSONP
2. Render each space (div tag) when the DOM is ready

This is fast because it download the JSONP request even before the "DOM is Ready". [reference](http://alexsexton.com/blog/2010/01/dont-let-document-ready-slow-you-down)

## How ads are displayed ?

### How other platforms do:

Most adservers loads extra content in order to render the ads and append features that allow it to be tracked.

Load so manny Assets forces this vendors to isolate all content in one of most controversial tags of HTML the iframes.

See how the [code rendered](benchmarks/rendered/adsense.html) by the popular [Google Adsense](benchmarks/rendered/adsense.html) service looks like:

[Google Adsense rendered code example](benchmarks/rendered/adsense.html)

### How we do:

Adlayer do not load any unnecessary asset to show the ad loaded or track it.

We provide the cleanest way [to display online advertising](benchmarks/rendered/adlayer.html)

[Adlayer Widget rendered code example](benchmarks/rendered/adsense.html)

## Iframeless
Iframes isolates the content from thirdy-party, which is a good thing when you don't trust the content being loaded.
With Adlyer all content placed in your page are inserted by you, so you should not be worried about this.

Iframes are also hard to customize, our tech was built for hackability for extensibility, Iframes don't.

Actually exists a way to interact to iframe content using HTML5 postMessage. But obviously it will not work on old browsers and looks stupid for some interaction.

## How data is tracked ?
Given the "same origin police" problem, there are few options to track data on thirdy-party libs:

* JSONPI (JSON + Padding + Iframe)
* Pixel tracking

#### JSONPI
JSONPI Is a technique that creates virtual iframes embedding some forms from other domains and allow thirdy-party libs to submit data over the web.

#### Pixel Tracking
Pixel Tracking, Web beacon or Image Tracking is a highly used technique to track data used by adservices, web analytics and other tools.

Basically what it does is create an image tag virtually from some url passing the tracking data as query string.

The server have the responsibility to read the query string and persist it even it not being a PUT to PUT request (Default http methods for save data on the web).


#### Why we choose Pixel Tracking ?
JSONPI aka iframe POST allows you to get the response of server in the iframe content. Knows the server response can be usefull  to know if everything worked as expected or something get wrong.

But in the real world with a lot of request per second reaching our servers, what a client-side library could do if something get wrong ?

Users change pages more quickly that we can retry the operation, so the response of the server is not much useful there nothing to do.

We can't make the user wait to be sure that he have being tracked. So Pixel tracking makes sense, response does not matter and we don't even have to worry if it have completed or not.

This could leaves to inconsistency ? yes but every tracking method will be inconsistency at this times. Visitors moves fast between pages and choose the fastest way to track they is the best we can do.


## Why to open source ?

## References
http://blog.adlayer.org/post/45140005964/blocos-asyncronous-vs-codigo-elegante

http://velocityconf.com/velocity2010/public/schedule/detail/15412

http://blog.socialcast.com/third-party-javascript/

http://www.olark.com/spw/2011/10/lightningjs-safe-fast-and-asynchronous-third-party-javascript/

https://www.webkit.org/blog/1395/running-scripts-in-webkit/

http://www.slideshare.net/SlexAxton/breaking-the-cross-domain-barrier