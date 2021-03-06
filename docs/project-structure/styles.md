# CSS structure

## One component per file

?> 💬 In BEM, every class selector is either the block name itself or uses the block name as a prefix, and the rules for each block live in their own dedicated file. Since file systems do not allow two files to have the same name, the OS is actually helping to prevent accidental duplication. If you follow all of the BEM naming conventions, and you ensure all block code resides in its own file, you will never have naming collisions. - [Philip Walton article on the side effects of css](https://philipwalton.com/articles/side-effects-in-css/)

  * filename matches the component name.
  * breakpoint variations also have their own file.

  ```scss
  /* ./components/search/search-form.scss */
  .search-form { /* ... */ }
    .search-form__button { /* ... */ }
    .search-form__field { /* ... */ }
    .search-form__label { /* ... */ }

    // variants
    .search-form--small { /* ... */ }
    .search-form--wide { /* ... */ }
  ```

## One media query per file
Component styles for different media queries have separate files.

```scss
.search-form {
  width: 100%;

  @media breakpoint-gt(#{$small}) { /* ✖️ Bad */
    width: rem-calc(256px);
  }
}
```

  Instead keep styles for each breakpoint in a separate file, repeating only just enough in order to override rules from small screen files.

  * `./components/search/search-form` : all sizes, small up **required**
  * `./components/search/search-form--smallonly`: small only
  * `./components/search/search-form--largeup`: large up
  * `./components/search/search-form--largeup-portrait`: large up, but only portrait
  * `./components/search/search-form--print`: print styles

## Use media queries at the component root level
Component screen and print media queries are bundled together in the root of the component folder:

```bash
 components/
    search/
        screen.scss
        print.scss
```

In scss it looks like:

  ```scss
  // ./components/screen.scss
  @import './search-form/screen';
  ```

In your `./component/search-form/screen.scss` file, it looks like:

```scss
@import "./search-form";

@media #{breakpoint-lte(small)} { // less than or equal to small topend
    @import "./search-form--smallonly";
}

@media #{breakpoint-gt(small)} { // greater than small topend
    @import "./search-form--mediumup";
}
```

## Print styles separate from screen styles

Components should a component level  `print.scss` file:

```scss
// ./components/search-form/print.scss
@media print {
    @import "./search-form--print";
}
```

Then if we're using a root `print.scss`:

```scss
@import "./components/search-form/search-form--print";
```

Otherwise we'll lazyload the print style with either `System.import` or using it as an entrypoint.

## Avoid over-nesting
Use no more than 1 level of nesting. It's easy to get lost with too much nesting.

  ```scss
  /* ✖️ Avoid: 3 levels of nesting */
  .image-frame {
    > .description {
      /* ... */

      > .icon {
        /* ... */
      }
    }
  }
  ```

**What's wrong?**

This creates problems later in a projects life (or for other project team members).
Maintenance is usually performed long after the mental model of the project has left your mind, or in fact it was never there.

Consider the steps to identify where such code could be found amongst the codebase:

* a search for `.image-frame > .description > .icon` results in zero matches.
* searching for `.description` results in far too many false matches.
* searching for `.icon` is now soaking up precious time and mental energy.


## Be more explicit

```scss
/* 🆗 OK: 2 levels */
.image-frame {
  .image-frame__description { /* ... */ }
  .image-frame__description-icon { /* ... */ }
}
```

```scss
/* ✔️ Idea: 1 level */
.image-frame { /* ... */ }
  .image-frame__description { /* ... */ }
  .image-frame__description-icon { /* ... */ }
```

When we use BEM, then often a css rule becomes very obvious in its purpose.

Consider the following same scenarios from above:

* A search for `.image-frame`, reveals only a few files. If we are following the file naming convention set out here, one of those files should be an obvious choice.
* searching for `.image-frame__description-icon` is again even more specific.