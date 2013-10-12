---
title: Breaking Up Pull Requests
layout: post
summary: Avoiding the "gigantic" pull request
filename: _posts/2013-10-12-breaking-up-pull-requests.md
---

If you're a team working with the [pull request](https://help.github.com/articles/using-pull-requests)
workflow on GitHub (a.k.a [e-notes](https://twitter.com/Huth/status/378306063520374784)),
you know that big pull requests can get rather unwiedly.

Something easy to forget is that GitHub supports pull requests to branches
other than your base branch. (i.e `master`)

Here's an example:

<img src="/static/img/prs/pull-request-example.png" class="img-responsive">

Merging that pull brings your work from the `smaller-incremenatal-feature`
branch into your main `big-feature` branch.

This makes code reviews considerably easier too. Instead of looking at a
changeset and written summary on every change needed for `big-feature`,
you can look at them individually and over time as the feature is
developed.
