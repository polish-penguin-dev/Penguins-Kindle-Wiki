# Illusion Guide

> [!NOTE]
> This page contains the (slightly modified) "Getting Started" section of the old wiki. It teaches the basics of making an IllusionGeneric application.

# Getting started

This section contains all you need to know about creating your first app.

## Contents

- Prerequisites
- Making an app
- Utilities & helpers
- Config.XML
- Illusion Generic scriptlet
- FAQ

> [!IMPORTANT]\
> You need prior HTML, CSS, and JS knowledge to continue.

# Prerequisites

- A jailbroken Kindle that supports scriptlets\*
- HTML, CSS, JS knowledge
- Patience, common sense

\*Illusion acts as a scriptlet by default. This can be modified into a KUAL application if you have technical knowledge.

# Making your app

> [!WARNING]\
> Kindles support ES5 and semi-modern HTML5, CSS3.

To begin, all you need to do is make a new folder with an HTML, CSS, and JS file. For example,

`hello/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Hello Illusion!</title>
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
    <link rel="stylesheet" type="text/css" media="screen" href="style.css">
    <script src="script.js"></script>
</head>
<body>
    <div class="container">
        <h1>Illusion</h1>
        <p id="status">JavaScript doesn't work</p>
    </div>
</body>
</html>
```

`hello/style.css`:

```css
.container {
    text-align: center;
};

h1, p {
    font-family: Arial, Helvetica, sans-serif;
};
```

`hello/script.js`:

```js
document.addEventListener("DOMContentLoaded", function() {
    document.getElementById("status").innerHTML = "JavaScript works!";
});
```

This is your basic app. You can add more logic later.

# Utilities & helpers

> [!TIP] Always try to import JavaScript locally, rather than using an external service (e.g. JSDelivr) for reliability & usage offline.

There are many different tools you can make use of — or should make use of — in, or when testing your application:

