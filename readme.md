**Front-end code guidelines**
**Work in progress**
**Writing better CSS**
------------------
There are a few things to keep in mind when writing *HTML* and *CSS* for applications at NLS. 

----------

Terminology
--

Rule declaration
---

A “rule declaration” is the name given to a selector (or a group of selectors) with an accompanying group of properties. Here's an example:

```css
.listing {
  font-size: 18px;
  line-height: 1.2;
}
```

Selectors
---

In a rule declaration, “selectors” are the bits that determine which elements in the DOM tree will be styled by the defined properties. Selectors can match HTML elements, as well as an element's class, ID, or any of its attributes. Here are some examples of selectors:

```css
.my-element-class {
  /* ... */
}

[aria-hidden] {
  /* ... */
}
```

Properties
---

Finally, properties are what give the selected elements of a rule declaration their style. Properties are key-value pairs, and a rule declaration can contain one or more property declarations. Property declarations look like this:

```css
/* some selector */ {
  background: #f1f1f1;
  color: #333;
}
```

## CSS

### Formatting

* Use soft tabs (2 spaces) for indentation
* Prefer dashes over camelCasing in class names.
  - Underscores and PascalCasing are okay if you are using BEM (see [OOCSS and BEM](#oocss-and-bem) below).
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
.no, .nope, .not_good {
    // ...
}
#lol-no {
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

### OOCSS and BEM

We encourage some combination of OOCSS and BEM for these reasons:

  * It helps create clear, strict relationships between CSS and HTML
  * It helps us create reusable, composable components
  * It allows for less nesting and lower specificity
  * It helps in building scalable stylesheets

**OOCSS**, or “Object Oriented CSS”, is an approach for writing CSS that encourages you to think about your stylesheets as a collection of “objects”: reusable, repeatable snippets that can be used independently throughout a website.

  * Nicole Sullivan's [OOCSS wiki](https://github.com/stubbornella/oocss/wiki)
  * Smashing Magazine's [Introduction to OOCSS](http://www.smashingmagazine.com/2011/12/12/an-introduction-to-object-oriented-css-oocss/)

**BEM**, or “Block-Element-Modifier”, is a _naming convention_ for classes in HTML and CSS. It was originally developed by Yandex with large codebases and scalability in mind, and can serve as a solid set of guidelines for implementing OOCSS.

  * CSS Trick's [BEM 101](https://css-tricks.com/bem-101/)
  * Harry Roberts' [introduction to BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)

We recommend a variant of BEM with PascalCased “blocks”, which works particularly well when combined with components (e.g. React). Underscores and dashes are still used for modifiers and children.

**Example**

```jsx
// ListingCard.jsx
function ListingCard() {
  return (
    <article class="ListingCard ListingCard--featured">

      <h1 class="ListingCard__title">Adorable 2BR in the sunny Mission</h1>

      <div class="ListingCard__content">
        <p>Vestibulum id ligula porta felis euismod semper.</p>
      </div>

    </article>
  );
}
```

```css
/* ListingCard.css */
.ListingCard { }
.ListingCard--featured { }
.ListingCard__title { }
.ListingCard__content { }
```

  * `.ListingCard` is the “block” and represents the higher-level component
  * `.ListingCard__title` is an “element” and represents a descendant of `.ListingCard` that helps compose the block as a whole.
  * `.ListingCard--featured` is a “modifier” and represents a different state or variation on the `.ListingCard` block.

Namespaces
----------

Optional, but a nice way for us to write code that is transparent and self-documenting.

The following are the namespaces that everyone should consider using. Note that these are class-names, and not IDs. As we should generally avoid using IDs.

`u-, h-`: Signify that something is a **Utility** (or **helper**) class.
Common utility classes are classes that add margin, padding, float or clearfixes.

`is-, has-`: Signify that something is styled this particular way because of a **state** or a **condition**. Use these classes for temporary, optional, or short-lived states and styles.
For example: is-hidden, is-expanded

`t-`: Signify that something is style this particular way because it is a part of a **Theme**.
Pages with unique styles or overrides for any objects or components should make use of theme classes.
For example: Using t-xmas on the body might enable a christmas theme on a page.

`js-`: A hook for **JavaScript**. Can be used as an ID instead of class. Don’t attach styles to this.


In practice this can look something like:

```
    <div class="c-slider" id="js-slider">
    	 <div class="c-slider__image"></div>
    	 <div class="c-slider__image is-hidden"></div>
    	 <div class="c-slider__image is-hidden"></div>
    </div>
 ```


Note that you shouldn't nest BEM classes

:+1:`<p class="w_wallet__balance">` 

:-1:`<p class="w_wallet__content__balance">`

We want the styles behind `&__balance` to apply regarding of how the wallet’s DOM is structured.

Sass
--

Syntax
---

* Use the `.scss` syntax, never the original `.sass` syntax
* Order your regular CSS and `@include` declarations logically (see below)

Ordering of property declarations
---

1. Property declarations

    List all standard property declarations, anything that isn't an `@include` or a nested selector.

    ```scss
    .btn {
      background: green;
      font-weight: bold;
      // ...
    }
    ```

2. `@include` declarations

    Grouping `@include`s at the end makes it easier to read the entire selector.

    ```scss
    .btn {
      background: green;
      font-weight: bold;
      @include transition(background 0.5s ease);
      // ...
    }
    ```

3. Nested selectors

    Nested selectors, _if necessary_, go last, and nothing goes after them. Add whitespace between your rule declarations and nested selectors, as well as between adjacent nested selectors. Apply the same guidelines as above to your nested selectors.

    ```scss
    .btn {
      background: green;
      font-weight: bold;
      @include transition(background 0.5s ease);

      .icon {
        margin-right: 10px;
      }
    }
    ```

Variables
---

Prefer dash-cased variable names (e.g. `$my-variable`) over camelCased or snake_cased variable names. It is acceptable to prefix variable names that are intended to be used only within the same file with an underscore (e.g. `$_my-variable`).

Mixins
---

Mixins should be used to DRY up your code, add clarity, or abstract complexity--in much the same way as well-named functions. Mixins that accept no arguments can be useful for this, but note that if you are not compressing your payload (e.g. gzip), this may contribute to unnecessary code duplication in the resulting styles.

Extend directive
---

`@extend` should be avoided because it has unintuitive and potentially dangerous behavior, especially when used with nested selectors. Even extending top-level placeholder selectors can cause problems if the order of selectors ends up changing later (e.g. if they are in other files and the order the files are loaded shifts). Gzipping should handle most of the savings you would have gained by using `@extend`, and you can DRY up your stylesheets nicely with mixins.

Nested selectors
---

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

Project structure
--

keep a good overview over the project.

Most projects differ from one another but a good project structure could be like this:


    ├───Styles
    │   ├───Layout
    │   │   └───_header.scss
    │   │   └───_section.scss
    │   │   └───_footer.scss
    │   │   └───_article.scss
    │   ├───Components
    │   │   └───_buttons.scss
    │   │   └───_slider.scss
    │   │   └───_banner.scss
    │   │   └───_cart.scss
    │   │   └───_profile.scss
    │   ├───Core
    │   │   └───_reset.scss
    │   │   └───_typography.scss
    │   ├───Environment
    │   │   └───_config.scss
    │   │   └───_mixins.scss
    │   │   └───_functions.scss
    │   ├───Vendor
    │   │   └───_that-plugin.scss


Writing maintainable CSS
-----------------

You should always aim to write styles that can be easily understood by other developers.
Begin your files with a quick description of what you are doing and how others can use it.

If the partial stylesheet in question uses colors and/or typography values that are different from the rest of the application it is recommended that you use local variables; see point 3 below.
That way you don’t have to pollute your global config with inappropriate values, and you still have an easy access to modifying the values that matter.
If a property needs explaining, add comments to the bottom of the file and make a reference to it.

Note that you should be writing consistent CSS, ideally by using a linter (see Tools section.)
Consistent rule ordering helps the server a lot with gzip.

Responsive Web Design
-----------------

Generally you should create 3 main breakpoints; large, medium, and small -- so it roughly corresponds to desktop, tablet and mobile -- and break your layout down accordingly.

You should aim to make your layout mobile-first and fluid so it scales up naturally. When you need to shift the layout drastically you use the above device targetted breakpoints. When the content and/or design breaks we’ll add a new breakpoint and go from there. Try to keep those custom breakpoints to a mininum. Component-specific breakpoints should be made as local variables.

Conflict free CSS
-----------------

The best way to prevent CSS conflicts in large web applications (besides using iframes) is to wrap our style with your or your team’s prefix. This is also called namespacing, not to be confused with the modular namespacing method that we covered before.
An example of a namespaced widget could be something like this

    // ***
    // * This widget is amazing
    // ***
    
    .nls-rp {
    
      @import "something-else";
    
      .container {
        margin: 0 auto;
        border-radius: 2px;
        color: color(_white);
        overflow-x: hidden;
      }
    
      .login-button {
        min-width: 50%;
        padding: .5em 1em;
        transition: box-shadow, color, background .2s;
        border: 1px solid;
        border-color: color(_lightgray);
        background: color(_fancy);
        box-shadow: 0 2px 0 0 currentColor;
      }
    
      .footer {
        position: relative;
        height: rem(280px);
        padding: 0;
        background color(_blue);
      }
    }

This way our styles will never affect the hosting page’s styles. Unfortunately, this doesn’t prevent other stylesheets from leaking into ours so the host page has to have its styles namespaced as well.

Units of measurement
====================

Using pixel values in CSS is fine, but when making a responsive web application that’s rich of text consider using ems and/or rems.

In typography, em is roughly the size of the letter M. In CSS it can be thought of as “the currently specified font-size”. If an element has the font-size of 20px, 1 em is 20px, and 1.5 ems are 30px.  It is popular in web design because it allows the user to zoom in and out on the web page without breaking the design.

CSS also has rem, which is the root em. CSS has a :root selector. Set the :root to 16px (most browser's default size) and a rem will always be equal to 16px. If the :root is not set, it will use the html or body font-size.
