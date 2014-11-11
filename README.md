[![Build Status](https://travis-ci.org/kaimallea/isMobile.png)](https://travis-ci.org/kaimallea/isMobile)
[![Node dependencies status](https://david-dm.org/kaimallea/isMobile.png)](https://david-dm.org/kaimallea/isMobile)

# isMobile

A simple JS library that detects mobile devices.



_Big thanks to [Igor Ribeio Lima](https://github.com/igorlima) for his contributions._

## Why use isMobile?

I had a specific requirement for a project when I created this:

**`- Redirect all iPhones, iPods, Android phones, and seven inch devices to the mobile site.`**

A completely separate site had been created for mobile devices, so feature detection/graceful degredation/progressive enhancement were out of the question. I had to redirect.

I couldn't do detection on the back-end, because the entire site was cached and served by Akamai; I had to do the detection client-side.

So I resorted to UA sniffing.

I tried to keep the script small (**currently ~1.70 KB, minified**) and simple, because it would need to execute in the `<head>`, which is generally a bad idea, since JS blocks downloading and rendering of anything else while it parses and executes. In the case of mobile redirection, I don't mind so much, because I want to start the redirect as soon as possible, before the device has a chance to start downloading and rendering stuff. For non-mobile platforms, the script should execute fast, so the browser can quickly get back to downloading and rendering.

## How it works

isMobile runs quickly on page load to detect mobile devices; it then creates a JavaScript object with the results.

## Devices detected by isMobile

The following properies of the `isMobile` object will either be `true` or `false`

### Apple devices

* `isMobile.apple.phone`
* `isMobile.apple.ipod`
* `isMobile.apple.tablet`
* `isMobile.apple.device` (any mobile Apple device)

### Android devices

* `isMobile.android.phone`
* `isMobile.android.tablet`
* `isMobile.android.device` (any mobile Android device)

### Windows devices

* `isMobile.windows.phone`
* `isMobile.windows.tablet`
* `isMobile.windows.device` (any mobile Windows device)

### Specific seven inch devices

* `isMobile.seven_inch`
	* `true` if the device is one of the following 7" devices:
		- Nexus 7
		- Kindle Fire
		- Nook Tablet 7 inch
		- Galaxy Tab 7 inch
		- MediaPad 7 Youth 2

### "Other" devices

* `isMobile.other_blackberry_10`
* `isMobile.other_blackberry`
* `isMobile.other_opera` (Opera Mini)
* `isMobile.other_firefox`

### Aggregate Groupings

* `isMobile.any` - any device matched
* `isMobile.phone` - any device in the 'phone' groups above
* `isMobile.tablet` - any device in the 'tablet' groups above


## Example Usage

I include the minified version of the script, inline, and at the top of the `<head>`. Cellular connections tend to suck, so it would be wasteful overhead to open another connection, just to download <1kb of JS:


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <script>
        // Minified version of isMobile included in the HTML since it's <1kb
       (function(e){var t=/iPhone/i,n=/iPod/i,r=/iPad/i,i=/(?=.*\bAndroid\b)(?=.*\bMobile\b)/i,s=/Android/i,o=/IEMobile/i,u=/(?=.*\bWindows\b)(?=.*\bARM\b)/i,a=/BlackBerry/i,f=/BB10/i,l=/Opera Mini/i,c=/Firefox OS/i,h=/Maemo/i,p=/Meego/i,d=/Symbian/i,v=/Ubuntu Touch/i,m=/webOS/i,g=/Windows CE/i,y=/Windows Phone/i,b=/Windows RT/i,w=/(?=.*\bFirefox\b)(?=.*\bMobile\b)/i,E=new RegExp("(?:"+"Nexus 7"+"|"+"BNTV250"+"|"+"Kindle Fire"+"|"+"Silk"+"|"+"GT-P1000"+"|"+"MediaPad 7 Youth 2"+")","i");var S=function(e,t){return e.test(t)};var x=function(e){var x=e||navigator.userAgent;this.apple={phone:S(t,x),ipod:S(n,x),tablet:S(r,x),device:S(t,x)||S(n,x)||S(r,x)};this.android={phone:S(i,x),tablet:!S(i,x)&&S(s,x),device:S(i,x)||S(s,x)};this.windows={phone:S(o,x),tablet:S(u,x),device:S(o,x)||S(u,x)};this.other={blackberry:S(a,x),blackberry10:S(f,x),opera:S(l,x),firefox:S(w,x),firefoxos:S(c,x),maemo:S(h,x),meego:S(p,x),symbian:S(d,x),ubuntutouch:S(v,x),webos:S(m,x),windowsce:S(g,x),windowsphone:S(y,x),windowsrt:S(b,x),device:S(a,x)||S(f,x)||S(c,x)||S(m,x)||S(v,x)||S(p,x)||S(h,x)||S(d,x)||S(y,x)||S(l,x)||S(w,x)};this.seven_inch=S(E,x);this.any=this.apple.device||this.android.device||this.windows.device||this.other.device||this.seven_inch;this.phone=this.apple.phone||this.android.phone||this.windows.phone;this.tablet=this.apple.tablet||this.android.tablet||this.windows.tablet;if(typeof window==="undefined"){return this}};var T=function(){var e=new x;e.Class=x;return e};if(typeof module!="undefined"&&module.exports&&typeof window==="undefined"){module.exports=x}else if(typeof module!="undefined"&&module.exports&&typeof window!=="undefined"){module.exports=T()}else if(typeof define==="function"&&define.amd){define(T())}else{e.isMobile=T()}})(this)


        // My own arbitrary use of isMobile, as an example
        (function () {
            var MOBILE_SITE = '/mobile/index.html', // site to redirect to
                NO_REDIRECT = 'noredirect'; // cookie to prevent redirect

            // I only want to redirect iPhones, Android phones and a handful of 7" devices
            if (isMobile.apple.phone || isMobile.android.phone || isMobile.seven_inch) {
            	
            //or
            //if(isMobile.any) {

                // Only redirect if the user didn't previously choose
                // to explicitly view the full site. This is validated
                // by checking if a "noredirect" cookie exists
                if ( document.cookie.indexOf(NO_REDIRECT) === -1 ) {
                    document.location = MOBILE_SITE;
                }
            }
        })();
    </script>
</head>
<body>
    <!-- imagine lots of html and content -->
</body>
</html>
```

### node.js usage

#####Installation
`npm install ismobilejs`

#####Usage
```
var isMobile = require('ismobilejs');
console.log(isMobile(req.headers['user-agent']).any);
```