- [BabelJS](https://babeljs.io/)/[SWC](https://swc.rs/); JavaScript transpiler (e.g. ES9 → ES5, Kindle compatible)
- [Polyfill](/Assets/polyfill.min.js); allows you to use semi-modern JavaScript features
- [ScreenControl](/Assets/screencontrol-binaries.tar.gz) (Lab126); control your Kindle from the web scriptlet (endpoint `http://192.168.X.X:6789/screen`)
- [Mesquito SDK](/Assets/mesquito-sdk.js); helper functions
- [CanIUse](https://caniuse.com/); can I use this web feature? (Kindle's browser is approx. Safari 5/WebKit 533.16)

For support, DM `penguins184` (that's me!) on Discord, or ask someone in the [Discord](https://discord.gg/kindle) that's smart.

# Config.XML

Back to your project, create a Config.XML file in the same folder as your HTML, CSS, JS.

`hello/config.xml`:

```xml
<?xml version="1.0"?>
<widget id="com.you.hello" version="1.0" viewmodes="application" xmlns="http://www.w3.org/ns/widgets"
    xmlns:kindle="http://kindle.amazon.com/ns/widget-extensions">
    <name xml:lang="en">Hello Illusion!</name>
    <description xml:lang="en">An Illusion Showcase Application</description>

    <content src="index.html" />

    <!-- Permissions & settings standard from Kindle Store. -->
    <kindle:permissions>
        <kindle:permission name="local-port-access" />
    </kindle:permissions>
    <kindle:network>
        <kindle:asset key="user-agent" value="kindle://device-type" />
        <kindle:asset key="user-agent" value="kindle://sw-version" />
        <kindle:asset key="user-agent" value="kindle://pretty-sw-version" />
        <kindle:asset key="http-header" value="kindle://transport-method" />
        <kindle:asset key="http-header" value="kindle://country-code" />
        <kindle:asset key="initialDNS" value="false" />
        <kindle:asset key="maxConnections" value="6" />
        <kindle:asset key="maxConnectionsPerHost" value="2" />
        <kindle:asset key="maxConnectionsPerProxy" value="6" />
        <kindle:asset key="overrideProxy" value="none" />
        <kindle:asset key="enableCaching" value="false" />
    </kindle:network>

    <kindle:cookiejar>
        <kindle:asset key="persistent" value="true" />
        <kindle:asset key="usePrivateCookies" value="false" />
        <kindle:asset key="useDeviceCookies" value="true" />
        <kindle:asset key="useAccessToken" value="true" />
    </kindle:cookiejar>

    <kindle:chrome>
        <kindle:asset key="configureSearchBar" value="system" />
    </kindle:chrome>

    <kindle:gestures>
        <kindle:param name="tap" value="yes" properties="fire_on_tap:1 max_updown_delta:0" />
        <kindle:param name="swipe" value="yes" />
    </kindle:gestures>

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

    <kindle:messaging>
        <kindle:app name="com.lab126.pillow" value="yes" />
        <kindle:app name="com.lab126.chromebar" value="yes" />
        <kindle:app name="com.lab126.readnow" value="yes" />
    </kindle:messaging>

    <!-- Removed jQuery, non-used JS -->
    <kindle:resources>
        <kindle:asset key="AllowHTTPSApplicationManifestCrossDomain" value="true" />
        <kindle:asset key="ApplicationCachePath" value="/var/local/mesquite/devkit/resource/appcache" />
        <kindle:asset key="ApplicationCacheLoadDelay" value="6.0" />
        <kindle:asset key="LocalStorageQuota" value="26214400" />
    </kindle:resources>

    <kindle:settings>
        <kindle:setting name="internetRequired" value="yes" />
        <kindle:setting name="saveContext" value="no" />
        <kindle:setting name="disable-wua-features" value="yes" />
    </kindle:settings>
</widget>
```

This isn't going to get into details — for that, check the [KindleModding](https://kindlemodding.org/wafs-and-mesquite/understanding-config-xml.html) wiki.

You only need to change the top part. Fields that you could change are marked "MODIFY";

```xml
<?xml version="1.0"?>
<widget id="MODIFY (1)" version="MODIFY (2)" viewmodes="application" xmlns="http://www.w3.org/ns/widgets"
    xmlns:kindle="http://kindle.amazon.com/ns/widget-extensions">
    <name xml:lang="en">MODIFY (3)</name>
    <description xml:lang="en">MODIFY (4)</description>

    <content src="MODIFY (5)" />
    ... (everything else, keep the same)
```

1. The namespace – for example, `xyz.penguins184.myapp`. This doesn't have to match a real domain; you could do `com.your_username.appname`.
2. Version – anything you want, or just 1.0.
3. App name (English).
4. App description (English).
5. Entrypoint (default, "index.html").

# Adding Illusion

Almost there! To make your app executable, you could add Illusion.

1. Download [Illusion Generic scriptlet](/Scripts/IllusionGeneric.sh)
2. Modify where indicated;

```sh
#!/bin/sh
# Name: MODIFY (1)
# Author: MODIFY (2)
# DontUseFBInk

#Modify below!
SOURCE_DIR="/mnt/us/documents/MODIFY (3)"
TARGET_DIR="/var/local/mesquite/MODIFY (4)"
DB="/var/local/appreg.db" #KEEP THIS!
APP_ID="MODIFY (5)"

#DO NOT MODIFY BELOW

#Copy to var/local/mesquite
if [ -d "$SOURCE_DIR" ]; then
    if [ -d "$TARGET_DIR" ]; then
        rm -rf "$TARGET_DIR"
    fi
    cp -r "$SOURCE_DIR" "$TARGET_DIR"
else
    exit 1
fi

#Redeclare to appreg.db
sqlite3 "$DB" <<EOF
INSERT OR IGNORE INTO interfaces(interface) VALUES('application');

INSERT OR IGNORE INTO handlerIds(handlerId) VALUES('$APP_ID');

INSERT OR REPLACE INTO properties(handlerId,name,value)
  VALUES('$APP_ID','lipcId','$APP_ID');
INSERT OR REPLACE INTO properties(handlerId,name,value)
  VALUES('$APP_ID','command','/usr/bin/mesquite -l $APP_ID -c file://$TARGET_DIR/');
INSERT OR REPLACE INTO properties(handlerId,name,value)
  VALUES('$APP_ID','supportedOrientation','U');
EOF

echo Registered $APP_ID, you may now launch it with LIPC.
sleep 2

#Launch with LIPC
nohup lipc-set-prop com.lab126.appmgrd start app://$APP_ID >/dev/null 2>&1 &
```

(Modifiers)

1. Name of scriptlet, shows up in library.
2. Author of scriptlet, shows up in library.
3. Name of the folder in which you are developing your app.
4. Name of the folder in which you are developing your app.
5. Same namespace as mandated in Config.XML.

## You're done!

- Distribute with `appName.sh` and `appName/` folder!
- To install, drag both to the `documents/` folder on your Kindle!
- Updating? Simply drag & drop, replacing the current folder and/or scriptlet. NOTE: You have to reboot, reload [the page], or kill the Mesquite process (by switching apps; or other).
- :>

# Frequently asked questions

## Q: What is Mesquite?

A: Mesquite is a private Amazon 'service' used on the Kindle to launch WAFs (Web Application Frameworks).

## Q: Different scaling on different models?

A: This is a known — and quite annoying — issue. You can create two versions of your app, not use static `px` height/width (note: `vh`, `vw` are unsupported), or use `@media` queries (recommended!).

## Q: Why is Mesquite still running in the background?

A: Mesquite does not terminate/cleanup until another instance of it is opened. This is standard Amazon behaviour, and is why you need to restart to see changes reflected in your app — or safely kill the process.

## Q: Why can't I... center a div?

A: Use tables! CSS2 doesn't support Flexbox, Grid, or even the old `position: absolute;` method! Here's an example:

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

## Q: Why can't I load characters from different alphabets on older Kindles/mojibake?

A: I don't know, but it can be mitigated with ANSI escape codes/per-language font as done in [KAnki](https://github.com/crizmo/KAnki/tree/main).

## Q: Why does Illusion exist?

A: Easy apps.

## Q: Fetch()?

A: Look at XMLHttpRequest().

## Q: Why is my JS not loading?

A: Most likely, ES6 features (try Babel + Polyfill), or incorrect path.

## Q: Why hasn't my app updated?

A: Reload, then try again! (Add a button with `window.location.reload();`, spam it, exit in & out, spam it, it will update!) If not, restart Kindle or switch Mesquite apps (Mesquite process gets killed on app switch).

## Q: WebKit version/Safari version?

A: Approx. Safari 5, 5.1 launching with WebKit 533.16 according to [MDN](https://github.com/mdn/browser-compat-data/blob/main/browsers/safari.json).

## Q: [What are some] common CSS/JS methods/styles that are unsupported?

A: Fetch(), Flexbox, arrow functions, const/let, CSS variables, ...

## Q: Run sh?

Look at [Utild](https://github.com/KindleModding/utild) and [KindleForge](https://github.com/polish-penguin-dev/KindleForge) which is an IllusionUtild application. Also consider reading the Mesquite & WAFs writeup. Calling `com.lab126.transfer` is also possible but unreliable.

## Q: Found something out? How do I add my own helpful Q&A?

A: DM Penguins184 on Discord!
