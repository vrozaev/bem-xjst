# Input data — BEMJSON

## Overview

Any JavaScript object can be considered BEMJSON. However, to control the templating result, we need the standard modes expected by [bem-xjst](1-about.md).

## Conventions for standard BEMJSON modes

* [block](#block) — block name
* [elem](#elem) — element name
* [mods](#mods) — block modifier hash
* [elemMods](#elemmods) — element modifier hash
* [content](#content) — child nodes
* [mix](#mix) — BEM entities that should be mixed
* [bem](#bem) — whether to add classes and JavaScript parameters for the BEM entity itself and its mixes
* [js](#js) — JavaScript parameters
* [attrs](#attrs) — hash of HTML attributes
* [cls](#cls) — HTML class
* [tag](#tag) — HTML tag
* [user-defined fields](#user-defined-fields)

### block

`{String}` block name.

```js
// BEMJSON
{ block: 'page' }
```

### elem

`{String}` element name.

```js
// BEMJSON
{
    block: 'page',
    content: {
        // The block can be omitted on this node,
        // since it will be taken from the context (page).
        elem: 'header'
    }
}
```

### mods

`{Object}` block modifier hash. Keys can be any valid keys for JavaScript objects. Values can be `String` or `Boolean` types.

```js
// BEMJSON
{
    block: 'tab',
    mods: { name: 'index', visible: true }
}
```

*Result of templating:*

```html
<div class="tab tab_name_index tab_visible"></div>
```

`mods` is ignored if `elem` and `elemMods` are specified.

```js
// BEMJSON
{
    block: 'control',
    // Ignored because 'elem' and 'elemMods' are specified
    mods: { type: 'nav' },
    elem: 'input',
    elemMods: { type: 'search' }
}
```

*Result of templating:*

```html
<div class="control__input control__input_type_search"></div>
```

### elemMods

`{Object}` hash of element modifiers. `elemMods` is ignored if the element isn’t specified.

```js
// BEMJSON
{
    block: 'page',
    elem: 'header',
    elemMods: { type: 'search' }
}
```

### content

`{*}` Child nodes.

```js
// BEMJSON
{
    block: 'page',
    content: [
        { block: 'header'},
        { block: 'article', content: '…' },
        '© 2017'
    ]
}
```

An object with `html` field is a special type of BEMJSON nodes. All other fields of such objects will be ignored. `html` field should have a string which will be rendered as is.

```js
{
    block: 'markup',
    content: {
        html: '<code>new Date();</code>'
    }
}
```

*Result of templating:*

```html
<div class="markup"><code>new Date();</code></div>
```

### mix

`{Object|Object[]|String}` BEM entities to [mix](https://en.bem.info/methodology/key-concepts/#mix) with the current one.

```js
// BEMJSON
{
    block: 'link',
    mix: { block: 'controller' }
}
```

*Result of templating:*

```html
<div class="link controller"></div>
```

### js

`{Boolean|Object}` JavaScript parameters. If the value isn’t falsy, it mixes `i-bem` and adds the content to the JavaScript parameters. By default: `undefined`.

More information about [i-bem and JavaScript parameters](https://en.bem.info/platform/i-bem/parameters/).

```js
// BEMJSON
{
   block: 'link',
   js: true
}
```

*Result of templating:*

```html
<div class="link i-bem" data-bem='{"link":{}}'></div>
```

Values are escaped:

```js
// BEMJSON
{
   block: 'link',
   js: { title: 'film "Sid & Nancy"' }
}
```

*Result of templating:*

```html
<div class="link i-bem" data-bem='{"link":{title:"film &#39;Sid &amp; Nancy&#39;"}}'></div>
```

### bem

`{Boolean}` tells the template engine whether to add classes and JavaScript parameters for the BEM entity and its mixes. By default: `true`.

```js
// BEMJSON
{
    block: 'menu',
    elem: 'item',
    bem: false
}
```

*Result of templating:*

```html
<div></div>
```

### attrs

`{Object}` hash of HTML attributes. Attribute values are [escaped using the attrEscape function](6-templates-context.md#attrescape).

```js
// BEMJSON
{
    attrs: {
        id: 'anchor',
        name: 'Cartoon "Tom & Jerry"'
    }
}
```

*Result of templating:*

```html
<div id="anchor" name="Cartoon &quot;Tom &amp; Jerry&quot;"></div>
```

If any field in attrs hash will contain BEM-entity (object with `block`/`elem`/`mods`/`elemMods` fields) for such object will be run `BEMHTML.apply`. Example:

```js
// BEMJSON
{ 
    block: 'button', 
    attrs: {
        'data-text': {
            block: 'i-bem',
            elem: 'i18n',
            key: 'button-success'
        }
    }
}
```

```js
// Template
block('i-bem').elem('i18n')({
  // Here I18N.get it’s just method which returns some string
  def: (node, ctx) => node.I18N.get(ctx.key)
});
```

*Result of templating:*

```html
<div id="button" data-text="Congratulations!"></div>
```


### cls

`{String}` HTML class or classes (separated by spaces) that do not belong to the BEM subject domain. For example, the use of [microformats](http://microformats.org/) or semantic markup from [schema.org](https://schema.org/).

```js
// BEMJSON
{
    block: 'user',
    cls: 'h-card p-name',
    content: 'Andrei Linde'
}
```

*Result of templating:*

```html
<div class="user h-card p-name">Andrei Linde</div>
```

### tag

`{Boolean|String}` HTML tag. `false` or `''` tells the BEMHTML engine to skip generating the HTML tag. By default: `div`.

```js
// BEMJSON
{
    tag: false,
    content: 'start'
}
```

*Result of templating:*

```html
start
```

### User-defined fields

You can add any other data fields in order to later process them in the template body as you see fit.

```js
// BEMJSON
{
    block: 'link',
    url: '/',
    target: '_blank'
}
```

For more information about how to process user-defined BEMJSON fields, see the section on [templates](5-templates-syntax.md).

Read next: [Template syntax](5-templates-syntax.md)
