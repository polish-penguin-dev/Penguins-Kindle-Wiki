# Kindle Object Documentation

## Messaging

Send & retrieve information through the LIPC messaging system.

### `kindle.messaging.sendMessage(id, event, data)`

Send a JSON message through the kindle's internal LIPC messaging system.

| Name  | Ex. Value                                                                       | Type   | Description                       |
|-------|---------------------------------------------------------------------------------|--------|-----------------------------------|
| id    | com.lab126.chromebar                                                            | String | The app ID to send the message to |
| event | configureChrome                                                                 | String | The event identifier              |
| data  | `{ appId: "com.lab126.store", topNavBar: { template: "title", title: “...” } }` | String | Stringified JSON data             |

### `kindle.messaging.sendStringMessage(id, event, data)`

Send a string message through the kindle's internal LIPC messaging system.

| Name  | Ex. Value               | Type   | Description                       |
|-------|-------------------------|--------|-----------------------------------|
| id    | com.kindlemodding.utild | String | The app ID to send the message to |
| event | runCMD                  | String | The event identifier              |
| data  | echo "Hello World!"     | String | String data                       |

### `kindle.messaging.recieveMessage(eventType, callback)`

Recieve a message sent to the WAF through `lipc-send-event`. **THIS IS NOT THE EQUIVALENT OF `lipc-get-prop`!**

| Name      | Ex. Value                          | Type   | Description                                                                                             |
|-----------|------------------------------------|--------|---------------------------------------------------------------------------------------------------------|
| eventType | myEvent                            | String | The event identifier passed in when calling lipc-send-event                                             |
| callback  | function (eventType, json) { ... } | String | A callback, containing the eventType once more (according to the old wiki?) and the JSON data recieved. |