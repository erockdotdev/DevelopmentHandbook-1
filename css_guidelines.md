This is a work in progress document for the CSS/SASS best practices.

## SCSS/SASS guidelines
It's easy to get a site up with no style guide lines - but eventually cleanly adding sections, changing colors, using other browsers or rendering responsive views becomes a nightmare.

By imposing a little bit of structure and some best practices we can reduce the tangled style rules that make pages difficult to manage and update.


## The Style Guide
Generally we have a variables sass file that where we set some rules for colors that are reused throughout the site.

This is a great practice but by extending the purpose of variables file by adding a few rules that are generally defined else where we will one place that defines consistent site wide attributes.

The basic properties of a style guide are colors, fonts, breakpoints, asset sizes (get some feed back if this is necessary but I think it should be - because we should have fixed asset sizes, with the exception being if we want to increase or reduce a section based on asset height - right now its the other way around and this helps impose design or client to use correctly sized images).

// Include examples here

This might go without saying but this means that a developer should never use these properties without referencing the variables in the style guide. So no hardcoded colors, etc.


# Cross Browser Styling - Proactive and Reactive approaches

## Cross Browser Mixins

Be proactive - There are numerous properties that require vendor prefixes to work across browsers. Often these are not addressed until we move to QA
Here is an excellent library we can use that will help us achieve cross browser compatibility.
https://github.com/matthieua/Sass-css3-mixins

## Normalize CSS
Be reactive - Cross browser issues don't only occur because of a lack of vendor prefixes. Some browsers not only vary in how they interpret rules, but also inject their own rules on certain elements ( looking at you safari :/).
Normalize its a great base for your css rules to work as expected across various browsers
https://necolas.github.io/normalize.css/8.0.1/normalize.css

Note: Normalize CSS in included with bootstrap - so if you are using Bootstrap (as most projects call for) then you can skip this step.


# What about our CSS
Now that we have the basic style guide lines covered what about our css?

## Scope
On of the biggest issues we've faced is lack of scope for a component or page. Which leads to the "short blanket" problem where you fix one section and it break another. That section gets fixed and now the other section breaks.

As a rule every component (or template) should have a top level class name for that component. The convention I like to follow is `className="component-<COMPONENT_NAME>" or class="component-<COMPONENT_NAME>"`

For example an event component would look like `<div className="component-event">...</div>`

this gives us the ability to scope all SCSS rules to this and only this section
example:

```
.component-event {
  .event-header {
    margin: 0 10px;
    border: solid $grey 1px;
    background-color: $blue;
  }
  ...
}

```

The same would apply for top level page views where you call in each component/template allowing for finer control over components per page view ( but we don't want to over do this as we'll discuss later ).


There are essentially two primary ways of of importing SASS files. Importing to the component and importing into a main.scss.

In a React project it would be best to create your style sheet adjacent to the component code to and import the style to the component.

ex.


in header.scss

```
.component-header {
  h1 {
    background: $black;
    color: $white;
  }
}
```

in header.js

```
import React from 'react';
import './header.scss';

export default (props)=>(
  <div className="component-header">
    <h1> THIS IS THE HEADER </h1>
  </div>
  )
```

Alternatively you have the option of separating out style sheets and using an sass import to include them in one main style sheet.

ex.

in stylesheets/header.scss

```
.component-header {
  h1 {
    background: $black;
    color: $white;
  }
}
```

in main.scss

```
@import './header.scss';
// include all additional style sheets in this form

```

This method is NOT preferred and useful only in non react projects.
Using this style of import does not enforce scoping styles per component.




## Bootstrap and Responsive design
Generally speaking we've been using bootstrap on out projects to help build our responsive layouts.

There are few common practices that we want to avoid.

Overwriting default values.
There is a place for overwriting bootstrap values but generally this should be avoided directly.
When an HTML element has no other class but a bootstrap class its tempting to use that as selector

ex.
```
col-md-6 {
  padding: 0;
}
```

This parred with bad scope can have some serious unintended consequences, and even if scoped can break the component/template styles.

It's a much better practice to add an additional class name to the element and target that for your changes (event if its to overwrite the bootstrap class).

Speaking of columns - They make for a clean lay out for a row but have the intentional functionality of breaking the cell vertically at different breakpoints. DO NOT USE bootstrap columns unless you want this feature. otherwise you'll have odd breaks when resizing the browser.

### Overwriting bootstrap classes
If you need to over write bootstrap class globally create a `bootstrap-overwrite.scss` and target the bootstrap classes with new values.
(This is a good practice even if you are not using a CDN and load the bootstrap stylesheet classes locally).


# Some other notes

Use hyphen case when naming classes ( not camel case or under scores )

example `className="component-blog-post"`

Avoid "magic numbers"
If you find youself using a rule like `margin-left: -37px` then that is a huge flag that you need to refactor some rules around those elements.

Avoind inline styles
In a react project the only time you should ever see inline styles are when the sheets are changes programmatically for example a height property is based on a state value in react.

Avoid using !important
If the style rules are scoped properly and no inline styles are used, then there should not be an instance where !important is needed.
If there is an instance where a style absolutely needs to be over written then you should use a more specific selector see (this)[https://www.smashingmagazine.com/2007/07/css-specificity-things-you-should-know/] article about specificity.


Comment style stylesheets
Sometimes odd things happen and you have to use an unusual rule - if thats the case please leave comments explaining why.

Formatting
** speak to About ES Lint and Prettier

Using rules with low support.
If you are given a unique situation and need to find a css rule that is new to you use (can I use )[https://caniuse.com/] to check its support.
if for example a rule has no support on firefox then it not worth using at this point at all.


###Pixel Perfect translations.


You should check margins, colors font family, weight, size and so on form then mocks.

There might be instance where the same section appears slightly different through out the mocks. This is a opportunity to speak to design and find out if a noted difference is an oversight or a variant of the similar section.

Some times tablet versions of sections go beyond what media queries can handle and these times where its better to hide/show alternative components than try to sacrifice design and create confusing or otherwise awkward css rules.


## Styled components

As a rule we wont enforce styled components over using style sheets however by default they solve two of our  issues
Cross browser vendor prefixes and component scoping...