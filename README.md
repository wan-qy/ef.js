# ef.js [![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](https://raw.githubusercontent.com/ClassicOldSong/ef.js/master/LICENSE) [![npm](https://img.shields.io/npm/dt/ef.js.svg?style=flat-square)](https://www.npmjs.com/package/ef.js) [![Build status](https://img.shields.io/travis/ClassicOldSong/ef.js.svg?style=flat-square)](https://travis-ci.org/ClassicOldSong/ef.js)
<img align="right" width="95" height="95" title="logo of ef" src="https://cloud.githubusercontent.com/assets/10512422/25222027/d07b4792-25e9-11e7-9ba7-32925061c4df.png">

(maybe) An elegant HTML template engine & basic framework

ef.js is a static template framework for browsers, which you can write your UI without concerning about the logic, or writing logic without concerning about the UI.

ef.js also provides a simple template-engine which helps you create component modules with data binding at ease, but you can also use your favourite template-engine if it can be parsed into ef.js's AST.

Demo:
+ [TodoMVC](https://classicoldsong.github.io/todomvc-efjs/) - [repo](https://github.com/ClassicOldSong/todomvc-efjs)
+ [dbmon](https://classicoldsong.github.io/js-repaint-perfs/ef/opt.html) - [repo](https://github.com/ClassicOldSong/js-repaint-perfs)

Related projects:
+ [eft-parser](https://github.com/ClassicOldSong/eft-parser) - Parser for eft templates
+ [rollup-plugin-eft](https://github.com/ClassicOldSong/rollup-plugin-eft) - Import .ef and .eft templates directly from your code, taking the advantage of ef.js into your project with zero configuration.
+ [SublimeEFMLHighlighter](https://github.com/ClassicOldSong/SublimeEFMLHighlighter) - EFML syntax highlighter for SublimeText3
+ [VSCodeEFMLHighlighter](https://marketplace.visualstudio.com/items?itemName=ClassicOldSong.efml) - EFML syntax highlighter for VSCode
+ [AtomEFMLHighlighter](https://atom.io/packages/efml) - EFML syntax highlighter for Atom
+ [PrismEFMLSyntax](https://github.com/ClassicOldSong/PrismEFMLSyntax) - EFML syntax for [Prism](http://prismjs.com/)

Community project(s):
+ [EFML.vim](https://github.com/deluxghost/EFML.vim) by [deluxghost](https://github.com/deluxghost) - EFML (*.ef, *.eft) syntax highlighting for Vim

## Usage
``` javascript
import { create, inform, exec, bundle, setParser, parseEft, t, version } from 'ef.js'
// or you can use import * as ef from 'ef.js'

version // Version string of ef.js

setParser(someparser) // Change the default parser for ef.js so you can use a different type of template
parseEft('Your awesome template') // Get ef.js ast using default parser

const templateString = 'Your awesome template'
const ast = [/* AST which supported by ef */]

const data = {
	$data: {/* Binding data */},
	$methods: {/* Binding methods */}
}

const template1 = create(template)
const template2 = create(ast)
const template3 = t`Your awesome template`

const component1 = new template1() // Create a component without data
const component2 = new template2(data) // Create a component and then updates it's data

inform() // Tell ef to cache operation **USE WITH CARE**
exec() // Tell ef to execute all cached operations **USE WITH CARE**
exec(true) // Force execute cached operations **USE WITH CARE**
bundle(callback) // Wrapper for inform() and exec()

component1.$element // The DOM element of component1
component2.$element // The DOM element of component2

component1.$data.something = 'something new' // Update the binding data 'something'
component2.$methods.someMethod = ({e, value, state}) => {
	state.$data.something = 'something new'
	console.log('Event target', e.target)
	console.log('Value passed', value)
} // Update binding method

const logData = val => console.log('Subscribed data updated:', val)
component1.$subscribe('info.data', logData) // Observe a value
component1.$unsubscribe('info.data', logData) // Stop observing a value

component1.$update(data) // Update the whole component state
component2.$parent // Get where the component is mounted

component1.$refs // Get all referenced nodes

component1.mountingPoint = component2 // Mount component2 to 'mountingPoint' on component1
component1.mountingPoint = null // Detach the mounted component

component1.listMP.push(componet2) // Mount component2 to list 'listMP' mounting point on component1

component1.$mount(...) // Mount method called by ef when trying to mount
compinent1.$umount() // Unmount from parent
component1.$destroy() // Destroy the component when not needed for more memory

```

## ef.js template language (EFML) format
EFML is a completely **logic-free** template language. Just like HTML, there you can do nothing about logic, but EFML provides a easy starting point for data binding and events handling.

Also EFML is the first language that can be parsed into the AST which ef supports.

**Note:** EFML is **very strict to indents**. Wrong indents could lead to a parsing error.

Here is an example.

```
Tree structure
Lines not started with >#%@.+- are considered as comments
The escape character of eft is '&', for prevention of the conflict with js escapes.
Except for the change of the character, all the usage should be the same.
this is a comment
'>' stands for tag name
>div
	'#' stands for attributes
	Mustaches stands for binding data
	content inside mustaches after '=' stands for the default value for this binding
	content without mustaches stands for a static data
	which means that you cannot modify them using ef.js
	#class = {{class = some class name}}
	#style = {{attr.style = background: #ECECEC}}
	#id = testdiv
	#some-attr = some text
	#content
	'%' stands for properties
	%title = Welcome, {{name}}
	%anotherProperty = text
	'@' stands for events
	contents after ':' are considered as value passed to the handler
	@click = updateInfo:{{binding.value}} and static value
	modify keys now can be bind easily
	@mousedown.shift.alt.ctrl.meta = select
	bind to keys is also easy
	@keypress.13 = submit
	use '.prevent' to preventDefault, '.stop' to stopPropagation, '.stopImmediate' to stopImmediatePropagation
	@keydown.8.prevent.stop = stopbackspace
	use '.capture' to capture an event
	@submit.capture.stopImmediate = submit
	'.' stands for text nodes
	.Name: {{name}}&nJob: {{job}}
	>br
	'-' stands for standard mounting point
	-node1
	'.' after a tag name stands for class names for this tag
	>p.some.{{binding.class}}.class.names
		'#' at the end of a tag name stands for the reference name of the node
		Mustaches after a dot will bind to 'class' automatically
		>span.{{emergency = emergency}}#notice_box
			.Notice: {{notice}}
		.some text
		-node2
		'+' stands for list mounting point
		+list1
```

For standalone eft parser see [eft-parser](https://github.com/ClassicOldSong/eft-parser).

## Run a test
```
$ git clone https://github.com/ClassicOldSong/ef.js.git
$ cd ef.js
$ npm install
$ npm run dev
```
Then you can test it out in the opening browser window.

## Build from source
```
$ git clone https://github.com/ClassicOldSong/ef.js.git
$ cd ef.js
$ npm install
$ npm run build
```
Then you can get the fresh-built `ef.min.js` in the `dist` folder.

**Note:** All debugging messages are disabled in the production version.

## License
[MIT](http://cos.mit-license.org/)
