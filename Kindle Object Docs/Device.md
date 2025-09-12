# Kindle Object Documentation

## Device (Dev)

- Device information & modifiers. 
- Properties:

| Property Name           | Value                       | Description                                                                   |
|-------------------------|-----------------------------|-------------------------------------------------------------------------------|
| `contactInfo`           | 'Your System Administrator' | 👀                                                                             |
| `eid`                   | [BASE64 Encoded Data]       | Purpose unknown!                                                              |
| `getRegistrationState`  | 'registered'                | Checks if kindle is registered                                                |
| `hasScreenLight`        | `true`                      | Is paperwhite?                                                                |
| `hasKindleStoreAccess`  | `true`                      | If the kindle has kindle store access? Possibly related to parental controls. |
| `isWirelessMenuEnabled` | `true`                      | 'Wireless Menu' enabled?                                                      |
| `controlStatus`         | 3                           | Possible values: `1`: Device control, `2`: Parental control,`3`: No control.  |

- Functions:

- `kindle.device.setSensitivity(useThreshold, threshold)`
- Description: Modifies 'sensitivity' of the e-ink display. 100 will cause the screen to refresh much more, 0 much less in comparison.
- Parameters:

| Property Name | Data Type | Description                                                          |
|---------------|-----------|----------------------------------------------------------------------|
| threshold     | integer   | A 0-100 range to modify how often the kindle's display refreshes     |
| useThreshold  | boolean   | Whether to refresh like normal or use the modified 'threshold' value |

- `kindle.device.setOrientation(orientation)`
- Description: Sets the screen orientation according to the provided string. A list of valid strings can be found in the [Enums]() section.

- `kindle.device.getDPI()`
- Description: Returns device DPI

- `kindle.device.hasWirelessMenu()`
- Description: Returns a boolean based on whether or not the kindle has access to a wireless menu? Perhaps related to `kindle.device.isWirelessMenuEnabled`.

- `kindle.device.log(logService, logMsg, logLevel)`
- Description: Add something to the kindle's log. **These logs are sent to amazon!**
- Parameters:

| Property Name | Data Type | Description                                                    |
|---------------|-----------|----------------------------------------------------------------|
| logService    | String    | The name of the service which produced the log, e.g. `wafjs`.  |
| logMsg        | String    | The message to be logged                                       |
| logLevel      | String    | The log level, either `info`, `warn`, `error`, `debug`, `perf` |

- `kindle.device.getLab126SessionToken()`
- Description: Returns the session token needed to access the internal DB API 'TBA'

- `kindle.device.loadResource(frameId, resourceId)`
- Description: Loads a local JavaScript file into any frame within the WAF, bypassing XSS security. Must be specified in the `config.xml`.
- Parameters:

| Property Name | Data Type | Description                                                                                                                                |
|---------------|-----------|--------------------------------------------------------------------------------------------------------------------------------------------|
| frameId       | String    | The `id` of the frame in the DOM in which the file will be loaded. You may specify the top-level frame of the application through `_self`. |
| resourceId    | String    | The identifier of the JS file as specified in the `config.xml`, must reference a valid filesystem file.                                    |

- `kindle.device.addDomainToWhitelist(uri, addSubDomains)`
- Description: What it does is unknown, adds a URI to the allowed domain list?
- Parameters:

| Property Name | Data Type | Description                                                                                                |
|---------------|-----------|------------------------------------------------------------------------------------------------------------|
| uri           | String    | The `uri` to add, e.g. `http://amazon.com`.                                                                |
| addSubDomains | String    | Either `'true'` or `'false'`, dictating whether or not the subdomains of said uri should be added as well. |

- `kindle.device.clearCookies()`
- Description: Clear the application's cookie jar

- `kindle.device.clearApplicationCache()`
- Description: Clear the application's HTML cache

- `kindle.device.clearCache()`
- Description: Clear any resources cached by the application in memory

- `kindle.device.getMPDomain()`
- Description: Get the marketplace (...store?) domain.

- `kindle.device.getBaiduSearchURL()`
- Description: Returns the beginning of a baidu (Chinese search engine) URL?
- Value: `https://www.baidu.com/s?tn=baiduhome_pg&ie=utf-8&rn=4&wd=`

- `kindle.device.disableSecureApis()`
- Description: Used in the `payment` WAF, disables "Secure APIs" on non-Amazon URLs

- `kindle.device.isInDemoMode()`
- Description: Returns a boolean to see if the device is currently in demo mode.

- `kindle.device.getSoftwareVersionNumber()`
- Description: Returns an integer representing the kindle's software version number.

- `kindle.device.getSoftwareVersionString()`
- Description: Returns a string showing the kindle's software version (e.g. `5.16.2.1.1`)

- `kindle.device.getASRMode()`
- Description: Unknown. Returned `0` when tested.

- `kindle.device.getCSSPixelsPerInch()`
- Description: Returns the number of 'CSS Pixels Per Inch'.