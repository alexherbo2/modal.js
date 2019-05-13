# Modal

> A way to create modal interfaces with filters.

## Overview

### Contexts

Modal lets you map a key to a command in different contexts:

- **Command** is the context for entering keyboard commands,
- **Text** is the context for typing text,
- **Link** is the context for links,
- **Video** is the context for videos,
- **Page** is a context with no filtering.

### Get started

**Example** – A minimal configuration for getting started:

``` javascript
const modal = new Modal
modal.enable('Text', 'Command')
modal.map('Command', ['KeyJ'], () => document.scrollingElement.scrollBy({ top: 60 }), 'Scroll down')
modal.map('Command', ['KeyK'], () => document.scrollingElement.scrollBy({ top: -60 }), 'Scroll up')
```

**Context**: Context of the command.

**Keys**: Keys represent a chord – a key sequence in which the keys are pressed at the same time.
They are composed of a single [key code][KeyboardEvent.code] and optional [modifiers].
For special keys, the list of key values can be found [here][Key Values].

**Command**: The command is either a function that takes exactly one argument – the [keydown]
event that triggered the command – or an instance of **Modal**.

**Description**: Description of the command.

**Note**: Commands are bound to physical keys and displayed with the US layout by default.
If you want to display the commands with a different layout, you can set the [`keyMap`] or [`KEY_MAP`] properties,
depending if you want to change the display for an instance of **Modal** or the class itself.

### Passing mode

**Example** – Create a mode to pass all keys but <kbd>Alt</kbd> + <kbd>Escape</kbd>:

``` javascript
const pass = new Modal('Pass')
modal.map('Page', ['Alt', 'Escape'], pass, 'Pass all keys to the page')
pass.map('Page', ['Alt', 'Escape'], modal, 'Stop passing keys to the page')
```

### Custom contexts

Custom contexts can be created using filters.

**Example** – Override the built-in link context:

``` javascript
modal.filter('Link', () => document.activeElement.nodeName === 'A', 'Command')
modal.enable('Link', 'Text', 'Command')
```

The filter is a function that dictates in what context a mapping will be available.

A third parameter can be passed to inherit a context and its commands.
In the example above, **Link** inherits from **Command**.

### Same key mappings

You can bind different commands to a same key.

**Example** – Add mappings to yank pages and links:

``` javascript
modal.map('Command', ['KeyY'], () => Clipboard.copy(location.href), 'Copy page address')
modal.map('Link', ['KeyY'], (event) => Clipboard.copy(event.target.href), 'Copy link address')
```

### Specific site mappings

Specific site bindings are also supported through custom contexts.

**Example** – Disable shortcuts in Gmail:

``` javascript
modal.filter('Gmail', () => location.hostname === 'mail.google.com')
modal.enable('Gmail', ...)
```

Create a context for [Gmail] and use the [built-in shortcuts][Gmail keyboard shortcuts].

**Example** – Create a context for GitHub:

``` javascript
modal.filter('GitHub', () => location.hostname === 'github.com', 'Command')
modal.filter('GitHub · Notifications', () => location.pathname === '/notifications', 'GitHub')
modal.enable('GitHub · Notifications', 'GitHub', ...)

modal.map('GitHub · Notifications', ['KeyR'], () => document.querySelector('form[action="/notifications/mark"]').submit(), 'Mark all as read')
```

### Events

Commands can be registered to be executed when certain events arise.

**Example** – Display the current context:

``` javascript
modal.on('context-change', (context) => modal.notify({ id: 'context', message: context.name }))
```

### Getting help

Finally, you can display all available commands (for the current context) with the help command.

**Example** – Show help on <kbd>F1</kbd>:

``` javascript
modal.map('Page', ['F1'], () => modal.help(), 'Show help')
```

More examples at [Create a keyboard interface to the web].

[Create a keyboard interface to the web]: https://alexherbo2.github.io/blog/chrome/create-a-keyboard-interface-to-the-web/

[keydown]: https://developer.mozilla.org/en-US/docs/Web/API/Document/keydown_event
[KeyboardEvent.code]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/code
[Key Values]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values
[Modifiers]: https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values#Modifier_keys

[Gmail]: https://mail.google.com
[Gmail keyboard shortcuts]: https://support.google.com/mail/answer/6594

[GitHub]: https://github.com
[GitHub · Notifications]: https://github.com/notifications

[`keyMap`]: https://github.com/alexherbo2/modal.js/search?q=keyMap
[`KEY_MAP`]: https://github.com/alexherbo2/modal.js/search?q=KEY_MAP
