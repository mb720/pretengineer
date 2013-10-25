---
title: Open-Sourcing Stripe Hooks
layout: post
summary: An independent service to handle receipts and notifications with Stripe
filename: _posts/2013-10-25-stripe-hooks.md
---

Increasingly, I'm using [Stripe](https://stripe.com/) as a payment
processor for both professional work and hobby projects.

One key feature they offer is [webhooks](https://stripe.com/docs/webhooks).
When something happens outside of your typical API communication with them,
they send an HTTP request to a url (or many) of your choice. This is
useful for notifying administrators, sending receipts to customers and so on.

I've also taken to writing service-oriented web applications that rely more
and more on APIs. I'll end up just shoving the handling of Stripe webhooks
into something that recieves HTTP on the public internet, typically the API
that backs an [Ember](http://emberjs.com/) or [Angular](http://angularjs.org/) app.

Really, the billing webhooks shouldn't be there, and should be self-contained.
Given that, I have a lot to repeat on every project.

So, I made [stripe-hooks](https://github.com/pearkes/stripe-hooks). It's
a boiler plate system for sending webhooks, templating the emails
and customizing it for your use case.

## An Example

1. Jane Customer purchases a copy of the B side *Everybody's Next One* by Steppenwolf on
your website
2. Stripe charges his card and sends a `charge.succeeded` webhook
3. `stripe-hooks` picks up the hook and, based on the configuration,
sends the administrator an email notifying them, and Jane a receipt

The basic email templates look like this, but they can be customized
to fit your needs:

<div class="img-center"><img src="/static/img/hooks/new-invoice.png" class="img-responsive"></div>

## Using It

It's written in Python but you really don't need to touch the code – all
you do is configure it using a JSON file and choose which Stripe events
to act on, and what content to send. You can change the content
by modifying the [templates](https://github.com/pearkes/stripe-hooks-emails/) directly.
The templates use [Jinja](http://jinja.pocoo.org/) and offer a lot of flexibility.

It's designed to be deployed on Heroku, but with few dependencies, it
can basically go anywhere Python can.

The GitHub repo has everything you need.

- [stripe-hooks](https://github.com/pearkes/stripe-hooks)
- [stripe-hooks-emails](https://github.com/pearkes/stripe-hooks-emails)

