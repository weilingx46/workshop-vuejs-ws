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

* [ ] Set up a Vue component

* [ ] Learn how to add event handlers and class/style bindings 

* [ ] Set up a Vue router

* [ ] Learn how to pass props to components

![](https://i.imgur.com/3eDkeRZ.gif)

## Setup

If we were building a large-scale app, we would install Vue.js via `npm`. 
But for our purposes, we can just include the CDN link in our HTML! Add the below lines at 
the bottom of your body tags in `index.html`:

```html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
```

And that's all the setup we should need!

## Step by Step

To get started, add the following code in `app.js`:

```javascript
const punchingBag = Vue.component('punching-bag', {
  data: () => {
    return {
      message: 'Hello CS 52!',
    }
  }
})

new Vue({
  // This is our root instance. 
  el: '#main-app',
});
```

A lot going on here, but let's dive in. The root instance of the Vue application 
is the code at the bottom (`new Vue(...)`). It mounts to the div where 

```html
<div id="main-app">
  ....
</div>
```

as indicated by its `el` property. Above that, we're creating a `punching-bag` 
component, which has its own specific data fields such as `message`. Already, 
you can see some similarities with React!

In actuality, a punching bag doesn't need a `message` field. So let's replace `data `
with the following:

```javascript
data: () => { 
  return {
    health: 100,
    ended: false,
}},
```

So now our punching bag has a `health` and a boolean to tell us if we've exhausted 
that health.

Ok but our punching bag also needs to have a layout right? Something like a template? 
Well let's add one! Update your punching bag component to look like the below: 

```javascript
const punchingBag = Vue.component('punching-bag', {
  data: () => { 
    return {
      health: 100,
      ended: false,
  }},
  template: `
    <div class="punching-bag">
      <div id="controls">
          <button v-on:click="punch" v-show="!ended">Punch</button>
          <button v-on:click="reset">Reset</button>
      </div>
    </div>
  `,
});
```

We now have a `template` property for our component that has the HTML layout it 
will need. Notice how there's a `v-on:click` on the buttons? That indicates to Vue 
that when a click event is emitted from the buttons execute the `punch()` and `restart()` 
methods within your component. `v-show` tells Vue to only show the Punch button 
if ended is false.

But we don't have methods yet. Let's add a `methods` property right below `data`:

```javascript
data: () => { 
  return {
    health: 100,
    ended: false,
}},
methods: {
  punch() {
    this.health -= 10;
    if (this.health <= 0) {
      this.ended = true;
    }
  },
  reset() {
    this.health = 100;
    this.ended = false;
  },
},
// .....your template code, etc
```

So we're updating our punching bag to reduce its health by 10 everytime it's 
punched, and when its health is below zero, set `ended` to true. Recall that in 
our template HTML, we don't show the Punch button if `ended` is true, so we are 
directly affecting our DOM.

Now let's update our template. Add the following HTML to the template so it looks like 
the below:

```javascript
template: `
  <div class="punching-bag">
    <div id="bag" v-bind:class="{ burst: ended }"></div>

    <div id="bag-health">
        <div v-bind:style="{ width: health + '%' }"></div>
    </div>

    <div id="controls">
        <button v-on:click="punch" v-show="!ended">Punch</button>
        <button v-on:click="restart">Restart</button>
    </div>
  </div>
`,
```

`v-bind` modifies the attributes of the div. In `#bag-health`, it updates its 
child div's width everytime our component's `health` changes. This is our 
progress bar! And in `#bag` it adds a class attribute `burst` to the div when 
ended is true. We use this in our CSS file to change the image (check it out!).

Your punching bag component should now look like the following:

```javascript
const punchingBag = Vue.component('punching-bag', {
  data: () => { 
    return {
      health: 100,
      ended: false,
  }},
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
  template: `
    <div class="punching-bag">
      <div id="bag" v-bind:class="{ burst: ended }"></div>

      <div id="bag-health">
          <div v-bind:style="{ width: health + '%' }"></div>
      </div>

      <div id="controls">
          <button v-on:click="punch" v-show="!ended">Punch</button>
          <button v-on:click="restart">Restart</button>
      </div>
    </div>
  `,
});
```

Now we just need to add our punching bag component to our root instance. Go to `index.html` 
and inside the `<div id=main-app>`, add this:

```html
<punching-bag></punching-bag>
```

That's it! Now run the following command and open up <http://localhost:8000> in your browser:

```python
python -m SimpleHTTPServer
```

And voila! We have our first Vue.js application! You should now see the punching bag and 
be able to click the Punch and Reset buttons to modify the progress bar.

But this is still very basic. So let's now add some routing!

We're still going to have our home page that has our punching bag but also add a `/tim` route 
that gives us a new punching bag with Tim's face!

Let's start with adding this code right below our punching bag component code:

```javascript
const routes = [
  { path: '/', component: punchingBag, props: { tim: false } },
  { path: '/tim', component: punchingBag, props: { tim: true } },
];

const router = new VueRouter({
  routes, // short for `routes: routes`
  mode: 'history', // removes the hash from
});
```

Wait what? Yes, Vue has props too! We've laid out our routes, but notice how we're 
reusing our punching bag component; the only thing we're changing is the `tim` 
prop that we're passing in.

Ok so now we need to update our punching bag to take advantage of this. In our `template`, 
update the `#bag` div to look like the following:

```
<div id="bag" v-bind:class="[{ burst: ended }, timClasses]"></div>
```

This tells Vue to add the `burst` class AND a `timClasses` object (that we will 
shortly define). Now, at the top of your punching bag component, add the following 
property:

```javascript
props: ['tim],
// ... your data, methods, template, etc
```

We now have access to the `tim` prop in our component. Now, between `methods` and `template`, 
add this code:

```javascript
computed: {
  timClasses: function () {
    return {
      'tim-100': this.tim && this.health <= 100 && this.health > 70,
      'tim-70': this.tim && this.health <= 70 && this.health > 30,
      'tim-30': this.tim && this.health <= 30 && this.health > 0,
      'tim-burst': this.tim && this.health <= 0,
    }
  }
},
```

`this.tim` simply refers to our `tim` prop. You can see that we're returning different classes 
based on our health. We put this code in our component's `computed` property, which 
holds fields that require more complicated logic than our simple `data` fields. 

We have one more thing to do. In `index.html` replace `#main-app` with the below: 

```html
<div id="main-app">
    <div class="links">
        <router-link to="/">Our OG Punching Bag!</router-link>
        <router-link to="/tim">Tim's Punching Bag</router-link>
    </div>
    <router-view></router-view>
</div>
```

We now have two links that go to different pages, i.e. the `router-link`s. The 
`<router-view></router-view>` line tells Vue where to insert our actual component.

And that's it!

## What to Submit/Extra Credit

Please commit and submit the link to your forked repo.

For extra credit:
  * Add an entirely new component (not reusing one like we did) and add a route for it
  * Modify our punching bag so that, when it bursts, it has a random image on top of it 
  (i.e. randomly selected from a set of images). Hint: This will need to be in 
  the `computed` section!

## Summary / What you Learned

* [X] Set up a Vue component

* [X] Learn how to add event handlers and class/style bindings 

* [X] Set up a Vue router

* [X] Learn how to pass props to components

## Resources

This workshop was adapted from a tutorial at https://www.youtube.com/watch?v=WjfpQlVem-8.
The repo with their provided code is located at https://github.com/iamshaunjp/vuejs-playlist/tree/65348d6c9202c7f573ca62305ca8c8cf19f15d58.

Further resources used:
  * https://vuejs.org/v2/guide/
  * https://router.vuejs.org/en/
