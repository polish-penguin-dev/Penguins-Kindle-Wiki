# Mesquite & WAFs

This Page Contains All My Mesquite Knowledge, Findings & Tips. For A Complete Guide On Easy Apps, See [Illusion](/Illusion-Guide.md).

## Definitions

- WAFs - Web Application Frameworks. Web Apps With Additional Features
- Mesquite - A Private Amazon "Service" That Launches/Manages WAFs, Using WebKit.
- Mesquito - The E-Book Store WAF Exploit That Allows You To Modify Store Contents Unjailbroken.
- Pillow - Mesquite's "Glue" That Seemingly Handles Dialogs And Has Elevated Permissions Through NativeBridge.

Those Are The Main Ones - Mesquito And Mesquite Are NOT The Same. Mesquito Is A Pun On Mesquite.

## Registering An App

To Make A Functional App You Need A WAF, You Need To Copy It To `var/local/mesquite` And Register It To Appreg.DB (`var/local/appreg.db`) To Make It Launchable With `lipc`. See WAF Structure Below, To Register An App To Appreg You Can Use The Following SH Replacing Variables;

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

The `APP_ID` Variable Should Match The One Specified In The `config.xml` Within The WAF. 

You Can Then Launch It Like So; `lipc-set-prop com.lab126.appmgrd start app://$APP_ID`.

Please Note That `opt/var/local/mesquite` Is NOT Symlinked To `var/local/mesquite`!

## WAF Structure

```
/var/local/mesquite/WAF
|   index.html
|   styles.css
|   script.js
|   config.xml
```

That's An Example Structure You Could Use, Although Only `config.xml` And An `index.html` Entrypoint Is Necessary.

## Config XML

The Config.XML File Contains Parameters That Tell Mesquite How To Run Your WAF. Some Notable Values Include;

### The Header

```xml
<widget id="com.example.namespace" version="1.0" viewmodes="application" xmlns="http://www.w3.org/ns/widgets"
    xmlns:kindle="http://kindle.amazon.com/ns/widget-extensions">
```

The Widget ID Should Match The One You Register In Appreg. You Can Also Specify App Version. "Application" Is The Only Currently Known Viewmode.

### Name & Description

Here's A Kindle Store Example:

```xml
<name xml:lang="en">Kindle Store</name>
<description xml:lang="en">Amazon Kindle Store</description>

<name xml:lang="de">Kindle-Shop</name>
<description xml:lang="de">Amazon Kindle-Shop</description>

<name xml:lang="ja">Kindle&#12473;&#12488;&#12450;</name>
<description xml:lang="ja">Amazon Kindle&#12473;&#12488;&#12450;</description>
```

This Part Serves To Show WAF Name & Description In Kindle UI. It Supports `xml:lang` For Localisation & Can Use Unicode Characters As Shown.

### The Payload

```xml
<content src="index.html" />
```

This Tag Tells Mesquite Which File To Load When The WAF Is Opened.

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

Disables/Enables APIs Availible To The Kindle Object

### Messaging

```xml
<kindle:messaging>
    <kindle:app name="com.lab126.pillow" value="yes" />
    <kindle:app name="com.lab126.chromebar" value="yes" />
    <kindle:app name="com.lab126.readnow" value="yes" />
</kindle:messaging>
```

A Whitelist For Which LIPC Services Can Be Called Through `kindle.messaging`

### Conclusion 

