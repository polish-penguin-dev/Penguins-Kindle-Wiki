# Kindle Object Documentation

## Gestures

> [!NOTE]
> The gestures you can use are configured within the `config.xml`. All of the following are replaceable functions.

### `kindle.gestures.onswipe(direction, pageX, pageY) = function() { ... }`

Description: triggered whenever the user swipes on the kindle screen. Returns the [direction](https://github.com/polish-penguin-dev/Penguins-Kindle-Wiki/blob/main/Kindle%20Object%20Docs/Enums.md#direction), `pageX` and `pageY` to indicate the position where the swipe started on the page.

### `kindle.gestures.onflick(direction) = function() { ... }`

Description: triggered whenever the user flicks the kindle screen, returns [flick direction](https://github.com/polish-penguin-dev/Penguins-Kindle-Wiki/blob/main/Kindle%20Object%20Docs/Enums.md#direction).

### `kindle.gestures.onpan(event) = function() { ... }`

Description: triggered whenever the user pans (maybe?) their kindle screen. The `event` parameter is unknown.

### `kindle.gestures.onpinch(event) = function() { ... }`

Description: triggered whenever the user pinches their kindle screen. The `event` parameter is unknown.

### `kindle.gestures.onhold(event) = function() { ... }`

Description: triggered whenever the user holds down on their kindle screen. The `event` parameter is unknown.

### `kindle.gestures.onzooms(event) = function() { ... }`

Description: triggered whenever the user performs the zoom gesture on their kindle screen. The `event` parameter is unknown.

### `kindle.gestures.ontap(event) = function() { ... }`

Description: triggered whenever the user taps on their kindle screen. The `event` parameter is unknown.