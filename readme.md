**Front-end code guidelines**
**Work in progress**
**Writing better CSS**
------------------
There are a few things to keep in mind when writing *HTML* and *CSS* for applications at NLS. 

----------

BEM
---

 meaning block, element, modifier is the naming methodology we use.

The naming convention follows this pattern:

    .block {...} 
    .block__element {...}
    .block__element--modifier {...}

The look of it might throw you off, but this convention helps solve the biggest problem we encounter with CSS, the lack of namespaces/scope and that every rule applies globally. BEM helps us modularize our styles so that we can prevent naming conflicts and specificity wars. It also provides a good object oriented structure with a familiar terminology and is simple enough to not get in your way. Last but not least, it gives developers a good overview over what each class in the DOM is responsible for.

BEM Best practices
----------

Don't `@extend` block modifiers with the block base.
- Good: `<div class="my-block my-block--modifier">`
- Bad: `<div class="my-block--modifier">`

Don't create elements inside elements. If you find yourself needing this, consider converting your element into a block.
- Bad: `.alert-box__close__button`

Choose your modifiers wisely. These two rules have very different meaning:

```scss
.block--modifier .block__element { color: red; }
.block__element--modifier { color: red; }
```


More examples of how BEM is written and structured can be seen throughout this document.

Namespaces
----------

Optional, but a nice way for us to write code that is transparent and self-documenting.

The following are the namespaces that everyone should consider using. Note that these are class-names, and not IDs. As we should generally avoid using IDs.

`o-`: Signify that something is an **Object**. Objects are usually common design patterns (like the Flag object). Modifying these classes could have severe knock-on effects.

`c-, w-`: Signify that something is a **Component** (or a **Widget**). This is a concrete, implementation-specific piece of UI.

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
and
``` 
    <div class="w-wallet" id="js-wallet">
      <div class="w-wallet__header">
        <h1>Wallet</h1>
        <p class="w_wallet__welcome-msg"> <!--#1-->
          Welcome back, user.
        </p>
      </div>
      <div class="w-wallet__content">
        <p class="w-wallet__balance">
          Your available balance is 
          <span class="w-wallet__amount">€74</span>
        </p>
        <button class="button--green w-wallet__deposit-button">Deposit</button> <!--#2-->
        <button class="button--default is-disabled w-wallet__withdraw-button">Withdraw</button>
      </div>
    </div>
```

Note that you shouldn't nest BEM classes

:+1:`<p class="w_wallet__balance">` 

:-1:`<p class="w_wallet__content__balance">`

We want the styles behind `&__balance` to apply regarding of how the wallet’s DOM is structured.

Project structure
-----------------

 keep a good overview over the project.

Most projects differ from one another but a good project structure could be like this:


    ├───Styles
    │   ├───Layout
    │   │   └───_header.scss
    │   │   └───_section.scss
    │   │   └───_footer.scss
    │   │   └───_article.scss
    │   ├───Components
    │   │   └───_slider.scss
    │   │   └───_banner.scss
    │   │   └───_cart.scss
    │   │   └───_profile.scss
    │   ├───Core
    │   │   └───_reset.scss
    │   │   └───_buttons.scss
    │   │   └───_typography.scss
    │   ├───Environment
    │   │   └───_config.scss
    │   │   └───_mixins.scss
    │   │   └───_functions.scss
    │   ├───Vendor
    │   │   └───_that-plugin.scss


Which is then imported by a master file like so:


    // * Setup environment
    @import "environment/global-config";
    @import "environment/functions";
    @import "environment/vars";
    @import "environment/mixins";
    
    // * Core
    @import "core/typography";
    @import "core/reset";
    
    // * Layout
    @import "layout/header";
    @import "layout/footer";
    @import "layout/section";
    @import "layout/article";
    
    // * Components
    @import "components/button";
    @import "components/checkbox";
    @import "components/slider";
    @import "components/banner";
    
    // * Widgets
    @import "widgets/cart";
    @import "widgets/profile";
    @import "widgets/converter"
    
    // * Vendor (3rd party)
    @import "vendor/that-plugin";


As you can see, importing each file instead of globbing `(*/**)` provides a much better overview of the project.

Writing maintainable CSS
-----------------

You should always aim to write styles that can be easily understood by other developers.
Begin your files with a quick description of what you are doing and how others can use it.

If the partial stylesheet in question uses colors and/or typography values that are different from the rest of the application it is recommended that you use local variables; see point 3 below.
That way you don’t have to pollute your global config with inappropriate values, and you still have an easy access to modifying the values that matter.
If a property needs explaining, add comments to the bottom of the file and make a reference to it.

Note that you should be writing consistent CSS, ideally by using a linter (see Tools section.)
Consistent rule ordering helps the server a lot with gzip.


