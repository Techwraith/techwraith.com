The fields of web design and web development are both converging on two sides of the same coin. Web designers are starting to develop responsive design patterns and element libraries to more easily create web pages and applications that can better adapt to user and product needs. Web developers are coming around to the idea of separating and modularizing their code to keep their API surfaces small and their systems interchangeable. These two approaches can be combined to create a wondrously productive design and development system. But first, lets talk a bit about how we got here.

In a traditional product development system, there are two groups: those who figure out what to build, and those who build it. It is common for these two groups to be separated by a layer of managers who shuttle messages back and forth. Here’s an example of what this looks like:

A product manager decides that a new feature needs to be built. They pull in a designer and ask them to design this new feature. Once they go back and forth a few times, the designer gives the product manager a final mockup. The product manager then gets a team of developers in a room, presents the mockups to them, and tells them to build it.

These developers take the design and write a bunch of brand new code. The feature gets plugged into the existing product, often times working around css inheritance creep and javascript api warts to jerry rig it in place. It remains in place for some amount of time until another project manager pulls another designer in to build a feature on top of this feature and the process starts all over again.

It is easy to see how this kind of system will lead to a “paint over it” mentality. When you don’t have a common style guide and defined roles for your modules, you end up with a mess.

There is a better way. At the very least designers can work together to create style guides for their elements and developers can work together to build modules that have well defined APIs. In fact this happens already.

Products on the web move quickly these days. Designers have to respond to their user's needs, their business' goals, and their developer's time. To respond to these forces effectively, designers often create style guides. These guides and pattern libraries allow designers to quickly build new features without having to re-create each element. Brad Frost recently proposed a new way of thinking about this process: "[Atomic Design](http://bradfrostweb.com/blog/post/atomic-web-design/)."

Atomic Design can be boiled down to a few core ideas. Atoms are the elements that your site uses (buttons, inputs, labels, etc.). These atoms can be brought together to form molecules (a search form, a comment box, etc.). In turn, these molecules can come together to form larger pieces - site wide page headers or even entire pages. These larger combinations can be put together to design an entire product.

Products aren’t just built from design systems. They also require a hefty amount of HTML, CSS, and (especially) JavaScript. Developers juggle these systems to breathe life into their designer’s mockups and flows. There are many approaches to keeping things on the front end organized. CSS frameworks like Bootstrap and Foundation give developers a system to stay within to keep things consistent. JS frameworks like YUI and JQuery UI give them reusable functionality and widgets.

These systems come with a price: your designer has to work within the confines of the system that your developer prefers.

When it comes to writing user interfaces, it is best to create a system of modules that can be combined to respond to user needs. A module with a well defined API will be easier to maintain and integrate with than a mess of spaghetti code.

For example, instead of adding yet another click handler to your “app.js” file, consider the ease of a reusable “button” module.

Fortunately, many new tools are coming out that allow developers to easily build and maintain modular systems on their own without relying on a design framework. Many open source tools help developers publish, build, and use modular pieces of UI. There are many package managers springing up for javascript and the front-end, but in terms of number of modules, there’s really only one: npm.

