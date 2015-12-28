---
title: "Beginner's Guide to Creating Discourse Plugins Part 5: Admin Interfaces"
weight: 320
---

<small class="documentation-source">Source: [https://meta.discourse.org/t/beginners-guide-to-creating-discourse-plugins-part-5-admin-interfaces/31761](https://meta.discourse.org/t/beginners-guide-to-creating-discourse-plugins-part-5-admin-interfaces/31761)</small>

Previous tutorials in this series:

Part 1: [Creating a basic plugin](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins)
Part 2: [Plugin outlets](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins-part-2-plugin-outlets)
Part 3: [Custom Settings](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins-part-3-custom-settings)
Part 4: [git setup](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins-part-4-git-setup)

---

Sometimes [site settings](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins-part-3-custom-settings) aren't enough of an admin interface for your plugin to work the way you want. For example, if you install the [discourse-akismet](https://github.com/discourse/discourse-akismet) plugin, you might have noticed that it adds a navigation item to the admin plugins section in of your Discourse:

<img src="//discourse-meta.s3-us-west-1.amazonaws.com/original/3X/2/c/2c42d190a226fcc85a017ab802c0eaafc872a4f7.png" width="690" height="169"> 

In this tutorial we'll show you how to add an admin interface for your plugin. I'm going to call my plugin purple-tentacle, in honor of [one of my favorite computer games](https://en.wikipedia.org/wiki/Day_of_the_Tentacle). Seriously, **[I really love that game](https://twitter.com/eviltrout/status/627119973773746176)**!

### Setting up the Admin Route

Let's start by adding a `plugin.rb` like we've done in previous parts of the tutorial. 

**plugin.rb**
```ruby
# name: purple-tentacle
# about: A sample plugin showing how to add a plugin route
# version: 0.1
# authors: Robin Ward
# url: https://github.com/discourse/purple-tentacle

add_admin_route 'purple_tentacle.title', 'purple-tentacle'

Discourse::Application.routes.append do
  get '/admin/plugins/purple-tentacle' => 'admin/plugins#index', constraints: StaffConstraint.new
end
```

The `add_admin_route` line tells Discourse that this plugin will need a link on the `/admin/plugins` page. Its title will be `purple_tentacle.title` from our i18n translations file and it will link to the `purple-tentacle` route.

The lines below that set up the server side mapping of routes for our plugin. One assumption Discourse makes is that almost every route on the front end has a server side route that provides data. For this example plugin we actually don't need any data from the back end, but we need to tell Discourse to serve up something in case the user visits `/admin/plugins/purple-tentacle` directly. This line just tells it: 'hey if the user visits that URL directly on the server side, serve the default plugins content!' 

(If this is confusing don't worry too much, we'll come back to it in a future tutorial when we handle server side actions.)

Next, we'll add a template that will be displayed when the user visits the `/admin/plugins/purple-tentacle` path. It will just be a button that shows an animated gif of purple tentacle when the user clicks a button:

**assets/javascripts/discourse/templates/admin/plugins-purple-tentacle.hbs**
```handlebars
{{#if tentacleVisible}}
  <div class='tentacle'>
    <img src="http://eviltrout.com/images/tentacle.gif">
  </div>
{{/if}}

<div class='buttons'>
  {{d-button label="purple_tentacle.show" action="showTentacle" icon="eye"}}
</div>
```

If you've learned the basics of handlebars the template should be pretty simple to understand. The `d-button` is a component in discourse we use for showing a button with a label and icon.

To wire up our new template we need to create a route map:

**assets/javascripts/discourse/purple-tentacle-route-map.js.es6**
```javascript
export default {
  resource: 'admin.adminPlugins',
  path: '/plugins',
  map() {
    this.route('purple-tentacle');
  }
};
```

A route map is something we added to discourse to make it so that plugins could add routes to the ember application. The syntax within `map()` is very similar to [Ember's router](http://guides.emberjs.com/v1.10.0/routing/defining-your-routes/). In this case our route map is very simple, it just declares one route called `purple-tentacle` under `/admin/plugins`. 

Finally, let's add our translation strings:

**config/locales/en.yml**
```yaml
en:
  js:
    purple_tentacle:
      title: "Purple Tentacle"
      show: "Show Purple Tentacle"


```
If you restart your development server, you should be able to visit `/admin/plugins` and you'll see our link! If you click it, you'll see the button to show our purple tentacle:

<img src="//discourse-meta.s3-us-west-1.amazonaws.com/original/3X/a/f/af2b79ca2649408553da39caf473d6715de99734.png" width="690" height="167"> 

Unfortunately, when you click the button, nothing happens :(  

If you look at your developer console, you should see an error that provides a clue to why this is: `Uncaught Error: Nothing handled the action 'showTentacle'`

Ah yes, the reason is in our handlebars template we are depending on a couple of things:

1. That when the user clicks the button, `showTentacle` will be called.
2. `showTentacle` should set the property `tentacleVisible` to true so that the image shows up.

If you haven't read the [Ember Guides on Controllers](http://guides.emberjs.com/v1.13.0/controllers/) now is a good time to do so, because we'll implement a controller for our `purple-tentacle` template that will handle this logic.

Create the following file:

**assets/javascripts/discourse/controllers/admin-plugins-purple-tentacle.js.es6**
```javascript
export default Ember.Controller.extend({
  tentacleVisible: false,

  actions: {
    showTentacle() {
      this.set('tentacleVisible', true);
    }
  }
});
```

And now when we refresh our page, clicking the button shows our animated character!

<img src="//discourse-meta.s3-us-west-1.amazonaws.com/original/3X/0/9/09dd726aea99bdb4783f785d0e8f611713b622be.png" width="647" height="462"> 

I'll leave it as an extra exercise to the reader to add a button that hides the tentacle when clicked :smile:

If you are having trouble getting your version of this plugin working, I've pushed it to [github](https://github.com/eviltrout/purple-tentacle).

---
### More in the series

Part 6: [Acceptance Tests](http://learndiscourse.org/beginner-s-guide-to-creating-discourse-plugins-part-6-acceptance-tests)
