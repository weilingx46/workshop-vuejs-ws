# CS52 Workshop, 5/15/18:  Vue.js

![](img/Medium-Vue.png)

Vue.js is, quite simply, a framework for building user interfaces. Does that sound familiar?
It should, because that's the same thing that React does! In fact, you'll see Vue.js commonly
listed as an alternative to React or Angular. It's lightweight, fast, and
features components like React. Unlike React, Vue.js allows two way data binding
between your Javascript and the DOM.

## Overview

In this tutorial, we'll build a single page app that demonstrates the basic functionality
of Vue.js. We'll create a virtual punching bag that has its own health bar and splits 
open when it's done! Then we'll add a router so that you have a page with a Tim 
punching bag as well!

![](https://i.imgur.com/3eDkeRZ.gif)

## Setup

If we were building a large-scale app, we would install Vue.js via `npm`. 
But for our purposes, we can just include the link in our HTML! Add the below line at 
the bottom of your body tags in `index.html`:

```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```

## Step by Step

To get started, add the following HTML inside the body tags of `index.html`.
This is where our Vue component will mount within our application.

```html
<div id="main-app">
  {{ message }}
</div>
```

Now head over to `app.js` and add the following code:

```javascript
new Vue({
  // Note how Vue connects components--by their ID in the layout file
  // This is a little diferent from how they're imported and used in React
  el: '#main-app',
  data: {
    message: 'Hello CS 52!'
  }
});
```

Now run the following command and open up [](`http://localhost:8000) in your browser:

```python
python -m SimpleHTTPServer
```

Voila! We have our first Vue.js component. The `data` property has all of the stuff available to 
our component to render within our app. Notice how it has a `message`? We included that 
by adding 

```html
{{ message }}
```

inside our HTMl (this is called mustache syntax). So now that we know how to add data, 
let's add what we need for our punching bag. We need a property to tell us the 
punching bag's "health" and when it's ready to burst.

Replace `data` in `app.js`:

```javascript
data: {
  health: 100,
  ended: false
}
```

Now we need something to change the health of the punching bag. Something like a function! 
Let's add that to our component as another property:

```javascript
methods: {
  punch() {
    this.health -= 10;
    if (this.health <= 0) {
      this.ended = true;
    }
  },
}
```

`this.health` and `this.ended` refer to the data in our component instance; remember 
that we defined these fields in the `data` property! Cool, so we've got a punching function. 
Let's add something to reset our punching bag back to full health. Add the below function 
within your `methods` section:

```javascript
restart() {
  this.health = 100;
  this.ended = false;
},
```

Your `app.js` should at this point look like the following:

```javascript
new Vue({
  el: '#main-app',
  data: {
    health: 100,
    ended: false,
  },
  methods: {
    punch() {
      this.health -= 10;
      if (this.health <= 0) {
        this.ended = true;
      }
    },
    restart() {
      this.health = 100;
      this.ended = false;
    },
  },
});
```

<!-- bag image -->
<div id="bag" v-bind:class="{ burst: ended }"></div>

<!-- bag health bar -->
<div id="bag-health">
    <div v-bind:style="{ width: health + '%' }"></div>
</div>

<!-- game control buttons -->
<div id="controls">
    <button v-on:click="punch" v-show="!ended">Punch</button>
    <button v-on:click="restart">Restart</button>
</div>

Your index.html should look like this:
![](img/indexHtmlScreenshot.png)


Now head over to your app.js file. You'll notice that you the line ``el='#main-app'``.
``el`` is an element selector, and it specifies the div that our app is going to act on (the same way we used ``getElementById`` in previous labs).

In your app.js, inside the ``data`` section , paste the following code:

~~~~
health: 100,
ended: false,
~~~~

These variables act like states, and they can be manipulated by the functions we provide in the methods section.
In this case, they keep track of the health of our bag and boolean indicating if the game has ended.

Now let's add a few methods to make our game more interesting. Paste the following code inside methods:

~~~~
punch() {
      this.health -= 10;
      if (this.health <= 0) {
        this.ended = true;
      }
    },
    restart() {
      this.health = 100;
      this.ended = false;
    },
~~~~

These functions will be called from button clicks as specified in the ``index.html`` file. 

## Summary / What you Learned

* [ ] can be checkboxes

## Resources

This workshop was based on a tutorial at https://www.youtube.com/watch?v=WjfpQlVem-8.
The repo with their provided code is located at https://github.com/iamshaunjp/vuejs-playlist/tree/65348d6c9202c7f573ca62305ca8c8cf19f15d58.