Many people think that npm can only be used for node modules, but as Max Ogden says, “[npm stands for ‘Node Packaged Module’](http://maxogden.com/node-packaged-modules.html)”. It turns out that with a few extra tools, npm works really well to manage front-end code - including HTML, CSS, and (of course) JavaScript.

Atomic design and modular development have a lot in common. They can be tied together to create a hybrid Design/Development system for modern web apps that doesn’t rely on external design systems or rigid development frameworks. I call this approach Atomic Development.

Atomic Development is a new way to design and build products. It builds upon atomic design and modular development to create a system that will make web apps easier to create, change, and maintain. Here’s the basics:

- Design and code your atoms first, then combine them into larger modules.
- Every module should know about the modules that it depends on.
- Modules can be combined to create other modules (Ex. - input + button = search form; search form + nav menu = page header)

By placing the design and the development so close together, it’s much easier for your design and development teams to work in tandem. You no longer have to lob something over a wall. Designers and developers work together, side by side, on new features, but there is enough modularity to allow for either side to change something without it effecting the other side. No more lobbing things over walls.

### Atomic Modules

There are a few tools that help make building front-end modules a little easier. Browserify allows the use of node style CommonJS modules on the front end, hbsfy does the same for handlebars templates, and rework-npm makes it easy for modules to include CSS.

These three tools are the foundation of an Atomic module.

### What is an Atomic Module?

An atomic module is really just a superset of a common-js module. It has a package.json to describe itself, a javascript file to describe its behaviors, a template file to describe its structure, and a css file to describe its style. If the module depends on any other modules, its package.json file lists them so that npm can go out and find them. If a module’s js file(s) need to use one of these dependencies, it uses the require() function. If a css file needs to use the styles of one of it’s dependencies, it uses the `@import` keyword.

It may be best to give an example. Lets create that search form that we were talking about earlier:

##### package.json

```json
{
  "name": "search-form",
  "version": "0.0.1",
  "main": "index.js",
  "style": "index.css",
  "dependencies": [
    "button": "0.1.0",
    "form-input": "0.2.1"
  ]
}
```

This isn’t much different than a standard node module’s package.json. You’ll notice that we are including a style property. This is what tells other modules what file to use for it’s css.

##### search-form.html.hbs

```hbs
<label>{{title}}</label>
<div class="input-target"></div>
<div class="button-target"></div>
```

This is a pretty standard little handlebars template. You’ll see what we’re going to do with the target divs once we move onto the javascript portion.

##### index.js

```javascript
var $ = require('jquery-browserify')
  , Button = require('button')
  , FormInput = require('form-input')
  , template = require('search-form.html.hbs')

var SearchForm = function (opts) {

  // render template
  var html = template({title: opts.title})
  this.$el = $(opts.target)
  this.$el.html(html)
  this.$el.addClass('search-form')

  // create the atoms that we'll use
  this.input = new FormInput()
  this.button = new Button({
    label: 'Search'
  , action: function () { // do something }
  })

  // append these atoms to our template in the right place
  this.$el.find('.input-target').append(this.input.$el)
  this.$el.find('.button-target').append(this.button.$el)

  return this
}

module.exports = SearchForm
```

This is where most of the magic happens. We require the dependencies that our search form has - button, and form-input- and we require our handlebars template (we can just require the template itself if we’re using hbsfy).

From there we just write a bit of code to render everything and put it on the page. We then export the function we just wrote so that any module (or entry file) that requires this module can use it. This is just a simple example, but you could use any view-ish library you’d like- Backbone, ember, angular, etc.

##### index.css

```css
@import "form-input";
@import "button";

.search-form label {}
.search-form input {}
.search-form button {}
```

This file will probably take the most explaining. Basically, we’re using CSS’s `@import` rule just like we use node’s require() function.

To get a more in depth explanation of what’s going on here, go read “Your CSS needs a dependency graph”.

#### Turning Atoms into Apps

Now, your modern app has more than just one module in it. If you’re going to be creating multiple atoms and combining them together into even more complex modules though, you’re going to need to be able to build all this code into a single javascript file and a single css file so that you can load those into your app on the browser.

For the javascript and handlebars side, we can just use Browserify and hbsfy to build a bundle for us. It will traverse our require tree and create a file with all of the necessary modules included.

The CSS side can be handled with Rework and rework-npm. Rework is a tool that allows you to build your own css pre-processor, and if you combine it with rework-npm, you get a great node-require-like css concatenation process.

#### Atomify

When you look at it all together like this, it seems a bit overwhelming. That’s why I created Atomify.

Atomify is basically just some glue code around the libraries that we covered in this article. It allows you to easily bundle your atomic modules together to create a single js and single css file for your app.

In my next post about Atomify, I’ll walk you through building out a simple app using Atomic Design and Development.

As always, feedback is appreciated: contact me on [Twitter](https://twitter.com/techwraith) or leave an issue on [Github](https://github.com/atomify/atomify).
