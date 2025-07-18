# Mesquite & WAFs

This page contains all my mesquite knowledge, findings & tips. For a complete guide on easy apps, see [Illusion](/Illusion-Guide.md).

## Definitions

- WAFs - web application frameworks. Web apps with additional features
- Mesquite - a private amazon "Service" that launches/manages wafs, using webkit.
- Mesquito - the e-book store waf exploit that allows you to modify store contents unjailbroken.
- Pillow - mesquite's "Glue" that seemingly handles dialogs and has elevated permissions through nativebridge.

Those are the main ones - mesquito and mesquite are not the same. Mesquito is a pun on mesquite.

## Registering An App

To make a functional app you need a waf, you need to copy it to `var/local/mesquite` and register it to appreg.db (`var/local/appreg.db`) to make it launchable with `lipc`. See waf structure below, to register an app to appreg you can use the following sh replacing variables;

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

The `app_id` variable should match the one specified in the `config.xml` within the waf. 

You can then launch it like so; `lipc-set-prop com.lab126.appmgrd start app://$app_id`.

Please note that `opt/var/local/mesquite` is not symlinked to `var/local/mesquite`!

## WAF Structure

```
/var/local/mesquite/WAF
|   index.html
|   styles.css
|   script.js
|   config.xml
```

That's an example structure you could use, although only `config.xml` and an `index.html` entrypoint is necessary.

## Config XML

The config.xml file contains parameters that tell mesquite how to run your waf. Some notable values include;

### The Header

```xml
<widget id="com.example.namespace" version="1.0" viewmodes="application" xmlns="http://www.w3.org/ns/widgets"
    xmlns:kindle="http://kindle.amazon.com/ns/widget-extensions">
```

The widget id should match the one you register in appreg. You can also specify app version. "Application" is the only currently known viewmode.

### Name & Description

Here's a kindle store example:

```xml
<name xml:lang="en">Kindle Store</name>
<description xml:lang="en">Amazon Kindle Store</description>

<name xml:lang="de">Kindle-Shop</name>
<description xml:lang="de">Amazon Kindle-Shop</description>

<name xml:lang="ja">Kindle&#12473;&#12488;&#12450;</name>
<description xml:lang="ja">Amazon Kindle&#12473;&#12488;&#12450;</description>
```

This part serves to show waf name & description in kindle ui. It supports `xml:lang` for localisation & can use unicode characters as shown.

### The Payload

```xml
<content src="index.html" />
```

This tag tells mesquite which file to load when the waf is opened.

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

Disables/enables apis availible to the kindle object

### Messaging

```xml
<kindle:messaging>
    <kindle:app name="com.lab126.pillow" value="yes" />
    <kindle:app name="com.lab126.chromebar" value="yes" />
    <kindle:app name="com.lab126.readnow" value="yes" />
</kindle:messaging>
```

A whitelist for which lipc services can be called through `kindle.messaging`

### Conclusion 

