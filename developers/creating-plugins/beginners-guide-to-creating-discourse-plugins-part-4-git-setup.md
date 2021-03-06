---
title: "Beginner's Guide to Creating Discourse Plugins Part 4: Git Setup"
weight: 340
---

Previous tutorials in this series:

Part 1: [Creating a basic plugin](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins)
Part 2: [Plugin outlets](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins-part-2-plugin-outlets)
Part 3: [Custom Settings](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins-part-3-custom-settings)
---

Now that your plugin is getting more sophisticated, it's time to get more sophisticated about how you develop it.

We suggest that you use [git](https://git-scm.com/) as version control for your plugin. We also recommend that you use [github](https://github.com) to share your plugin code with others. 

### Creating your Git Repo

Once you've created your Github account, visit [this url](https://github.com/new) to create a new repository. You can call it anything you want, but generally something that stars with `discourse-` is good. Make sure the repository is **public**. Here's how my screen looked:

<img src="//discourse-meta.s3-us-west-1.amazonaws.com/original/3X/3/8/38c2d794af363a8a9840cddc5ac8d92a24374b12.png" width="690" height="390"> 

### Creating your local working folder

At this point I create a local directory on my computer to hold the plugin. I usually put mine in `~/code` but you can put it anywhere you like on your computer:

```sh
mkdir -p ~/code/discourse-plugin-test
cd ~/code/discourse-plugin-test
```
Now let's follow the instructions from github to initialize the repo with a README:

```sh
echo "# discourse-plugin-test" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:eviltrout/discourse-plugin-test.git
git push -u origin master
```

Finally, create a `plugin.rb` file for your plugin as explained in [part 1](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins). For this example I just created a dummy one:

**plugin.rb**
```ruby
# name: discourse-plugin-test
# about: Shows how to set up Git
# version: 0.0.1
# authors: Robin Ward
```

### Creating a symlink

Because you followed our [developer guide](http://blog.discourse.org/2013/04/discourse-as-your-first-rails-app/) you should have a copy of discourse checked out on your computer somewhere. I checked mine out to `~/code/discourse` but again you could have put it anywhere and this should still work if you adjust the following code accordingly:

```sh
cd ~/code/discourse/plugins
ln -s ~/code/discourse-plugin-test .
```

The above code created a [symbolic link](https://en.wikipedia.org/wiki/Symbolic_link) between your discourse code and your plugin folder. Restart your rails server and you should find your plugin is working!

The beauty of this setup is you can just check your plugin into github and not worry about the discourse codebase it lives inside. Your changes will be isolated to the plugin itself. If you need to edit discourse's code you still can, but git will track the changes separately!

I recommend using one editor window for your plugin codebase and one for Discourse itself. It is easier when you think of them as two different things.

---
### More in the series

Part 5: [Admin Interfaces](http://learndiscourse.org/beginners-guide-to-creating-discourse-plugins-part-5-admin-interfaces)

<small class="documentation-source">Source: [https://meta.discourse.org/t/beginners-guide-to-creating-discourse-plugins-part-4-git-setup/31272](https://meta.discourse.org/t/beginners-guide-to-creating-discourse-plugins-part-4-git-setup/31272)</small>
