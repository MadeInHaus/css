# HAUS CSS / Sass Styleguide

*A :muscle: approach to our presentation layer.*

This styleguide has been greatly inspired by (and ported over in some cases from) [Airbnb's wonderful css styleguide](https://github.com/airbnb/css).

## Table of Contents

  1. [Terminology](#terminology)
    - [Rule Declaration](#rule-declaration)
    - [Selectors](#selectors)
    - [Properties](#properties)
  1. [CSS](#css)
    - [Formatting](#formatting)
    - [Comments](#comments)
    - [ID Selectors](#id-selectors)
    - [JavaScript hooks](#javascript-hooks)
  1. [Sass](#sass)
    - [Syntax](#syntax)
    - [Ordering](#ordering-of-property-declarations)
    - [Mixins](#mixins)
    - [Placeholders](#placeholders)
    - [Nested selectors](#nested-selectors)
    - [Property order](#property-order)

## Terminology

### Rule declaration

A "rule declaration" is the name given to a selector (or a group of selectors) with an accompanying group of properties. Here's an example:

```css
.content {
  font-size: 1.8em;
  line-height: 1.2;
}
```

### Selectors

In a rule declaration, "selectors" are the bits that determine which elements in the DOM tree will be styled by the defined properties. Selectors can match HTML elements, as well as an element's class, ID, or any of its attributes. Here are some examples of selectors:

```css
.my-element-class {
  /* ... */
}

[aria-hidden] {
  /* ... */
}
```

### Properties

Finally, properties are what give the selected elements of a rule declaration their style. Properties are key-value pairs, and a rule declaration can contain one or more property declarations. Property declarations look like this:

```css
/* some selector */ {
  background: #f1f1f1;
  color: #333;
}
```

## CSS

### Formatting

* Use soft tabs (4 spaces) for indentation
* Use dashes over camelCasing in class names. Never use underscores.
* Do not use ID selectors
* When using multiple selectors in a rule declaration, give each selector its own line.
* Put a space before the opening brace `{` in rule declarations
* In properties, put a space after, but not before, the `:` character.
* Put closing braces `}` of rule declarations on a new line
* Put blank lines between rule declarations

**Bad**

```css
.avatar{
    border-radius:50%;
    border:2px solid white; }
.no, .nope, .garbage {
    // ...
}
#hell-no {
  // ...
}
```

**Good**

```css
.avatar {
  border-radius: 50%;
  border: 2px solid white;
}

.one,
.selector,
.per-line {
  // ...
}
```

### Comments

* Prefer line comments (`//` in Sass-land) to block comments.
* Prefer comments on their own line. Avoid end-of-line comments.
* Write detailed comments for code that isn't self-documenting:
  - Uses of z-index
  - Compatibility or browser-specific hacks

### ID selectors

While it is possible to select elements by ID in CSS, it should generally be considered an anti-pattern. ID selectors introduce an unnecessarily high level of [specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity) to your rule declarations, and they are not reusable.

For more on this subject, read [CSS Wizardry's article](http://csswizardry.com/2014/07/hacks-for-dealing-with-specificity/) on dealing with specificity.

### JavaScript hooks

Avoid binding to the same class in both your CSS and JavaScript. Conflating the two often leads to, at a minimum, time wasted during refactoring when a developer must cross-reference each class they are changing, and at its worst, developers being afraid to make changes for fear of breaking functionality.

We recommend creating JavaScript-specific classes to bind to, prefixed with `.js-`:

```html
<button class="btn btn-primary js-do-rad-stuff">Do Rad Stuff</button>
```

## Sass

### Syntax

* Use the `.scss` syntax, never the original `.sass` syntax
* Order your `@extend`, regular CSS and `@include` declarations logically (see below)

### Ordering of property declarations

1. `@extend` declarations

    Just as in other OOP languages, it's helpful to know right away that this "class" inherits from another.

    ```scss
    .btn-primary {
      @extend %btn;
      // ...
    }
    ```

2. Property declarations

    Now list all standard property declarations, anything that isn't an `@extend`, `@include`, or a nested selector.

    ```scss
    .btn-green {
      @extend %btn;
      background: green;
      font-weight: bold;
      // ...
    }
    ```

3. `@include` declarations

    Grouping `@include`s at the end makes it easier to read the entire selector, and it also visually separates them from `@extend`s.

    ```scss
    .btn-green {
      @extend %btn;
      background: green;
      font-weight: bold;
      @include hide-text();
      // ...
    }
    ```

4. Nested selectors

    Nested selectors, _if necessary_, go last, and nothing goes after them. Apply the same guidelines as above to your nested selectors. Nesting selectors beyond three levels should be avoided as it produces over-specific code.

    ```scss
    .btn {
      @extend %btn;
      background: green;
      font-weight: bold;
      @include hide-text();
      .icon {
        margin-right: 10px;
      }
    }
    ```

### Mixins

Mixins, defined via `@mixin` and called with `@include`, should be used sparingly and only when function arguments are necessary. A mixin without function arguments (i.e. `@mixin hide { display: none; }`) is better accomplished using a placeholder selector (see below) in order to prevent code duplication.

### Placeholders

Placeholders in Sass, defined via `%selector` and used with `@extend`, are a way of defining rule declarations that aren't automatically output in your compiled stylesheet. Instead, other selectors "inherit" from the placeholder, and the relevant selectors are copied to the point in the stylesheet where the placeholder is defined. This is best illustrated with the example below.

Placeholders are powerful but easy to abuse, especially when combined with nested selectors. **As a rule of thumb, avoid creating placeholders with nested rule declarations, or calling `@extend` inside nested selectors.** Placeholders are great for simple inheritance, but can easily result in the accidental creation of additional selectors without paying close attention to how and where they are used.

**Sass**

```sass
// Unless we call `@extend %icon` these properties won't be compiled!
%icon {
  font-family: "Airglyphs";
}

.icon-error {
  @extend %icon;
  color: red;
}

.icon-success {
  @extend %icon;
  color: green;
}
```

**CSS**

```css
.icon-error,
.icon-success {
  font-family: "Airglyphs";
}

.icon-error {
  color: red;
}

.icon-success {
  color: green;
}
```

### Nested selectors

**Do not nest selectors more than three levels deep!**

```scss
.page-container {
  .content {
    .profile {
      // STOP!
    }
  }
}
```

When selectors become this long, you're likely writing CSS that is:

* Strongly coupled to the HTML (fragile) *—OR—*
* Overly specific (powerful) *—OR—*
* Not reusable


Again: **never nest ID selectors!**

If you must use an ID selector in the first place (and you should really try not to), they should never be nested. If you find yourself doing this, you need to revisit your markup, or figure out why such strong specificity is needed. If you are writing well formed HTML and CSS, you should **never** need to do this.

### Property order

In the interest of readability and continuity, the following property order should be utilized:

1. Extends
1. Positioning
2. Display & Box Model
3. Color
4. Typography
5. Misc
6. Mixins

```
.selector {
    // Extends
    @extend %transition-opacity;

    // Positioning
    position: absolute;
    z-index: 10;
    top: 0;
    right: 0;

    // Display & Box Model
    width: 100px;
    height: 100px;
    margin: 10px;
    padding: 10px;
    display: inline-block;
    overflow: hidden;
    box-sizing: border-box;
    border: 10px solid $white;

    // Color
    background: $black;
    color: $white

    // Typography
    font-family: sans-serif;
    font-size: 16px;
    line-height: 1.4;
    text-align: right;

     // Misc
     cursor: pointer;

     // Mixins
     @include hide-text;
}
```