That's some of the notable ones, with things like gestures & settings omitted. Please see [the wiki's](https://kindlemodding.org/wafs-and-mesquite/understanding-config-xml.html) config.xml analyzation.

## Limitations

Mesquite is based on a webkit version around safari 5.0 (webkit 533.16 according to mdn web docs) and so you can use html4 (some html5), css2 (some css3) and es5. You can use numerous tools to overcome this, see development tools section, but it's important to keep in mind you cannot use modern web features such as `const`, `let`, `() =>`, `promise()`, at least by default.

## Kindle Object

Every waf has an exposed `window.kindle` object which acts as a bridge between javascript and the kindle's system. Many apis are availible, which are configurable via the features section in a wafs `config.xml`.

In addition to all of the apis detailed here, `ismock: 1` and `extensions: {}` properties are exposed when loading the kindle store remote (the waf loads an iframe of a website) in a modern web browser.

The kindle store remote is detailed more in notes.

You can see simple documentation on [the wiki](https://kindlemodding.org/wafs-and-mesquite/the-kindle-object/), but it omits `bluetooth`, `winmgrutils`, `localprefs`, `bkgrnd`, `popup`, `uitest`, `download`, `appmgr` feature documentation mentioned in config xml.

I'll try to update the documentation at a later date because it can be quite confusing at times/nonsensical and lacks documentation of the features above mentioned.

## Dialogs

Dialogs are popups with messages shown to the user. They are handled by pillow, wrapping an instance of mesquite to show contents.

For example;

![kindle release notes dialog](https://media.goodereader.com/blog/uploads/images/2018/12/03043947/whatsnew_5_10_1_3.png.webp)

All system dialogs should be handled by pillow.

## Pillow

Pillow dialogs are stored in `usr/share/webkit-1.0/pillow` and have varying degrees of permissions.

Pillow dialogs can be elevated, able to run any lipc service with no config.xml whitelist (unlike regular wafs) through nativebridge, but sometimes they only have regular js or none at all - I am yet to find out where this is configured.

Pillow is said to begin being replaced by kpppillow (kindle++ pillow) with react native in 5.18.3.

(sad fact! `com.lab126.pillow` and `com.lab126.blanket` are not actually related...)

## NativeBridge

Nativebridge is like the kindle object in the sense it bridges mesquite to the kindle's system, but has elevated permissions, as mentioned above. There is no nativebridge documentation on the wiki, so here's the methods list taken from `usr/lib/libpillow.so` (with descriptions for the ones I know - though some are self-explanitory):

- devcapInitialize
- raiseChrome
- dismissChrome
- devcapGetInt
- devcapIsAvailable
- accessHasharrayProperty(lipcID, lipcEventType, {})
    - `kindle.messaging.sendMessage()` Without Whitelist?
- getIntLipcProperty(appID, propertyName)
    - Returns An Integer Property
- showDialog
- setLipcProperty(appID, propertyName, valueString)
    - Set Property With String
- logString
- setIntLipcProperty(appID, propertyName, valueInt)
    - Set Property With Integer
- getOrientationValue
- getDynamicConfigValue
- devcapGetString
- getAppId
- getStringLipcProperty(appID, propertyName)
    - Returns A String Property
- messagePillowCase
- setWindowTitle
- setWindowPosition
- getWindowPosition
- hideKb()
    - Hide The Keyboard
- showKb()
    - Show The Keyboard
- setAcceptFocus
- hideMe()
    - Set The Supplied Pillow Instance Invisible (Useful For `default_status_bar`)
- showMe()
    - Set The Supplied Pillow Instance Visible (Useful For `default_status_bar`)
- setWindowSize
- registerEventsWatchCallback(cb)
    - Set A Function That Will Be Called When A Registered Event Fires, First Argument Of Callback Gets Value
- subscribeToEvent(appID, eventID)
    - Add An Event That Triggers The Function Supplied To registerEventsWatchCallback()
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
    - Execute Command, Mainly From `/usr/share/webkit-1.0/pillow/debug_cmds.json`

Also see [pillowhelper](https://github.Com/paulfreund/weblaunch/blob/master/bin/pillowhelper.js)!

## Development Tools

There are many tools availible to assist you with app development. For example;

- [can mesquite use?](https://html-preview.github.io/?Url=https://github.Com/polish-penguin-dev/illusion/blob/main/mesquite/can-mesquite-use.html) - caniuse for mesquite. See if you can use certain web features
- [babeljs](https://babeljs.io/)/[swc](https://swc.rs/) - transpile modern js (e.g. es6) to es5 kindle-compatible js
- [polyfill](https://github.com/polish-penguin-dev/illusionchess/blob/master/illusionchess/js/polyfill.min.js) - allow you to use modern web apis on the kindle (not language features!). Best in combination with a transpiler
- [mesquito sdk](https://github.com/kindlemodding/mesquito/blob/main/mesquito/sdk.js) - although adapted for usage in the store waf, it contains useful scriptlets and functionality regarding chromebar, for example.
- [illusion](https://github.com/polish-penguin-dev/illusion) - a scriptlet which registers and launches wafs for you
- [screencontrol](https://github.com/polish-penguin-dev/illusion/blob/main/assets/screencontrol-binaries.tar.gz) - control your kindle from the web

## Exploits

Because mesquite is quite old (and being phased out of the ui with react native), it's been known to be susceptible to exploits. Here are some examples;

### Mesquito

[mesquito](https://github.Com/kindlemodding/mesquito/) itself is an exploit, replacing the contents of the kindle store waf through `.Active_content_sandbox` cache. It is semi-persistent, as the store constantly "Updates" and overwrites this cache.

This was patched in 5.18.1+ but if you make the cache extremely large (e.G. By pasting in filler files) or pasting and doing a hard reset some people mention cache deletion doesn't happen/is slower.

### Winterbreak

[winterbreak](https://github.Com/kindlemodding/winterbreak) is a more complex jailbreak using a couple exploits. It firstly utilises mesquito, to overwrite the store. It then sends a message to `com.Lab126.Pillow`, event id `customdialog`. In this method you would usually specify a `dialog.Html` file and it would display `/usr/share/webkit-1.0/pillow/ + [path]` (or something along those lines). But because of this, you could specify something like `../../../../../../mnt/us/dialog.Html` in the path and it could load a dialog with nativebridge access from `/mnt/us`, accessible unjailbroken. This is done to run `com.Lab126.Transfer` through lipc which runs `jb.Sh`. Nativebridge was necessary and `customdialog` because of the mesquite `sendmessage` whitelist in the store config xml which does not allow for `com.Lab126.Transfer`.

This `customdialog` exploit is patched in 5.18.1+.

### Languagebreak One-Click

[languagebreak one-click](https://github.Com/notmarek/languagebreak/tree/oneclick) is what winterbreak is based upon. It also uses nativebridge, `customdialog`, and mesquito, except it uses languagebreak method to run sh and not `com.Lab126.Transfer`. It is more experimental.

### Experimental Browser XSS (filemanager.Js)

On firmware versions below 5.16.4, the experimental browser uses mesquite and not chromium. It is essentially a waf with some scripts (including filemanager.Js) that turns it into a browser with a url bar, downloads functionality, etc...

When you download a file, it is handled by `filemanager.Js` which creates a dialog asking "Would you like to download [file name].[ext]?". You can place html elements in the download name, allowing for xss, and running a dialoger html. This pillow dialog does not have `nativebridge` access though.

It is patched in modern firmware.

### Kindledrip

I would recommend reading [this article](https://medium.Com/@baronyogev/kindledrip-from-your-kindles-email-address-to-using-your-credit-card-bb93dbfb2a08) on how kindledrip utilised send-to-kindle, mesquite, jpeg xr overflow, and more to achieve rce.

## UI Design

Ui design can be tricky on the kindle because scaling varies wildly between models, and css2 lacks modern features like grid, or flexbox.

I would recommend using `@media` queries and `%` values for optimal scaling between kindle models. Additionally, to center a `<div>` element, you can use tables:

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

## Points Of Reference

Here are some mesquite apps you can use as reference when building your own:

- [illusionchess](https://github.Com/polish-penguin-dev/illusionchess) - penguins184
- [kanki](https://github.Com/crizmo/kanki) - kurizu
- [kwordle](https://github.Com/crizmo/kwordle/) - kurizu
- [kindleforge](https://github.Com/polish-penguin-dev/kindleforge) - penguins184, kurizu, gingrspacecadet

## Notes

Here are some additional notes/info you may find useful.

### The Kindle Store Remote

In wafs, `kindle.dconfig` stores store-specific values.

`kindle.dconfig.getValue("url.store")` returns `https://www.amazon.co.uk/gp/digital/juno/index.html` (with varying tlds based on region). The store waf loads this url into an `iframe`.

You can access this now, but will get a (fake) `http 404` error unless your useragent abides by regex `\d-juno_\d`.

Oddly, appending query parameters `?clientId=ereader&apiVersion=1.0&deviceType=A21RY355YUXQAF` (device type shouldn't matter) you recieve; "Please update your Kindle software to connect to the Kindle store. Kindle software can be downloaded here: http://www.amazon.co.uk/KindleSoftwareUpdate".

Additionally, appending `?developer=1` query parameter the source code is not truncated and fully commented.

Previously it is said this prefills a mock kindle object if loaded in a modern web browser, but this does no longer happen. The object is still probably stored in kindle firmware.

### Utild

[utild](https://github.com/KindleModding/utild) is a simple lipc service you can use to just run `sh` instead of the `com.lab126.transfer` exploit. It works more reliably and can be installed & used in mesquite apps to run actual commands. See illusionutild for more.

## FAQ

### why can't I use `kindle.messaging.recievemessage()` with utild?

This happens because `utild` returns command value with `lipc-get-prop` whereas to use `recievemessage()` an event must be sent to the waf with `lipc-send-event`.

### Why does `com.lab126.transfer` not run sh?

`com.lab126.transfer` can only be ran with a valid internet connection.

### Does chromium have wafs?

With the introduction of chromium in 5.16.4 and slow replacement of mesquite, it seems reasonable - but chromium cannot act as a waf with kindle object.

## Conclusion

Thanks for reading my mesquite & wafs writeup!

Contact penguins184 on discord to suggest updates/ask any questions. Bye!
