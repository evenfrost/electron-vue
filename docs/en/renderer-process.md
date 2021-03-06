# Renderer Process
> Since Electron uses Chromium for displaying web pages, Chromium’s multi-process architecture is also used. Each web page in Electron runs in its own process, which is called the renderer process.

> In normal browsers, web pages usually run in a sandboxed environment and are not allowed access to native resources. Electron users, however, have the power to use Node.js APIs in web pages allowing lower level operating system interactions.

**From the [Electron Documentation](http://electron.atom.io/docs/tutorial/quick-start/#renderer-process)**

---

## On the subject of `vue` and `vuex`

### vue components
If you are unfamiliar with Vue components, please give [this](http://vuejs.org/v2/guide/single-file-components.html) a read. Using components gives our large complex applications more organization. Each component has the ability to encapsulate its own CSS, template, and JavaScript functionality.

Components are stored in `app/src/renderer/components`. When creating child components, place them inside a new folder with the name of its parent component.
```
app/src/renderer/components
├─ ParentComponentA
│  ├─ assets
│  |  └─ logo.png
│  ├─ ChildComponentA.vue
│  └─ ChildComponentB.vue
└─ ParentComponentA.vue
```
##### Notice
Assets can also be organized in this manner. If a particular component needs an image file, let's place that inside a folder named `assets`. If you are using static assets that you do not want webpack to bundle, please read [**Using Static Assets**](#using-static-assets) below.

### vue routes
For more information about vue-router click [here](https://github.com/vuejs/vue-router). In short, I'd encourage the use of vue-router as creating a Single Page Application is much more practical when making electron apps. Do you really want to manage a bunch of BrowserWindows or static page navigation? Probably not.

Routes are held in `app/src/renderer/routes.js` and defined like so...
```js
{
  path: '<routePath>',
  name: '<routeName>',
  component: require('components/<routeName>View')
}
```
...where both `<routePath>` and `<routeName>` are variables. These routes are imported to `app/src/renderer/main.js` and are then attached to the component tree using the `<router-view></router-view>` directive in `app/src/renderer/App.vue`.

##### Notice
When using `vue-router`, refrain from using [**HTML5 History Mode**](http://router.vuejs.org/en/essentials/history-mode.html). This mode is strictly meant for serving files over the `http` protocol and does not work properly with the `file` protocol that electron serves files with. The default `hash` mode is what you will need.

### vuex modules
Describing `vuex` is not the easiest thing to do, so please read [this](http://vuex.vuejs.org/en/intro.html) for a better understanding of what problem it tries to solve and how it works.

electron-vue takes advantage of `vuex`'s module structure to create multiple data stores and are saved in `app/src/renderer/vuex/modules`.

Having multiple data stores can be great for organization, but can also be annoying to have to import each and every one. But don't fret, as `app/src/renderer/vuex/modules/index.js` does the dirty work for us! This little script let's `app/src/renderer/vuex/store.js` import all of our modules in one-shot.

# Using Static assets

Sometimes your don't want webpack to bundle some of your assets. Let's say you have a large sprite collection, but your app loads the images in on-demand. You don't want webpack to bundle every single sprite if only a few might be used at a time.

When using static assets, let's place them inside the `app/dist` folder. Essentially everything webpack produces in production builds lives in this folder, so everything inside here will be **included** in final builds.

#### Use Case
Let's say I have a component that loads an image, but the image's path isn't known until some other task is completed. To keep things simple, let's just use a `$data` variable to bind our `<img>`'s src.

```html
<template>
  <img v-bind:src="imageUrl">
</template>

<script>
  export default {
    data () {
      return { imageUrl: 'imgs/unsplash.png' }
    }
  }
</script>
```
Here webpack will not bundle the `unsplash.png` image and the app will look inside the `app/dist` directory for the asset.

