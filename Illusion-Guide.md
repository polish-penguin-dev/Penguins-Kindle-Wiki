# Illusion Guide

> [!NOTE]
> This Page Contains The (Slightly Modified) "Getting Started" Section Of The Old Wiki. It Teaches The Basics Of Making An IllusionGeneric Application.

# Getting Started

This Section Contains All You Need To Know About Creating Your First App.

## Contents

- Prerequisites
- Making An App
- Utilities & Helpers
- Config.XML
- Illusion Generic Scriptlet
- FAQ

> [!IMPORTANT]  
> You Need Prior HTML, CSS, And JS Knowledge To Continue.

# Prerequisites

- A Jailbroken Kindle That Supports Scriptlets*
- HTML, CSS, JS Knowledge
- Patience, Common Sense

*Illusion Acts As A Scriptlet By Default. This Can Be Modified Into A KUAL Application If You Have Technical Knowledge.

# Making Your App

> [!WARNING]  
> Kindles Support ES5 And Semi-Modern HTML5, CSS3.

To Begin, All You Need To Do Is Make A New Folder With A HTML, CSS, And JS File. For Example,

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
        <p id="status">JavaScript Doesn't Work</p>
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
    document.getElementById("status").innerHTML = "JavaScript Works!";
});
```

This Is Your Basic App. You Can Add More Logic Later.

# Utilities & Helpers

> [!TIP]
> Always Try To Import JavaScript Locally, Rather Than Using An External Sevice (E.g. JSDelivr) For Reliability & Usage Offline.

There Are Many Different Tools You Can Make Use Of - Or Should Make Use Of - In, Or When Testing Your Application:

- [BabelJS](https://babeljs.io/)/[SWC](https://swc.rs/); JavaScript Transpiler (E.g. ES9 -> ES5, Kindle Compatible)
- [Polyfill](/Assets/polyfill.min.js); Allows You To Use Semi-Modern JavaScript Features
- [ScreenControl](/Assets/screencontrol-binaries.tar.gz) (Lab126); Control Your Kindle From The Web Scriptlet (Endpoint `http://192.168.X.X:6789/screen`)
- [Mesquito SDK](/Assets/mesquito-sdk.js); Helper Functions
- [CanIUse](https://caniuse.com/); Can I Use This Web Feature? (Kindle's Browser Is Approx. Safari 5/WebKit 533.16)

For Support, Direct Message `penguins184` (That's Me!) On Discord, Or Ask Someone In The [Discord](https://discord.gg/kindle) That's Smart.

# Config.XML

Back To Your Project, Create A Config.XML File In The Same Folder As Your HTML, CSS, JS.

`hello/config.xml`:
```xml
<?xml version="1.0"?>
<widget id="com.you.hello" version="1.0" viewmodes="application" xmlns="http://www.w3.org/ns/widgets"
    xmlns:kindle="http://kindle.amazon.com/ns/widget-extensions">
    <name xml:lang="en">Hello Illusion!</name>
    <description xml:lang="en">An Illusion Showcase Application</description>

    <content src="index.html" />

    <!-- Permissions & Settings Standard From Kindle Store. -->
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

    <!-- Removed JQuery, Non-Used JS -->
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

This Isn't Going To Get Into Details - For That, Check The [KindleModding](https://kindlemodding.org/wafs-and-mesquite/understanding-config-xml.html) Wiki.

You Only Need To Change The Top Part. Fields That You Could Change Are Marked "MODIFY";
```xml
<?xml version="1.0"?>
<widget id="MODIFY (1)" version="MODIFY (2)" viewmodes="application" xmlns="http://www.w3.org/ns/widgets"
    xmlns:kindle="http://kindle.amazon.com/ns/widget-extensions">
    <name xml:lang="en">MODIFY (3)</name>
    <description xml:lang="en">MODIFY (4)</description>

    <content src="MODIFY (5)" />
    ... (Everything Else, Keep The Same)
```

1. The Namespace - For Example, `xyz.penguins184.myapp`. This Doesn't Have To Match A Real Domain; You Could Do `com.your_username.appname`.
2. Version - Anything You Want, Or Just 1.0
3. App Name (English)
4. App Description (English)
5. Entrypoint (Default, "index.html")

# Adding Illusion

Almost There! To Make Your App Executable, You Could Add Illusion.

1. Download [Illusion Generic Scriptlet](/Scripts/IllusionGeneric.sh)
2. Modify Where Indicated;
```sh
#!/bin/sh
# Name: MODIFY (1)
# Author: MODIFY (2)
# DontUseFBInk

#Modify Below!
SOURCE_DIR="/mnt/us/documents/MODIFY (3)"
TARGET_DIR="/var/local/mesquite/MODIFY (4)"
DB="/var/local/appreg.db" #KEEP THIS!
APP_ID="MODIFY (5)"

#DO NOT MODIFY BELOW

#Copy To VAR/LOCAL/MESQUITE
if [ -d "$SOURCE_DIR" ]; then
    if [ -d "$TARGET_DIR" ]; then
        rm -rf "$TARGET_DIR"
    fi
    cp -r "$SOURCE_DIR" "$TARGET_DIR"
else
    exit 1
fi

#Redeclare To APPREG.DB 
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

echo Registered $APP_ID, You May Now Launch It With LIPC.
sleep 2

#Launch With LIPC
nohup lipc-set-prop com.lab126.appmgrd start app://$APP_ID >/dev/null 2>&1 &
```

(Modifiers)
1. Name Of Scriptlet, Shows Up In Library
2. Author Of Scriptlet, Shows Up In Library
3. Name Of The Folder In Which You Are Developing Your App
4. Name Of The Folder In Which You Are Developing Your App
5. Same Namespace As Mandated In Config.XML

## You're Done!

- Distribute With `appName.sh` And `appName/` Folder!
- To Install, Drag Both To The `documents/` Folder On Your Kindle!
- Updating? Simply Drag & Drop, Replacing The Current Folder And/Or Scriptlet. NOTE; You Have To Reboot, Reload [The Page], Or Kill The Mesquite Process (By Switching Apps; Or Other).
- :>

# Frequently Asked Questions

## Q: What Is Mesquite?

A: Mesquite Is A Private Amazon 'Service' Used On The Kindle To Launch WAFs (Web Application Frameworks).

## Q: Different Scaling On Different Models?

A: This Is A Known - And Quite Annoying - Issue. You Can Create 2 Versions Of Your App, Not Use Static `px` Height/Width (Note; `vh`, `vw` Are Unsupported), Or Use `@media` Queries (Recommended!)

## Q: Why Is Mesquite Still Running In The Background?

A: Mesquite Does Not Terminate/Cleanup Until Another Instance Of It Is Opened. This Is Standard Amazon Behaviour, And Is Why You Need To Restart To See Changes Reflected In Your App - Or Safely Kill The Process. 

## Q: Why Can't I... Center A Div?

A: Use Tables! CSS2 Doesn't Support Flexbox, Grid, Or Even The Old `position: absolute;` Method! Here's An Example:

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

## Q: Why Can't I Load Characters From Different Alphabets On Elder Kindles/Mojibake?

A: Don't Know, But Can Be Mitigated With ANSI Escape Codes/Per-Language Font As Done In [KAnki](https://github.com/crizmo/KAnki/tree/main).

## Q: Why Does Illusion Exist?

A: Easy Apps.

## Q: Fetch()?

A: Look At XMLHttpRequest()

## Q: Why Is My JS Not Loading?

A: Most Likely, ES6 Features (Try Babel + Polyfill), Or Incorrect Path.

## Q: Why Hasn't My App Updated?

A: Reload, Then Try Again! (Add Button With `window.location.reload();`, Spam It, Exit In & Out, Spam It, It Will Update!) If Not, Restart Kindle Or Switch Mesquite Apps (Mesquite Process Gets Killed On App Switch)

## Q: WebKit Version/Safari Version?

A: Approx. Safari 5, 5.1 Launching With WebKit 533.16 According To [MDN](https://github.com/mdn/browser-compat-data/blob/main/browsers/safari.json).

## Q: [What Are Some] Common CSS/JS Methods/Styles That Are Unsupported?

A: Fetch(), Flexbox, Arrow Functions, Const/Let, CSS Variables, ...

## Q: Run SH?

Look At [Utild](https://github.com/KindleModding/utild) And [KindleForge](https://github.com/polish-penguin-dev/KindleForge) Which Is An IllusionUtild Application. Also Consider Reading The Mesquite & WAFs Writeup. Calling `com.lab126.transfer` Is Also Possible But Unreliable.

## Q: Found Something Out? How Do I Add My Own Helpful Q&A?

A: DM Penguins184 On Discord!
