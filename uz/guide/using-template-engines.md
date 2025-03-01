---
layout: page
title: Using template engines with Express
menu: guide
lang: uz
description: Discover how to integrate and use template engines like Pug, Handlebars,
  and EJS with Express.js to render dynamic HTML pages efficiently.
---

# Using template engines with Express

Before Express can render template files, the following application settings have to be set.

* `views`, the directory where the template files are located. Eg: `app.set('views', './views')`
* `view engine`, the template engine to use. Eg: `app.set('view engine', 'pug')`

Then install the corresponding template engine npm package.

```bash
$ npm install pug --save
```

<div class="doc-box doc-notice" markdown="1">
Express-compliant template engines such as Pug, export a function named `__express(filePath, options, callback)`, which is called by `res.render()` to render the template code.

Some template engines do not follow this convention, the [Consolidate.js](https://www.npmjs.org/package/consolidate) library was created to map all of node's popular template engines to follow this convention, thus allowing them to work seamlessly within Express.
</div>

Once the view engine is set, you don't have to explicitly specify the engine or load the template engine module in your app, Express loads it internally as shown below, for the example above.

```js
app.set('view engine', 'pug')
```

Create a Pug template files named "index.pug" in the views directory, with the following content.

```pug
html
  head
    title= title
  body
    h1= message
```

Then create a route to render the "index.pug" file. If the `view engine` property is not set, you will have to specify the extension of the view file, else you can omit it.

```js
app.get('/', (req, res) => {
  res.render('index', { title: 'Hey', message: 'Hello there!' })
})
```

On making a request to the home page, "index.pug" will be rendered as HTML.

To better understand how template engines work in Express, read ["Developing template engines for Express"](/{{ page.lang }}/advanced/developing-template-engines.html).
