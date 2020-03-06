# Tutorial

**Table of contents**

- [Getting started](#getting-started)
- [Mapping](#mapping)
- [Keyboard layout](#keyboard-layout)
- [Contexts](#contexts)
- [Custom contexts](#custom-contexts)
- [Site-specific mappings](#site-specific-mappings)
- [Overloading](#overloading)
- [Passing mode](#passing-mode)
- [Events](#events)
- [Self documenting](#self-documenting)

## Getting started

A minimal configuration to get started:

``` javascript
const modal = new Modal
modal.enable('Text', 'Command')
modal.map('Command', ['KeyJ'], ({ repeat }) => scroll.down(repeat), 'Scroll down', 'Scroll')
modal.map('Command', ['KeyK'], ({ repeat }) => scroll.up(repeat), 'Scroll up', 'Scroll')
```

**Note**: The commands scroll with [scroll.js].

[scroll.js]: https://github.com/alexherbo2/scroll.js

## Mapping

Creating and removing mappings boils down to the following commands:

```
map(context, keys, command, description, label)
```

```
unmap(context, keys)
```

- `context` dictates in what [context](#contexts) the mapping will be available.

- `keys` is a list of keys representing a chord – a key sequence in which the keys are pressed at the same time.

They are composed of a single [key code][`KeyboardEvent.code`] and optionally, one or multiple [modifiers].

See the list of [key values] for special keys.

[`KeyboardEvent.code`]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code
[Key values]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values
[Modifiers]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values#Modifier_keys

**Note**: Commands are bound to **physical keys** and **displayed** with the [US layout][QWERTY] by default.
See the [Keyboard layout] section for configuration.

- `command` is the function to evaluate.

The function takes exactly one argument, the [`keydown`] event that triggered the command.
For most commands, this argument can be ignored.
The command can also be an instance of `Modal` to allow command chaining.

[`keydown`]: https://developer.mozilla.org/en-US/docs/Web/API/Document/keydown_event

- `description` is the description of the command.

- `label` is the label of the command.

**Example** – Scroll with [scroll.js]:

``` javascript
modal.map('Command', ['KeyJ'], ({ repeat }) => scroll.down(repeat), 'Scroll down', 'Scroll')
modal.map('Command', ['KeyK'], ({ repeat }) => scroll.up(repeat), 'Scroll up', 'Scroll')
```

## Keyboard layout

[Keyboard layout]: #keyboard-layout

**Example** – Display keys with the [US layout][QWERTY]:

``` javascript
modal.keyMap = {
  Backquote: { key: '`', shiftKey: '~' }, Digit1: { key: '1', shiftKey: '!' }, Digit2: { key: '2', shiftKey: '@' }, Digit3: { key: '3', shiftKey: '#' }, Digit4: { key: '4', shiftKey: '$' }, Digit5: { key: '5', shiftKey: '%' }, Digit6: { key: '6', shiftKey: '^' }, Digit7: { key: '7', shiftKey: '&' }, Digit8: { key: '8', shiftKey: '*' }, Digit9: { key: '9', shiftKey: '(' }, Digit0: { key: '0', shiftKey: ')' }, Minus: { key: '-', shiftKey: '_' }, Equal: { key: '=', shiftKey: '+' },
  KeyQ: { key: 'q', shiftKey: 'Q' }, KeyW: { key: 'w', shiftKey: 'W' }, KeyE: { key: 'e', shiftKey: 'E' }, KeyR: { key: 'r', shiftKey: 'R' }, KeyT: { key: 't', shiftKey: 'T' }, KeyY: { key: 'y', shiftKey: 'Y' }, KeyU: { key: 'u', shiftKey: 'U' }, KeyI: { key: 'i', shiftKey: 'I' }, KeyO: { key: 'o', shiftKey: 'O' }, KeyP: { key: 'p', shiftKey: 'P' }, BracketLeft: { key: '[', shiftKey: '{' }, BracketRight: { key: ']', shiftKey: '}' }, Backslash: { key: '\\', shiftKey: '|' },
  KeyA: { key: 'a', shiftKey: 'A' }, KeyS: { key: 's', shiftKey: 'S' }, KeyD: { key: 'd', shiftKey: 'D' }, KeyF: { key: 'f', shiftKey: 'F' }, KeyG: { key: 'g', shiftKey: 'G' }, KeyH: { key: 'h', shiftKey: 'H' }, KeyJ: { key: 'j', shiftKey: 'J' }, KeyK: { key: 'k', shiftKey: 'K' }, KeyL: { key: 'l', shiftKey: 'L' }, Semicolon: { key: ';', shiftKey: ':' }, Quote: { key: "'", shiftKey: '"' },
  KeyZ: { key: 'z', shiftKey: 'Z' }, KeyX: { key: 'x', shiftKey: 'X' }, KeyC: { key: 'c', shiftKey: 'C' }, KeyV: { key: 'v', shiftKey: 'V' }, KeyB: { key: 'b', shiftKey: 'B' }, KeyN: { key: 'n', shiftKey: 'N' }, KeyM: { key: 'm', shiftKey: 'M' }, Comma: { key: ',', shiftKey: '<' }, Period: { key: '.', shiftKey: '>' }, Slash: { key: '/', shiftKey: '?' }
}
```

[QWERTY]: https://en.wikipedia.org/wiki/QWERTY

## Contexts

modal.js lets you map a key to a command in different contexts:

- `Command` ⇒ Context for entering keyboard commands.
- `Text` ⇒ Context for typing.
- `Link` ⇒ Context for links.
- `Image` ⇒ Context for links.
- `Video` ⇒ Context for videos.
- `Document` ⇒ Context when selecting the whole document.
- `Page` ⇒ Context without filtering.

By default, only `Page` is enabled.

```
enable(...filters)
```

You can create a vi-like interface with the following configuration:

``` javascript
modal.enable('Text', 'Command')
```

## Custom contexts

Custom contexts can be created using filters.

```
filter(name, filter, parent)
```

- `name` is the filter name.

- `filter` is a function that dictates in what context the mapping will be available.

A third parameter can be passed to inherit a context and its commands.

- `parent` is the parent context.

**Example** – Create a context for tables:

``` javascript
modal.filter('Table', () => modal.findParent((element) => element.nodeName === 'TABLE'), 'Command')
```

## Site-specific mappings

**Example** – Create [GitHub]-specific mappings:

``` javascript
modal.filter('GitHub', () => location.hostname === 'github.com', 'Command')
modal.filter('GitHub · Notifications', () => location.pathname === '/notifications', 'GitHub')
modal.enable('GitHub · Notifications', 'GitHub', ...)

// GitHub · Notifications – https://github.com/notifications
modal.map('GitHub · Notifications', ['KeyR'], () => document.querySelector('form[action="/notifications/mark"]').submit(), 'Mark all as read', 'GitHub · Notifications')
```

[GitHub]: https://github.com

## Overloading

You can bind different commands to a same key, depending on the context.

**Example** – Add mappings to yank pages and links with [clipboard.js]:

``` javascript
modal.map('Document', ['KeyY'], () => Clipboard.copy(location.href), 'Copy page address', 'Clipboard')
modal.map('Link', ['KeyY'], ({ target }) => Clipboard.copy(target.href), 'Copy link address', 'Clipboard')
```

[clipboard.js]: https://github.com/alexherbo2/clipboard.js

## Passing mode

Pass all keys but <kbd>Alt</kbd> + <kbd>Escape</kbd>:

``` javascript
const pass = new Modal('Pass')
modal.map('Page', ['Alt', 'Escape'], pass, 'Pass all keys to the page', 'Pass keys')
pass.map('Page', ['Alt', 'Escape'], modal, 'Stop passing keys to the page', 'Pass keys')
```

## Events

Commands can be registered to be executed when certain events arise.

- `context-change(context)`
- `command(command)`
- `default(event)`
- `start()`
- `stop()`

**Example** – Display the current context:

``` javascript
modal.on('context-change', (context) => {
  modal.notify({
    id: 'context',
    message: context.name
  })
})
```

## Self documenting

Finally, you can display all available commands in the current context with the help command.

``` javascript
modal.map('Page', ['F1'], () => modal.help(), 'Show help', 'Help')
```