That's Some Of The Notable Ones, With Things Like Gestures & Settings Omitted. Please See [The Wiki's](https://kindlemodding.org/wafs-and-mesquite/understanding-config-xml.html) Config.XML Analyzation.

## Limitations

Mesquite Is Based On A WebKit Version Around Safari 5.0 (Webkit 533.16 According To MDN Web Docs) And So You Can Use HTML4 (Some HTML5), CSS2 (Some CSS3) And ES5. You Can Use Numerous Tools To Overcome This, See Development Tools Section, But It's Important To Keep In Mind You Cannot Use Modern Web Features Such As `const`, `let`, `() =>`, `Promise()`, At Least By Default.

Mesquite & Java Are Also Being Phased Out Of The UI In Favour Of React Native.

## Kindle Object

Every WAF Has An Exposed `window.kindle` Object Which Acts As A Bridge Between JavaScript And The Kindle's System. Many APIs Are Availible, Which Are Configurable Via The Features Section In A WAFs `config.xml`.

In Addition To All Of The APIs Detailed Here, `isMock: 1` And `extensions: {}` Properties Are Exposed When Loading The Kindle Store Remote (The WAF Loads An Iframe Of A Website) In A Modern Web Browser.

The Kindle Store Remote Is Detailed More In Notes.

You Can See Simple Documentation On [The Wiki](https://kindlemodding.org/wafs-and-mesquite/the-kindle-object/), But It Omits `bluetooth`, `winmgrUtils`, `localprefs`, `bkgrnd`, `popup`, `uitest`, `download`, `appmgr` Feature Documentation Mentioned In Config XML.

I'll Try To Update The Documentation At A Later Date Because It Can Be Quite Confusing At Times/Nonsensical And Lacks Documentation Of The Features Above Mentioned.

## Dialogs

Dialogs Are Popups With Messages Shown To The User. They Are Handled By Pillow, Wrapping An Instance Of Mesquite To Show Contents.

For Example;

![Kindle Release Notes Dialog](https://media.goodereader.com/blog/uploads/images/2018/12/03043947/WhatsNew_5_10_1_3.png.webp)

All System Dialogs Should Be Handled By Pillow.

## Pillow

Pillow Dialogs Are Stored In `usr/share/webkit-1.0/pillow` And Have Varying Degrees Of Permissions.

Pillow Dialogs Can Be Elevated, Able To Run Any LIPC Service With No Config.XML Whitelist (Unlike Regular WAFs) Through NativeBridge, But Sometimes They Only Have Regular JS Or None At All - I Am Yet To Find Out Where This Is Configured.

Pillow Is Said To Begin Being Replaced By KPPPillow (Kindle++ Pillow) With React Native In 5.18.3.

(Sad Fact! `com.lab126.pillow` And `com.lab126.blanket` Are Not Actually Related...)

## NativeBridge

NativeBridge Is Like The Kindle Object In The Sense It Bridges Mesquite To The Kindle's System, But Has Elevated Permissions, As Mentioned Above. There Is No NativeBridge Documentation On The Wiki, So Here's The Methods List Taken From `usr/lib/libpillow.so` (With Descriptions For The Ones I Know - Though Some Are Self-Explanitory):

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

Also See [PillowHelper](https://github.com/PaulFreund/WebLaunch/blob/master/bin/pillowHelper.js)!

## Development Tools

There Are Many Tools Availible To Assist You With App Development. For Example;

- [Can Mesquite Use?](https://html-preview.github.io/?url=https://github.com/polish-penguin-dev/Illusion/blob/main/Mesquite/Can-Mesquite-Use.html) - CanIUse For Mesquite. See If You Can Use Certain Web Features
- [BabelJS](https://babeljs.io/)/[SWC](https://swc.rs/) - Transpile Modern JS (E.g. ES6) To ES5 Kindle-Compatible JS
- [Polyfill](https://github.com/polish-penguin-dev/IllusionChess/blob/master/illusionChess/js/polyfill.min.js) - Allow You To Use Modern Web APIs On The Kindle (NOT Language Features!). Best In Combination With A Transpiler
- [Mesquito SDK](https://github.com/KindleModding/Mesquito/blob/main/mesquito/sdk.js) - Although Adapted For Usage In The Store WAF, It Contains Useful Scriptlets And Functionality Regaring Chromebar, For Example.
- [Illusion](https://github.com/polish-penguin-dev/Illusion) - A Scriptlet Which Registers And Launches WAFs For You
- [ScreenControl](https://github.com/polish-penguin-dev/Illusion/blob/main/Assets/screencontrol-binaries.tar.gz) - Control Your Kindle From The Web

## Exploits

Because Mesquite Is Quite Old (And Being Phased Out Of The UI With React Native), It's Been Known To Be Susceptible To Exploits. Here Are Some Examples;

### Mesquito

[Mesquito](https://github.com/KindleModding/Mesquito/) Itself Is An Exploit, Replacing The Contents Of The Kindle Store WAF Through `.active_content_sandbox` Cache. It Is Semi-Persistent, As The Store Constantly "Updates" And Overwrites This Cache.

This Was Patched In 5.18.1+ But If You Make The Cache Extremely Large (E.g. By Pasting In Filler Files) Or Pasting And Doing A Hard Reset Some People Mention Cache Deletion Doesn't Happen/Is Slower.

### WinterBreak

[WinterBreak](https://github.com/KindleModding/WinterBreak) Is A More Complex JailBreak Using A Couple Exploits. It Firstly Utilises Mesquito, To Overwrite The Store. It Then Sends A Message To `com.lab126.pillow`, Event ID `customDialog`. In This Method You Would Usually Specify A `dialog.html` File And It Would Display `/usr/share/webkit-1.0/pillow/ + [PATH]` (Or Something Along Those Lines). But Because Of This, You Could Specify Something Like `../../../../../../mnt/us/dialog.html` In The Path And It Could Load A Dialog With NativeBridge Access From `/mnt/us`, Accessible Unjailbroken. This Is Done To Run `com.lab126.transfer` Through LIPC Which Runs `jb.sh`. NativeBridge Was Necessary And `customDialog` Because Of The Mesquite `sendMessage` Whitelist In The Store Config XML Which Does Not Allow For `com.lab126.transfer`.

This `customDialog` Exploit Is Patched In 5.18.1+.

### LanguageBreak One-Click

[LanguageBreak One-Click](https://github.com/notmarek/LanguageBreak/tree/oneclick) Is What WinterBreak Is Based Upon. It Also Uses NativeBridge, `customDialog`, and Mesquito, Except It Uses LanguageBreak Method To Run SH And Not `com.lab126.transfer`. It Is More Experimental.

### Experimental Browser XSS (FileManager.JS)

On Firmware Versions Below 5.16.4, The Experimental Browser Uses Mesquite And Not Chromium. It Is Essentially A WAF With Some Scripts (Including FileManager.JS) That Turns It Into A Browser With A URL Bar, Downloads Functionality, Etc...

When You Download A File, It Is Handled By `FileManager.JS` Which Creates A Dialog Asking "Would You Like To Download [File Name].[Ext]?". You Can Place HTML Elements In The Download Name, Allowing For XSS, And Running A Dialoger HTML. This Pillow Dialog Does Not Have `nativeBridge` Access Though.

It Is Patched In Modern Firmware.

### KindleDrip

I Would Recommend Reading [This Article](https://medium.com/@baronyogev/kindledrip-from-your-kindles-email-address-to-using-your-credit-card-bb93dbfb2a08) On How KindleDrip Utilised Send-To-Kindle, Mesquite, JPEG XR Overflow, And More To Achieve RCE.

## UI Design

UI Design Can Be Tricky On The Kindle Because Scaling Varies Wildly Between Models, And CSS2 Lacks Modern Features Like Grid, Or FlexBox.

I Would Recommend Using `@media` Queries And `%` Values For Optimal Scaling Between Kindle Models. Additionally, To Center A `<div>` Element, You Can Use Tables:

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

Here Are Some Mesquite Apps You Can Use As Reference When Building Your Own:

- [IllusionChess](https://github.com/polish-penguin-dev/IllusionChess) - Penguins184
- [KAnki](https://github.com/crizmo/KAnki) - Kurizu
- [KWordle](https://github.com/crizmo/KWordle/) - Kurizu
- [KindleForge](https://github.com/polish-penguin-dev/KindleForge) - Penguins184, Kurizu, Gingrspacecadet

## Notes

Here Are Some Additional Notes/Info You May Find Useful.

### The Kindle Store Remote

In WAFs, `kindle.dconfig` Stores Store-Specific Values.

`kindle.dconfig.getValue("url.store")` Returns `https://www.amazon.co.uk/gp/digital/juno/index.html` (With Varying TLDs Based On Region). The Store WAF Loads This URL Into An `iframe`.

You Can Access This Now, But Will Get A (Fake) `HTTP 404` Error Unless Your UserAgent Abides By RegEx `\d-juno_\d`.

Oddly, Appending Query Parameters `?clientId=ereader&apiVersion=1.0&deviceType=A21RY355YUXQAF` (Device Type Shouldn't Matter) You Recieve; "Please update your Kindle software to connect to the Kindle store. Kindle software can be downloaded here: http://www.amazon.co.uk/KindleSoftwareUpdate".

Additionally, Appending `?developer=1` Query Parameter The Source Code Is Not Truncated And Fully Commented.

Previously It Is Said This Prefills A Mock Kindle Object If Loaded In A Modern Web Browser, But This Does No Longer Happen. The Object Is Still Probably Stored In Kindle Firmware.

### Utild

[Utild](https://github.com/KindleModding/utild) Is A Simple LIPC Service You Can Use To Just Run `SH` Instead Of The `com.lab126.transfer` Exploit. It Works More Reliably And Can Be Installed & Used In Mesquite Apps To Run Actual Commands. See IllusionUtild For More.

## FAQ

### Why Can't I Use `kindle.messaging.recieveMessage()` With Utild?

This Happens Because `UTILD` Returns Command Value With `lipc-get-prop` Whereas To Use `recieveMessage()` An Event Must Be Sent To The WAF With `lipc-send-event`.

### Why Does `com.lab126.transfer` Not Run SH?

`com.lab126.transfer` Can Only Be Ran With A Valid Internet Connection.

### Does Chromium Have WAFs?

With The Introduction Of Chromium In 5.16.4 And Slow Replacement Of Mesquite, It Seems Reasonable - But Chromium Cannot Act As A WAF With Kindle Object.

## Conclusion

Thanks For Reading My Mesquite & WAFs Writeup!

Contact Penguins184 On Discord To Suggest Updates/Ask Any Questions. Bye!