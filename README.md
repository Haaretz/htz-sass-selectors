# @haaretz/sass-selectors

> Helpers for manipulating selectors in Sass

Haaretz interactive element are often used in a quote-unquote, "hostile environment",
included into pages that are already ripe with global styles.

Therefore, they should be both robust - as unlikely as possible to be affected by
preexisting styles - and unobtrusive - styled in a manner that ensures they will not
affect already existing styles.

This module exposes mixins and functions designed for this purpose - allowing for
the creation and use of prefixed css-custom-properties and BEM-style selectors.

## Installation

```sh
yarn add --dev @haaretz/css-selectors
```
or with npm:
```sh
yarn install --save-dev @haaretz/css-selectors
```

## Usage

```scss
@import '~@haaretz/css-selectors'
```

### Configuration

`@haaretz/sass-selectors` exposes the following configuration variables:

#### `$selector-prefix`
The prefix added to classes and custom properties  
**type:** `string`  
**default:** `''`  


#### `$element-separator`
The separator used for BEM child elements  
**type:** `string`  
**default:** `'__'`  

#### `$modifier-separator`
The separator used for BEM modifiers  
**type:** `string`  
**default:** `'--'`

#### `$state-prefix`
The default prefix for state classes  
**type:** `string`  
**default:** `'is'`

#### `$qualify-state`
Should state classes be added as qualifiers like in SMACSS
(e.g., `.base-class.is-state`) this increasing specificity
or as a single class like in classic BEM (e.g. `.base-class--is-state`)
thus retaining flatters specificity.  
**type:** `boolean`  
**default:** `true`  


### Custom Property Helpers

#### `create-var`
Create custom properties that are prefixed to avoid name
collusion but without the syntactic verbosity of repeatedly doing:
`--#{$selector-prefix}variable-name: variable-value;`  
**Type:** `mixin`  
**Params:**  
  `$name` (`string`): The custom-property's unprefixed name  
  `$value` (`any`): The value assigned to the custom-property  
**Example:**
```scss
.foo {
  @include create-var(primary, red);
}
// Assuming `$selector-prefix: bar-`, will generate:
// .foo { --bar-primary: red; }
```

#### `use-var`
Use a prefixed custom properties to avoid name collusion
but without the syntactic verbosity of repeatedly doing:
color: var(--#{$selector-prefix}color1);  
**Type:** `function`  
**Params:**  
  `$name` ('string'): The _unprefixed_ name of the variable to use.
  Usually one defined using the `css-var` mixin.  
**Return:** (`string`) Prefixed custom property usage   
**Example:**
```scss
.foo {
  color: use-var(primary);
}
// Assuming `$selector-prefix: bar-`, will generate:
// .foo { color: var(--bar-primary); }
```


### BEM Helpers

#### `class`
Create class selectors that are prefixed to avoid name
collusion but without the syntactic verbosity of repeatedly doing:
.#{$selector-prefix}name { ... }.

Can be used to create any class, but designed chiefly to be used as
an idiomatic way to create `BEM` block rulesets (hense, aliased as `b`)   
**Alias:** `b`   
**Type:** `mixin`   
**Params:**   
  `$names` (`arglist`): Unprefixed class name(s) to create the selector from   
**Examples:**   
```scss
// Single class:
@include class(foo) { color: red; }
// Assuming `$selector-prefix: bar-`, will generate:
// .bar-foo { color: red; }

// Multiple class:
@include class(foo, bar) { color: red; }
// Assuming `$selector-prefix: bar-`, will generate:
// .bar-foo, .bar-bar { color: red; }

// Scoped:
.scope { @include class(foo) { color: red; } }
// Assuming `$selector-prefix: bar-`, will generate:
// .scope {.bar-foo { color: red; } }
```

#### `element`
Idiomatically create BEM element rulesets. Can be nested inside
modifiers or other elements to infinite depth.   
**Alias:** `e`   
**Type:** `mixin`   
**Params:**
  `$name` (`string`): The element's name   
**Example:**   
```scss
@include b(foo) {
  ...
  @include element(bar) { ... } // -> .foo__bar { ... }
}
```

#### `modifier`
Idiomatically create BEM modifier rulesets. Can be nested inside
elements or other modifiers to infinite depth   
**Alias:** `m`   
**Type:** `mixin`   
**Params:**   
  `$name` (`string`): The modifier's name   
**Example:**   
```scss
@include b(foo) {
  ...
  @include modifier(bar) { ... } // -> .foo--bar { ... }
}
```

#### `is`
Idiomatically create state qualifier classes of a parent selector   
**Alias:** `state`   
**Type:** `mixin`   
**Params:**   
  `$state` (`string`): The represented state   
  `$prefix` (`string`): The string to use when prefixing `$state`,
  e.g, `@include is(open, is) { ... }` will create an `.is-open`
  qualifier. default: `$state-prfix`.   
  `$qualify` (`boolean`): Should state classes be added as qualifiers
  like in SMACSS (e.g., `.base-class.is-state`) thus increasing
  specificity or as a single class like in classic BEM (e.g.
  `.base-class--is-state`) thus retaining flatter specificity.   
**Example:**   
```scss
html {
  ...
  @include is(app) { ... } // -> .html.is-app { ... }
}
```


### Qualifiers

#### `when-is`
Qualify a selector with another selector to increase specificity   
**Alias:** `qualify`   
**Type:** `mixin`   
**Params:**   
  `$qualifier` (`string`)   
**Examples:**   
```scss
// Qualify element with a class:
button {
  @include when-is(.large) { ... } // -> button.large {...}
}

// Qualify class when a certain element:
.button {
  @include when-is(a) { ... } // -> a.button {...}
}
```
