# Kindle Object Documentation

## Enums

Strings returned on/used as input in certain API functions. Taken from `/opt/var/local/mesquite` sqsh. Mostly 1:1 from wiki, I have not explored this myself.

### connectionPromptLevels

| Value     | Description                                                                        |
|-----------|------------------------------------------------------------------------------------|
| all       | Turn on all user prompting. Everything needed to make a connection will be used.   |
| never     | Turn off all user prompting. A connection will be made *automatically* if possible |
| nocaptive | Turn on user prompting for everything but a captive portal situation               |

### connectionResults

| Value                  | Description                                                |
|------------------------|------------------------------------------------------------|
| success                | The connection attempt succeeded                           |
| failure-user-canceled  | The user canceled the connection request                   |
| failure                | There was a failure when connecting                        |
| failure-captive-portal | There was a captive-portal related failure when connecting |

### scrollBarStates

| Value   | Description                       |
|---------|-----------------------------------|
| auto    | Automatically hide/show scrollbar |
| hidden  | Hide the scrollbar                |
| visible | Show the scrollbar                |

### fileDownloadResults

| Value    | Description                                |
|----------|--------------------------------------------|
| success  | Succeeded in downloading the file          |
| error    | There was an error downloading the file    |
| canceled | The file download was canceled by the user |
| rejected | The file type is not downloadable          |

### eInkRefreshModes

| Value   | Description                                                         |
|---------|---------------------------------------------------------------------|
| auto    | Automatically set the refresh mode                                  |
| minimal | Do not refresh the display often                                    |
| maximal | Refresh the display often                                           |
| manual  | Do not refresh the display at all unless directly called by JS/Code |

### connectionTypes

| Value | Description                                                      |
|-------|------------------------------------------------------------------|
| wifi  | Kindle is connected via WiFi                                     |
| wan   | Kindle is connected via 3G                                       |
| none  | No interfaces are currently active on the Kindle (airplane mode) |

### orientations

| Value          | Description                                 |
|----------------|---------------------------------------------|
| auto           | Set orientation automatically               |
| portrait       | Set **portrait** orientation automatically  |
| portraitUp     | Portrait orientation                        |
| portraitDown   | Portrait orientation (upside down)          |
| landscape      | Set **landscape** orientation automatically |
| landscapeLeft  | Landscape orientation (clockwise)           |
| landscapeRight | Landscape orientation (anticlockwise)       |

### direction

| Value | Description      |
|-------|------------------|
| up    | Self-explanitory |
| down  | ...              |
| left  | ...              |
| right | ...              |

### alignment

| Value  | Description                                                                                  |
|--------|----------------------------------------------------------------------------------------------|
| top    | Self-explanitory                                                                             |
| buttom | Bottom? may be misspelling from old wiki, try 'bottom', or it's just a firmware misspelling. |
| center | ...                                                                                          |
| left   | ...                                                                                          |
| right  | ...                                                                                          |

### pillow.buttons (chromebar)

| Value     | Description                                 |
|-----------|---------------------------------------------|
| back      | Self-explanitory                            |
| store     | ...                                         |
| home      | ...                                         |
| forward   | ...                                         |
| menu      | ...                                         |
| refresh   | ...                                         |
| cancel    | ...                                         |
| discovery | ...                                         |
| KPP_BACK  | ...                                         |
| KPP_CLOSE | ...                                         |

### pillow.buttonStates (chromebar)

| Value    | Description                                                            |
|----------|------------------------------------------------------------------------|
| enabled  | Button is enabled                                                      |
| disabled | Button is disabled                                                     |
| false    | Button is hidden (this one is a boolean, not a string like the others) |

### pillow.buttonHandling (chromebar)

| Value     | Description                        |
|-----------|------------------------------------|
| system    | Button is handled by kindle itself |
| notifyapp | Button is handled by WAF           |

### pillow.contextMenuIDs

| Value                   | Description |
|-------------------------|-------------|
| browserSettings         | Unknown!    |
| browserBookmarks        | ...         |
| browserBookmarkPage     | ...         |
| browserHistory          | ...         |
| browserArticleOrWebMode | ...         |

### pillow.contextMenuStates

| Value    | Description        |
|----------|--------------------|
| enabled  | Menu item enabled  |
| disabled | Menu item disabled |