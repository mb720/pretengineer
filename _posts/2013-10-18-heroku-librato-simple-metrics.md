---
title: Simple Metrics with Heroku and Librato
layout: post
summary: There's absolutely no excuse not to record business metrics
filename: _posts/2013-10-18-heroku-librato-simple-metrics.md
---

Metrics are important.

No, not your load average.[^1] Your *business* metrics are important. Are
your signups falling? Maybe it's a:

- Marketing problem
- Technical problem
- PR problem

You only know this if you're recording it. There's many ways to do this,
of course, and there's also many ways to display that information, to alert
on certain thresholds ("> 10 signups in last 24 hours!") or to check-in
on progress.

If you use Heroku and aren't recording metrics, Librato just [upgraded](http://blog.librato.com/posts/2013/10/16/heroku-add-on-logs-integration)
their add-on to make it *really* easy and completely agnostic to the
technology you use.

In Python:

{% highlight python %}
print("count#user.signups=1") # This syntax tells Librato the metric name and value
{% endhighlight %}

Or, whatever you use. I'm pretty sure it can print to `stdout`.[^2]

That's it. Librato pulls that in through Heroku's logging platform
and renders a dashboard for you. Alerts with threshholds can be set on
individual metrics to notify you of deviations.

It's $19 a month[^3] for a 2 dyno plan, covering 20 metrics with a 1 year
retention period. Well worth it, in my opinion.

Go monitor!

- [Librato add-on](https://addons.heroku.com/librato)

[^1]: Okay, yea, your technical metrics can be really useful for debugging
problems and alerting you early. But, conciously putting business metrics
first helps you prioritize and focus on what's important.
[^2]: Is there anything that doesn't?
[^3]: They have a free plan that defaults to adding a bunch of Heroku-esque
metrics such as dyno memory usage, but the custom metrics as noted
above are only available on paid plans, which $19 is the cheapest of.
