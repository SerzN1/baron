Baron - a small, fast and crossbrowser custom scrollbar with native system scroll mechanic.

## Demo

http://diokuz.github.io/baron/

## Features

- Do not replaces native system scroll mechanic.
- Customizable scrollbar design with full CSS support.
- No strong dependences on jQuery.
- Plugin system (fixable headers, sticky footer, autotests and more)

Baron just hides the system scrollbar, without removing it. This guarantees scrolling will work on any system where the CSS property 'overflow: scroll' is applied.

## Simple usage

If you want only to hide system scrollbar:

* Include either the development or minified version of baron.js:

```js
<script src="baron.js"></script>
```

* Make some HTML:

```html
<div class="scroller">
    Your scrollable content here
</div>
```

* And some CSS:

```css
.scroller {
    overflow-y: scroll;
    /* -webkit-overflow-scrolling: touch; *//* uncomment to accelerate scrolling on iOs */
}
.scroller::-webkit-scrollbar { /* Preventing webkit cross-direction scrolling bug */
    width: 0;
}
```

Note: you can choose any class names, and slyle them as you want.

* Initialize baron:

```js
$('.scroller').baron();
```

## Advanced usage

```html
<div class="scroller">
    Your scrollable content here
    <div class="scroller__track"><!-- Track is optional -->
        <div class="scroller-bar"></div>
    </div>
</div>
```

```css
.scroller {
    overflow-y: scroll;
    /* -webkit-overflow-scrolling: touch; *//* uncomment to accelerate scrolling on iOs */
}
.scroller::-webkit-scrollbar { /* Preventing webkit cross-direction scrolling bug */
    width: 0;
}
.scroller__track {
    display: none; /* Invisible by default */
    position: absolute;
    right: 4px;
    top: 10px;
    bottom: 4px;
    width: 10px;
    background: rgba(0, 0, 0, .1);
}
.baron .scroller__track {
    display: block; /* Visible when scroll is possible */
}
.scroller__bar { /* The bar. You should define width, right position and background */
    position: absolute;    
    z-index: 1;
    right: 0;
    width: 10px;
    background: #999;
}
```

You can specify some parameters at baron initialization:

```js
var scroll = $('.scroller').baron(params);

// or
var scroll = baron(params);
```

and store baron scrollbar object to `scroll` variable.

where:

```js
params = {
    // Selector for scroller element.
    // Default: this (in jQuery mode).
    scroller: '.scroller',

    // Selector for bar element
    // Default: 'undefined'
    bar: '.scroller__bar',

    // Track
    // Default: parent node of bar
    track: '.scroller__track',

    // CSS classname for scroller when its needed (when content height above scroller heights)
    // Default: ''
    barOnCls: 'baron',
    // Note: by default scroller__bar should be invisible

    // Scroll direction
    // Default: 'v' (vertical), 'h' for second baron invocation
    direction: 'h',

    // Freezing size of scroller parent, if true. Actual for horizontal scrolling.
    // Default: false
    freeze: true,

    // Local copy of jQuery-like utility
    // Default: window.jQuery
    $: function(selector, context) {
        return bonzo(query(selector, context));
    },

    // Event manager
    // Default: function(elem, event, func, mode) { params.$(elem)[mode || 'on'](event, func); };
    event: function(elem, event, func, mode) { // Events manager
        if (mode == 'trigger') {
            mode = 'fire';
        }

        bean[mode || 'on'](elem, event, func);
    }
}
```

All parameters are optional (except scroller, if you not using baron as jQuery plugin).

`scroll` methods:

```js
scroll.update(); // Update scroller
```

Note: baron returns the baron object, even in jQuery mode. That can break jQuery chaining. For example, you couldnt do this:

```js
$('.scroller').baron().css().animate();
```

but you can:

```js
$('.scroller').css().animate().baron();
```

and even more:

```js
var scroll = $('.scroller').css().animate().baron().fix();

scroll.baron({direction: 'h'}).test().anotherBaronPlugin();
```

Every baron plugin sould return baron object (this);

##Update baron

When container size changed (for example: you load additional data to container by ajax), you should call u() method:

```js
scroll.update();
```

or fire custom event 'sizeChange' to wrapper:

```js
$('.scroller').trigger('sizeChange');
```

## Horizontal and bidirectional scroll

To switch default vertical direction to horizontal direction just set 'direction' param to 'h' value:

```js
baron({
    ...
    direction: 'h'
});
```

If you want to scroll in both directions (vertical and horizontal) you must initialize baron two times: one per direction. In than case you can do chaining:

```js
baron(vParams).baron(hParams);
```

##noConflict mode

If you need window.baron for another purposes you can restore original value:
```js
// window.baron points to some other library
...
// you include baron, it replaces the window.baron variable to baron namespace

var graf = baron.noConflict();
// now window.baron points to that other library again, and you can use window.graf() etc.
```

## Custom build (Grunt)

If you want exclude fixa plugin functionality, type
```js
grunt core
```
in your console, and you will get dist/baron.js and dist/baron.min.js only with core functionality.

Type
```js
grunt full
```
to build full version of baron (including all available plugins).

Output files you can find in /dist/ folder.

## Browsers support

Full support: Chrome 1+, Firefox 3.6+, Safari 5+, Opera 9+ on Windows, OS X and iOS. Also, the best ever browser downloader - Internet Explorer - supported since version 7.

Partial support: IE6 (without fixable headers).

Not supported: Opera mini, old versions of Android browser, and other browsers which do not implement the `overflow: scroll` CSS property.

## fix plugin

To use fixable headers you should initialize fix plugin after baron:

```html
<div class="scroller">
    <div class="header__title-wrapper">
        <div class="header__title">First element</div>
    </div>
    ...content...
    <div class="header__title-wrapper">
        <div class="header__title">Second element</div>
    </div>
    ...content...
</div>
```

```js
baron(baronParams).fix(params);
```

where:
```js
params = {}
    // CSS selector for fixable elements
    // Must have parentNode with same height (see demo for details). Also see trackSmartLim parameter.
    elements: '.header__title',

    // CSS class for elements which now are outside of viewport
    outside: 'header__title_state_fixed',

    // CSS class for closest outsite element wrapper from top (left)
    before: 'header__title_position_top',

    // CSS class for closest outsite element wrapper from bottom (right)
    after: 'header__title_position_bottom',

    // if true - sets track top (left) position to header[0].parentNode.offsetHeight (offsetWidth)
    // Default: false
    limiter: true,

    // Radius for element fixing in px
    // Default: 0
    radius: 10
}
```

## test plugin

If you have some troubles with baron, use test plugin:

```js
baron(...).test(params);
```

And read results in browser console.

There is no params for test() right now.

## License

MIT.