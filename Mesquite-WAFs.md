# Mesquite & WAFs

This page contains all my Mesquite knowledge, findings & tips. For a complete guide on easy apps, see [Illusion](/Illusion-Guide.md).

## Definitions

- WAFs - Web application frameworks. Web apps with additional features
- Mesquite - A private Amazon "service" that launches/manages WAFs using WebKit.
- Mesquito - The e-book store WAF exploit that allows you to modify store contents unjailbroken.
- Pillow - Mesquite's "glue" that seemingly handles dialogs and has elevated permissions through NativeBridge.

Those are the main ones - Mesquito and Mesquite are not the same. Mesquito is a pun on Mesquite.

## Registering an app

To make a functional app you need a WAF, you need to copy it to `var/local/mesquite` and register it to Appreg.db (`var/local/appreg.db`) to make it launchable with `lipc`. See WAF structure below. To register an app to Appreg you can use the following sh replacing variables;

```sh
APP_ID="com.example.namespace"
APP_DIR="var/local/mesquite/example"

sqlite3 "/var/local/appreg.db" <<EOF
INSERT OR IGNORE INTO interfaces(interface) VALUES('application');

INSERT OR IGNORE INTO handlerIds(handlerId) VALUES('$APP_ID');

INSERT OR REPLACE INTO properties(handlerId,name,value)
  VALUES('$APP_ID','lipcId','$APP_ID');
INSERT OR REPLACE INTO properties(handlerId,name,value)
  VALUES('$APP_ID','command','/usr/bin/mesquite -l $APP_ID -c file://$APP_DIR/');
INSERT OR REPLACE INTO properties(handlerId,name,value)
  VALUES('$APP_ID','supportedOrientation','U');
EOF
```

The `APP_ID` variable should match the one specified in the `config.xml` within the WAF.

You can then launch it like so: `lipc-set-prop com.lab126.appmgrd start app://$APP_ID`.

Please note that `opt/var/local/mesquite` is not symlinked to `var/local/mesquite`.

## WAF structure

```
/var/local/mesquite/WAF
|   index.html
|   styles.css
|   script.js
|   config.xml
```

That's an example structure you could use, although only `config.xml` and an `index.html` entry point is necessary.

## Config XML

The config.xml file contains parameters that tell Mesquite how to run your WAF. Some notable values include;

### The header

```xml
<widget id="com.example.namespace" version="1.0" viewmodes="application" xmlns="http://www.w3.org/ns/widgets"
    xmlns:kindle="http://kindle.amazon.com/ns/widget-extensions">
```

The widget ID should match the one you register in Appreg. You can also specify app version. "Application" is the only currently known viewmode.

### Name & description

Here's a Kindle Store example:

```xml
<name xml:lang="en">Kindle Store</name>
<description xml:lang="en">Amazon Kindle Store</description>

<name xml:lang="de">Kindle-Shop</name>
<description xml:lang="de">Amazon Kindle-Shop</description>

<name xml:lang="ja">Kindle&#12473;&#12488;&#12450;</name>
<description xml:lang="ja">Amazon Kindle&#12473;&#12488;&#12450;</description>
```

This part serves to show WAF name & description in the Kindle UI. It supports `xml:lang` for localisation & can use Unicode characters as shown.

### The payload

```xml
<content src="index.html" />
```

This tag tells Mesquite which file to load when the WAF is opened.

### Features

```xml
<feature name="http://kindle.amazon.com/apis" required="true">
    <param name="appmgr" value="yes" />
    <param name="net" value="yes" />
    <param name="todo" value="yes" />
    <param name="gestures" value="yes" />
    <param name="chrome" value="yes" />
    <param name="dev" value="yes" />
    <param name="dconfig" value="yes" />
    <param name="download" value="yes" />
    <param name="messaging" value="yes" />
    <param name="uitest" value="yes" />
    <param name="popup" value="yes" />
    <param name="bkgrnd" value="yes" />
    <param name="localprefs" value="yes" />
    <param name="device" value="yes" />
    <param name="winmgrUtils" value="yes" />
    <param name="bluetooth" value="yes" />
</feature>
```

Disables/enables APIs available to the Kindle object.

### Messaging

```xml
<kindle:messaging>
    <kindle:app name="com.lab126.pillow" value="yes" />
    <kindle:app name="com.lab126.chromebar" value="yes" />
    <kindle:app name="com.lab126.readnow" value="yes" />
</kindle:messaging>
```

