# modal.js

###### [Demo][Krabby] | [Getting started](doc/tutorial.md)

A way to create modal interfaces with filters.

A live demo can be found in [Krabby].

[Krabby]: https://krabby.netlify.com

## Installation

Add [`modal.js`](src/modal.js) to your project.

## Usage

A minimal configuration to get started:

``` javascript
const modal = new Modal
modal.enable('Text', 'Command')
modal.map('Command', ['KeyJ'], ({ repeat }) => scroll.down(repeat), 'Scroll down', 'Scroll')
modal.map('Command', ['KeyK'], ({ repeat }) => scroll.up(repeat), 'Scroll up', 'Scroll')
```

**Note**: The commands scroll with [scroll.js].

[scroll.js]: https://github.com/alexherbo2/scroll.js

You can find some examples in [Krabby].

Read the [documentation](doc) for a complete reference.
