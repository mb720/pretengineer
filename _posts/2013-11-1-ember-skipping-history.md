---
title: Skipping History Entries in Ember Transitions
layout: post
summary: Avoid polluting the History API with redirect loops in Ember.js
filename: _posts/2013-10-1-ember-skipping-history.md
---

Occasionally you'll redirect a user in [Ember](http://emberjs.com/) silently
based on some condition. In that case, you don't want to pollute their
browser history, as hitting 'back' button would kick them into a loop.

This is, sadly, common in applications that manipulate the history
API and can cause a lot of frustration for the user (I yell "JAVASSSSCRIPTTT" really loud).

Ember has a good method for dealing with this. For those "silent"
transitions that will trigger based on a condition, use [`replaceRoute`](http://emberjs.com/api/classes/Ember.Controller.html#method_replaceRoute)
instead of [`transitionToRoute`](http://emberjs.com/api/classes/Ember.Controller.html#method_transitionToRoute).

Here's an example route that demonstrates how to use it. This assumes
that you have `Post` that has a `draft` attribute telling the application
to only show it to authenticated users. It's a trivial example.

{% highlight javascript %}
// A simple Ember.Route for a Post. In this case, the Post has a draft boolean
// that protects it from the public and requires authentication. We need
// to redirect a user there without polluting their history so "back" still works.
//
App.PostRoute = Ember.Route.extend({
  model: function(params) {
      return App.Post.find(params.id) // return a Post for a url /post/:id
  },
  setupController: function(controller, post) {
    if (post.draft === true) {
      // In the case of a draft, we want to redirect to a log-in
      // route to authenticate the user, sending them to /authenticate
      controller.replaceRoute("authenticate");
    }
    // Otherwise, continue as normal and load the post
    controller.set('model', post);
  }
});
{% endhighlight %}

`replaceRoute` simply won't create a history entry, dodging redirect loop
pain.
