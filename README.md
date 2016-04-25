# Sprinklr Sass Styleguide

## Table of Contents

  1. [SASS](#sass)
    - [Syntax](#syntax)
    - [Internal Order](#internal-order)
    - [Ordering of property declarations](#ordering-of-property-declarations)
    - [Variables](#variables)
    - [Mixins](#mixins)
    - [Placeholders](#placeholders)

### Syntax

* Use the .scss syntax, never the original .sass syntax
* Order your `@extend`, regular CSS and `@include` declarations logically (see below)

### Internal order of a .scss file

1. Imports
2. Variables
3. Base Styles


```
#!scss

/* ==========================================================================
   Container
   ========================================================================== */
@import "../bootstrap/variables";
@import "../bootstrap/mixins";

$container-padding: 1.5rem;

.container {
  // ...
}
```


### Ordering of property declarations

#### 1. `@extend` declarations

Just as in other OOP languages, it's helpful to know right away that this “class” inherits from another.

```
#!css
.btn-green {
  @extend %btn;
  // ...
}
```

#### 2. Property declarations

Now list all standard property declarations, anything that isn't an `@extend`, `@include`, or a nested selector.

```
#!css
.btn-green {
  @extend %btn;
  background: #53c182;
  font-weight: 500;
  // ...
}
```

#### 3. `@include` declarations

Grouping `@include`s at the end makes it easier to read the entire selector, and it also visually separates them from `@extend`s.

```
#!css
.btn-green {
  @extend %btn;
  background: #53c182;
  font-weight: 500;
  @include transition(background 0.5s ease);
  // ...
}
```

#### 4. Pseudo-elements

This includes `::after`, `::before`, `::selection`, etc.

#### 5. Pseudo-selectors

This includes `:hover`, `:focus`, `:active`, etc.

#### 6. Modifier classes

#### 7. Nested selectors

Nested selectors, _if necessary_, go last, and nothing goes after them. Add whitespace between your rule declarations and nested selectors, as well as between adjacent nested selectors. Apply the same guidelines as above to your nested selectors.

#### 8. Media Queries

*Example*

```
#!scss

.btn {
  @extend %btn;
  background: #fafafa;
  font-weight: 500;
  @include transition(background 0.5s ease);

  &::before {
    content: '';
  }

  &:focus, 
  &:hover {
    box-shadow: 0 0 0 .1rem #ccc;
  }

  &--default {
    padding: .5rem 1rem;
  }

  .btn__icon {
    margin-right: 1rem;
  }

  @media (max-width: 767px) {
    //..
  }
}
```

Do not nest selectors more than three levels deep!

```
#!scss
.page-container {
  .content {
    .profile {
      /* STOP! */
    }
  }
}
```

When selectors become this long, you're likely writing CSS that is:

* Strongly coupled to the HTML (fragile) *—OR—*
* Overly specific (powerful) *—OR—*
* Not reusable

**Never nest ID selectors!**

If you must use an ID selector in the first place (and you should really try not to), they should never be nested. If you find yourself doing this, you need to revisit your markup, or figure out why such strong specificity is needed. If you are writing well formed HTML and CSS, you should *never* need to do this.

### Variables

Prefer dash-cased variable names (e.g. `$foo-variable`) over camelCased or snake_cased variable names.


### Mixins

Mixins, defined via `@mixin` and called with `@include`, should be used sparingly and only when function arguments are necessary. A mixin without function arguments (i.e. `@mixin hidden { display: none; }`) is better accomplished using a placeholder selector (see below) in order to prevent code duplication.

#### Argument-Less Mixins

Sometimes mixins are used only to avoid repeating the same group of declarations over and over again, yet do not need any parameter or have sensible enough defaults so that we don’t necessarily have to pass arguments.

In such cases, we can safely omit the parentheses when calling them. The `@include` keyword (or + sign in indented-syntax) already acts as a indicator that the line is a mixin call; there is no need for extra parentheses here.

*Bad*

```
#!scss
.foo {
  @include text-overflow();
}
```

*Good*

```
#!scss
.foo {
  @include text-overflow;
}
```

### Placeholders

Placeholders in Sass, defined via `%selector` and used with `@extend`, are a way of defining rule declarations that aren't automatically output in your compiled stylesheet. Instead, other selectors “inherit” from the placeholder, and the relevant selectors are copied to the point in the stylesheet where the placeholder is defined. This is best illustrated with the example below.

Placeholders are powerful but easy to abuse, especially when combined with nested selectors. **As a rule of thumb, avoid creating placeholders with nested rule declarations, or calling `@extend` inside nested selectors.** Placeholders are great for simple inheritance, but can easily result in the accidental creation of additional selectors without paying close attention to how and where they are used.

*Sass*

```
#!scss
/* Unless we call `@extend %icon` these properties won't be compiled! */
%icon {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
}

.icon-error {
  @extend %icon;
  color: #ed775a;
}

.icon-success {
  @extend %icon;
  color: #53c182;
}
```

*CSS*

```css
.icon-error,
.icon-success {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
}

.icon-error {
  color: #ed775a;
}

.icon-success {
  color: #53c182;
}
```