Here’s an example of a component that is properly maintained.


    // ***
    // * Component: Slider
    // * This component includes all you need to style our front-page slider.
    // * There is a dark variation available; see --dark modifier.
    // *** ← #1
    
    .c-slider { //← #2
    
        $border-radius: 30px; //← #3
        $shadow-left: #FF9C00;
        $shadow-left--dark: #495056;
        $text-color: #333;
        $text-color--dark: #F2F2F2;
        $caption-text-color: #444;
        $caption-text-color--dark: #F3F3F3;
        $text-shadow: #F80;
        $text-shadow--dark: #123213;
        $gradientFrom: #FFF500;
        $gradientTo: #FFBC00;
        $gradientFrom--dark: #345123;
        $gradientTo--dark: #331232;
    
      width: 100%;
      height: 250px;
      margin: auto;
      overflow: hidden;
      &__slide { //← #4
        border-radius: $border-radius 0 0 $border-radius;
        background: linear-gradient($gradientFrom, $gradientTo);
        color: $text-color;
        text-shadow: 1px 1px 1px $text-shadow;
        will-change: transform; //[1] //← #5
        .c-slider--dark & { //← #6
          background: linear-gradient($gradientFrom--dark, $gradientTo--dark);
          color: $text-color--dark;
          text-shadow: 1px 1px 1px $text-shadow--dark;
        }
      }
      &__caption {
        border-radius: 0 $border-radius $border-radius 0;
        background: $caption-text-color;
        font-size: 1.2em;
        font-weight: 700;
        text-align: center;
        .c-slider--dark & {
          color: $caption-text-color--dark;
        }
      }
    }
    //[1]: We are animating transform properties on the slider with JavaScript, but we are telling the browser to prepare for the animation with the "will-change" property.*/

Note a few things:
There’s a brief description that explains what the component is.
The component is scoped with its class name...
… making its variables local to that parent class.
Using &__ in a selector we take advantage of Sass’s support for the BEM syntax. Nesting &__stuff compiles to Block__stuff.
When doing something that might not be obvious to other developers we add comments to the bottom of the document and reference them with a //[#] next to the declaration.
Using an ampersand (&) after a selector brings the selector in front of the other previous selectors making it useful for modifiers. The selector in question compiles to .c-slider--dark .c-slider__slide.

Conflict free CSS
-----------------