A whitelist for which LIPC services can be called through `kindle.messaging`.

### Conclusion

That's some of the notable ones, with things like gestures & settings omitted. Please see [the wiki's](https://kindlemodding.org/wafs-and-mesquite/understanding-config-xml.html) config.xml analysis.

## Limitations

Mesquite is based on a WebKit version around Safari 5.0 (WebKit 533.16 according to MDN Web Docs) and so you can use HTML4 (some HTML5), CSS2 (some CSS3) and ES5. You can use numerous tools to overcome this; see development tools section, but it's important to keep in mind you cannot use modern web features such as `const`, `let`, `() =>`, `Promise()`, at least by default.

Mesquite & Java are also being phased out of the UI in favour of React Native.

## Kindle object

Every WAF has an exposed `window.kindle` object which acts as a bridge between JavaScript and the Kindle's system. Many APIs are available, which are configurable via the features section in a WAF's `config.xml`.

In addition to all of the APIs detailed here, `isMock: 1` and `extensions: {}` properties are exposed when loading the Kindle Store remote (the WAF loads an iframe of a website) in a modern web browser.

The Kindle Store remote is detailed more in notes.

You can see simple documentation on [the wiki](https://kindlemodding.org/wafs-and-mesquite/the-kindle-object/), but it omits `bluetooth`, `winmgrUtils`, `localprefs`, `bkgrnd`, `popup`, `uitest`, `download`, `appmgr` feature documentation mentioned in config XML.

I'll try to update the documentation at a later date because it can be quite confusing at times/nonsensical and lacks documentation of the features above mentioned.

## Dialogs

Dialogs are popups with messages shown to the user. They are handled by Pillow, wrapping an instance of Mesquite to show contents.

For example;

![Kindle release notes dialog](https://media.goodereader.com/blog/uploads/images/2018/12/03043947/WhatsNew_5_10_1_3.png.webp)

All system dialogs should be handled by Pillow.

## Pillow

Pillow dialogs are stored in `usr/share/webkit-1.0/pillow` and have varying degrees of permissions.

Pillow dialogs can be elevated, able to run any LIPC service with no config.xml whitelist (unlike regular WAFs) through NativeBridge, but sometimes they only have regular JS or none at all - I am yet to find out where this is configured.

Pillow is said to begin being replaced by KPPPillow (Kindle++ Pillow) with React Native in 5.18.3.

(Sad fact! `com.lab126.pillow` and `com.lab126.blanket` are not actually related...)

## NativeBridge

NativeBridge is like the Kindle object in the sense it bridges Mesquite to the Kindle's system, but has elevated permissions, as mentioned above. There is no NativeBridge documentation on the wiki, so here's the methods list taken from `usr/lib/libpillow.so` (with descriptions for the ones I know - though some are self-explanatory):

- devcapInitialize
- raiseChrome
- dismissChrome
- devcapGetInt
- devcapIsAvailable
- accessHasharrayProperty(lipcID, lipcEventType, {})
  - `kindle.messaging.sendMessage()` without whitelist?
- getIntLipcProperty(appID, propertyName)
  - Returns an integer property
- showDialog
- setLipcProperty(appID, propertyName, valueString)
  - Set property with string
- logString
- setIntLipcProperty(appID, propertyName, valueInt)
  - Set property with integer
- getOrientationValue
- getDynamicConfigValue
- devcapGetString
- getAppId
- getStringLipcProperty(appID, propertyName)
  - Returns a string property
- messagePillowCase
- setWindowTitle
- setWindowPosition
- getWindowPosition
- hideKb()
  - Hide the keyboard
- showKb()
  - Show the keyboard
- setAcceptFocus
- hideMe()
  - Set the supplied Pillow instance invisible (useful for `default_status_bar`)
- showMe()
  - Set the supplied Pillow instance visible (useful for `default_status_bar`)
- setWindowSize
- registerEventsWatchCallback(cb)
  - Set a function that will be called when a registered event fires; first argument of callback gets value
- subscribeToEvent(appID, eventID)
  - Add an event that triggers the function supplied to registerEventsWatchCallback()
- cancelPendingDismiss
- registerClientParamsCallback
- dismissMe
- clearFlashTrigger
- createFlashTrigger
- flash
- redraw
- logTime
- logDbgNum
- logDbg
- dbgCmd
  - Execute command, mainly from `/usr/share/webkit-1.0/pillow/debug_cmds.json`

Also see [PillowHelper](https://github.com/PaulFreund/WebLaunch/blob/master/bin/pillowHelper.js)!

## Development tools

There are many tools available to assist you with app development. For example;

- [Can Mesquite Use?](https://html-preview.github.io/?url=https://github.com/polish-penguin-dev/Illusion/blob/main/Mesquite/Can-Mesquite-Use.html) - CanIUse for Mesquite. See if you can use certain web features
- [BabelJS](https://babeljs.io/)/[SWC](https://swc.rs/) - Transpile modern JS (e.g. ES6) to ES5 Kindle-compatible JS
- [Polyfill](https://github.com/polish-penguin-dev/IllusionChess/blob/master/illusionChess/js/polyfill.min.js) - Allow you to use modern web APIs on the Kindle (NOT language features!). Best in combination with a transpiler
- [Mesquito SDK](https://github.com/KindleModding/Mesquito/blob/main/mesquito/sdk.js) - Although adapted for usage in the Store WAF, it contains useful scriptlets and functionality regarding Chromebar, for example.
- [Illusion](https://github.com/polish-penguin-dev/Penguins-Kindle-Wiki/blob/main/Illusion-Guide.md) - A scriptlet which registers and launches WAFs for you
- [ScreenControl](https://github.com/polish-penguin-dev/Illusion/blob/main/Assets/screencontrol-binaries.tar.gz) - Control your Kindle from the web

## Exploits

Because Mesquite is quite old (and being phased out of the UI with React Native), it's been known to be susceptible to exploits. Here are some examples;

### Mesquito

[Mesquito](https://github.com/KindleModding/Mesquito/) itself is an exploit, replacing the contents of the Kindle Store WAF through `.active_content_sandbox` cache. It is semi-persistent, as the store constantly "updates" and overwrites this cache.

This was patched in 5.18.1+ but if you make the cache extremely large (e.g. by pasting in filler files) or pasting and doing a hard reset some people mention cache deletion doesn't happen/is slower.

### WinterBreak

[WinterBreak](https://github.com/KindleModding/WinterBreak) is a more complex jailbreak using a couple exploits. It firstly utilises Mesquito, to overwrite the store. It then sends a message to `com.lab126.pillow`, event ID `customDialog`. In this method you would usually specify a `dialog.html` file and it would display `/usr/share/webkit-1.0/pillow/ + [PATH]` (or something along those lines). But because of this, you could specify something like `../../../../../../mnt/us/dialog.html` in the path and it could load a dialog with NativeBridge access from `/mnt/us`, accessible unjailbroken. This is done to run `com.lab126.transfer` through LIPC which runs `jb.sh`. NativeBridge was necessary and `customDialog` because of the Mesquite `sendMessage` whitelist in the store config XML which does not allow for `com.lab126.transfer`.

This `customDialog` exploit is patched in 5.18.1+.

### LanguageBreak one-click

[LanguageBreak One-Click](https://github.com/notmarek/LanguageBreak/tree/oneclick) is what WinterBreak is based upon. It also uses NativeBridge, `customDialog`, and Mesquito, except it uses LanguageBreak method to run sh and not `com.lab126.transfer`. It is more experimental.

### Experimental browser XSS (FileManager.js)

On firmware versions below 5.16.4, the experimental browser uses Mesquite and not Chromium. It is essentially a WAF with some scripts (including FileManager.js) that turns it into a browser with a URL bar, downloads functionality, etc...

When you download a file, it is handled by `FileManager.js` which creates a dialog asking "Would you like to download [File Name].[Ext]?". You can place HTML elements in the download name, allowing for XSS, and running a dialoger HTML. This Pillow dialog does not have NativeBridge access though.

It is patched in modern firmware, and used by [Winterbreak2](https://github.com/KindleModding/Winterbreak2).

### KindleDrip

I would recommend reading [this article](https://medium.com/@baronyogev/kindledrip-from-your-kindles-email-address-to-using-your-credit-card-bb93dbfb2a08) on how KindleDrip utilised send-to-Kindle, Mesquite, JPEG XR overflow, and more to achieve RCE.

### Todo Dconfig Replacement

`kindle.todo` is a very interesting kindle object API that lets you send messages within the internal lab126 ToDo messaging system. In theory, this would allow for the downloading of new screensavers, updating wishlists, etc, and considering just how this connects everything, a jailbreak could even be theoretically possible (but not too practical considering it's much harder to actually make any custom WAFs now below 5.18.1). If you look at [Detour](https://github.com/polish-penguin-dev/Detour) in the "Detour Installer" section, you can see `kindle.todo` being used to modify dconfig values, and therefore replace the endpoint loaded in the store's `<iframe>`.

## UI design

UI design can be tricky on the Kindle because scaling varies wildly between models, and CSS2 lacks modern features like Grid or Flexbox.

I would recommend using `@media` queries and `%` values for optimal scaling between Kindle models. Additionally, to center a `<div>` element, you can use tables:

```html
<style>
  html, body {
    width: 100%;
    height: 100%;
    padding: 0px;
    margin: 0px;
    overflow: hidden;
  }

  .outer {
    display: table;
    width: 100%;
    height: 100%;
  }

  .inner {
    display: table-cell;     
    vertical-align: middle;  
    text-align: center;   
  }
</style>

<div class="outer">
    <div class="inner">
        <h1>Centered!</h1>
    </div>
</div>
```

## Points of reference

Here are some Mesquite apps you can use as reference when building your own:

- [IllusionChess](https://github.com/polish-penguin-dev/IllusionChess) - Penguins184
- [KAnki](https://github.com/crizmo/KAnki) - Kurizu
- [KWordle](https://github.com/crizmo/KWordle/) - Kurizu
- [KindleForge](https://github.com/polish-penguin-dev/KindleForge) - Penguins184, Kurizu, Gingrspacecadet

## Notes

Here are some additional notes/info you may find useful.

### The Kindle Store remote

In WAFs, `kindle.dconfig` stores store-specific values.

`kindle.dconfig.getValue("url.store")` returns `https://www.amazon.co.uk/gp/digital/juno/index.html` (with varying TLDs based on region). The store WAF loads this URL into an `iframe`.

You can access this now, but will get a (fake) `HTTP 404` error unless your userAgent abides by regex `\d-juno_\d`.

Oddly, appending query parameters `?clientId=ereader&apiVersion=1.0&deviceType=A21RY355YUXQAF` (device type shouldn't matter) you receive: "Please update your Kindle software to connect to the Kindle store. Kindle software can be downloaded here: [http://www.amazon.co.uk/KindleSoftwareUpdate](http://www.amazon.co.uk/KindleSoftwareUpdate)".

Additionally, appending `?developer=1` query parameter the source code is not truncated and fully commented.

Previously it is said this prefills a mock Kindle object if loaded in a modern web browser, but this does no longer happen. The object is still probably stored in Kindle firmware.

### Utild

[Utild](https://github.com/KindleModding/utild) is a simple LIPC service you can use to just run `sh` instead of the `com.lab126.transfer` exploit. It works more reliably and can be installed & used in Mesquite apps to run actual commands. See IllusionUtild for more.

### Per-platform config.xmls

As seen in the store WAF, you can have different config.xmls per kindle platform, by creating a `config_[platform].xml` file. This is mostly useless, but could potentially be used for scaling/opening different "payloads" per-platform if you can't use `@media` queries. Here's a list of platforms and which devices they map out to:

- Bellatrix; Kindle PW5, Basic 4
- Duet; Kindle Oasis
- Heisenberg; Kindle Basic 2 (8th Gen)
- Rex; Kindle PW4, Basic 3 (10th Gen)
- Wario; Kindle PW2, Basic, Voyage, PW3
- Yoshi; Kindle Touch, Kindle 4
- Yoshime3; Kindle PW
- Zelda; Kindle Oasis 2, 3

## FAQ

### Why can't I use `kindle.messaging.recieveMessage()` with Utild?

This happens because `Utild` returns command value with `lipc-get-prop` whereas to use `recieveMessage()` an event must be sent to the WAF with `lipc-send-event`.

### Why does `com.lab126.transfer` not run sh?

`com.lab126.transfer` can only be ran with a valid internet connection.

### Does Chromium have WAFs?

With the introduction of Chromium in 5.16.4 and slow replacement of Mesquite, it seems reasonable - but Chromium cannot act as a WAF with Kindle object.

## Conclusion

Thanks for reading my Mesquite & WAFs writeup!

Contact Penguins184 on Discord to suggest updates/ask any questions. Bye!