The best way to prevent CSS conflicts in large web applications (besides using iframes) is to wrap our style with your or your team’s prefix. This is also called namespacing, not to be confused with the modular namespacing method that we covered before.
An example of a namespaced widget, from an imaginary NLS team we’ll call RP is as follows:

    // ***
    // * This widget is amazing
    // ***
    
    .nls-rp {
    
      @import "that-library";
    
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

This way our styles will never affect the hosting page’s styles. Unfortunately, this doesn’t prevent other stylesheets from leaking into ours, so the host page has to have its styles namespaced as well. Hopefully we’ll get the concept of scopes in CSS in the not-so-far-away future, but until then this is our solution.

Units of measurement
====================

Using pixel values in CSS is fine, but when making a responsive web application that’s rich of text consider using ems and/or rems.

In typography, em is roughly the size of the letter M. In CSS it can be thought of as “the currently specified font-size”. If an element has the font-size of 20px, 1 em is 20px, and 1.5 ems are 30px.  It is popular in web design because it allows the user to zoom in and out on the web page without breaking the design.

CSS also has rem, which is the root em. CSS has a :root selector. Set the :root to 16px and a rem will always be equal to 16px. If the :root is not set, it will use the html or body font-size.

If you want to take advantage of (r)ems but still use the trusty pixels as foundation, you can use a Sass function that converts it for you. See this function, among others at our repo.

SVG icons over icon-fonts
================================

Include the icons you need as SVG symbols inside a sprite file and reference them in your HTML.
The advantages SVG icons have over icon fonts are:

You can add/delete/modify your icons without having to build a new font file.
You can use multiple colors on a single icon.
You don’t need to include multiple versions (woff, ttf, otf…) of your icons which results in fewer HTTP requests.
Fonts were never meant for icons and are in essence a hack.

Using SVG for icons is easy. Let’s go over the process real quick.

Get your icons in a SVG format from your designer and open it up with your favorite IDE.
Let’s say we get two icons, a heart and a star. It will look something like this

> heart.svg

    <?xml version="1.0" encoding="iso-8859-1"?>
    <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 16 14.7">
    <path d="M11.4,0C10,0,8.8,0.6,8,1.5C7.2,0.6,6,0,4.6,0C2.1,0,0,2.2,0,4.8 c0,0.7,0.1,1.4,0.3,2.1c0,0,0.2,0.6,0.4,0.9C2.6,12,8,14.7,8,14.7s5.4-2.7,7.3-6.9c0,0,0.3-0.6,0.4-0.9C15.9,6.2,16,5.5,16,4.8
        C16,2.2,13.9,0,11.4,0z"/>
    </svg>

> star.svg

    <?xml version="1.0" encoding="iso-8859-1"?>
    <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 17.2 16.4">
      <polygon points="8.6,13.2 14,16.4 12.5,10.3 17.2,6.2 11,5.7 8.6,0 6.2,5.7 0,6.2 4.7,10.3 3.3,16.4"/>
    </svg>

The only thing we need is the path from the heart and the polygon from the star. The rest is boilerplate SVG markup for the browser. Take note of the viewBox, though.

We’ll create a *sprites.svg* file in our images/ folder and include our heart and star into it.

    <?xml version="1.0" encoding="iso-8859-1"?>
    <!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
    <svg version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" width="0" height="0" style="visibility: hidden;">
    <defs>  
      <polygon id="icon-star" points="8.6,13.2 14,16.4 12.5,10.3 17.2,6.2 11,5.7 8.6,0 6.2,5.7 0,6.2 4.7,10.3 3.3,16.4"/>
    
      <path id="icon-heart" d="M11.4,0C10,0,8.8,0.6,8,1.5C7.2,0.6,6,0,4.6,0C2.1,0,0,2.2,0,4.8
    c0,0.7,0.1,1.4,0.3,2.1c0,0,0.2,0.6,0.4,0.9C2.6,12,8,14.7,8,14.7s5.4-2.7,7.3-6.9c0,0,0.3-0.6,0.4-0.9C15.9,6.2,16,5.5,16,4.8
        C16,2.2,13.9,0,11.4,0z"/>
    </defs>
    
    </svg>

We give the SVG document a *height* and *width* of 0 and hide it from the view and stick our icons into a `<defs>` (definitions) tags, so they can be used via the <use> tag in our HTML. Finally we give them a ID that we can reference them with and their initial viewBox values. The viewBox is a unitless value that sets the aspect ratio of our rendered icon and gives the values inside our path and polygon some context to work with.

Inside our markup we can include our icons...

    <svg class="icon icon-star" viewBox="0 0 17.2 16.4">
        <use xlink:href="images/sprite.svg#icon-star"></use>
    </svg>
    
    <svg class="icon icon-heart" viewBox="0 0 16 14.7">
        <use xlink:href="images/sprite.svg#icon-heart"></use>
    </svg>

...and we’re done.

Responsive Web Design
=====================

Gone are the days where we could target a few devices and make them their own breakpoints. With hundreds of available screen resolutions we are forced to make our designs work on every possible size.

Generally, you should create 3 main breakpoints, large, medium, and small -- so it roughly corresponds to desktop, tablet and mobile -- and break your layout down accordingly. Beside those main breakpoints, you should aim to make your application fluid so it scales down naturally. When the content and/or design breaks we’ll add a breakpoint and go from there. Component-specific breakpoints should be made as local variables.

Sass has a handy library called *Sass-breakpoint* which is highly recommended. Read more about it in the Tools section below.


Tools
=====

There are so many tools available that can make our jobs easier when writing and maintaining CSS. Here we will introduce some of the tools that have been of the most value to us.

Autoprefixer [[Gulp]](https://github.com/sindresorhus/gulp-autoprefixer) [[Grunt]](https://github.com/nDmitry/grunt-autoprefixer)
Autoprefixer lets us skip the nonsense that CSS prefixes are by letting us write unprefixed code. Autoprefixer then uses the *caniuse.com* API to determine what prefixes are needed to satisfy your needs.

Sass (SCSS) [[.com]](http://sass-lang.com) 
We use the SCSS syntax of the Sass preprocessor to write our styles. Sass gives us huge benefits with configs, nesting, functions, BEM support, and much more. SCSS is then compiled to regular CSS.

SCSS Lint [[Sublime]](https://github.com/attenzione/SublimeLinter-scss-lint) [[Intellij]](https://github.com/idok/scss-lint-plugin) [[Atom]](https://github.com/idok/scss-lint-plugin)
This highly configurable IDE plugin highlights your styles as you write them, and tells you if it’s not according to the set standard.
You can find the NLS recommended config in [our repo](https://github.com/nlsdev/frontend/blob/master/.scss-lint.yml).

Sass Breakpoint [[.com]](http://breakpoint-sass.com/)
Sass Breakpoint allows us to easily use breakpoints inside our styles. Make a map/config of breakpoints variables and include the mixin, wrapping the code that should be affected. 

Susy [[.net]](http://susy.oddbird.net/)

> “In a world of agile development and
> super-tablet-multi-magic-laptop-phones, the best layouts can’t be
> contained in a single framework or technique. CSS Libraries are a
> bloated mess of opinions about how to do your job. Why let the
> table-saw tell you where to put the kitchen? Your markup, your design,
> your opinions | our math.”

 *--Susy website*

Susy is a CSS grid toolkit that’s built with Sass. It makes it easy to work with floated layouts. With Susy we don’t have to pollute our markup with layout utility classes, we simply handle it from within our styles.
