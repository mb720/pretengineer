---
title: Open-Sourcing Small Victories
layout: post
summary: A post-mortem and open-sourcing of a moderately successful side project
filename: "_posts/2013-10-01-open-sourcing-small-victories.md"
draft: true
---

Small Victories is a web app that makes a website from your Dropbox
folder. It was moderately successful after launch and has since stagnated.

If you've ever read about some project and thought, agh, "Yet-Another-X" –
we made one of those (kinda – see below). "Yet Another
Dropbox Folder to Website" app. We thought it was cool at the time – and still do,
of course – but we've been distracted by other things and it's just been
hanging out.

We're open sourcing it for the fun of it, but also as an experience
in packaging up a project for other people to see. I think this is
sometimes the most challenging part of open source – polishing something
so that it may be somewhat useful to someone.

This is also a bit of a launch post-mortem, describing traffic numbers,
any marketing we did (Jacob tweeted a link and wrote a blog post) as well
as current numbers, costs and upkeep. Jacob's blog is titled "Honesty
is the best policy" and I tend to agree.

## Project Statistics

- About 13k visits in total, the bulk of that on the day we shipped.
[Google Analytics Screenshot](https://dl.dropboxusercontent.com/s/t2thf77n6hs0xfi/2013-09-30%20at%206.49%20PM.png)
- 774 websites created
- [Public Metrics Dashboard](https://metrics.librato.com/share/dashboards/3nggi1qc)
- At 1000 a minute since launch, ~99,360,000 requests to the Dropbox API thus far (holy crap)
- Two independent Heroku dynos (1 background worker, 1 web)
- Free (small) Redis and Postgres instances
- Total cost of $50 a year ($20 for an SSL certificate, $30 for the domain)

## What Small Victories Is

Small Victories is a website that takes a bunch of files in a Dropbox
folder and creates a personal website for you. Updating is a simple as
saving a file in the folder. The rest is automatic and relatively fast.

It supports custom domains, so you can have a website like
`picnics.jack.ly` and all of your picnic photos now have a home.

While other Dropbox CMS services certainly exist, just running on the same platform doesn't make them all the same. Small Victories aims to be dead-simple: there's no admin interface, and there's no need to 'publish' – it just goes. Simple doesn't mean it's not powerful. We automatically inject any javascript and CSS you write, so your only limitations are really the creativity to work within some boundaries.

The whole thing is kind of hack – part of what makes it fun to build.
For example, a user changing the custom domain in their `_settings.txt`
file fires off an HTTP request to the Heroku API to add a new domain for
that app. That means there are hundreds of domains on one Heroku app.

Don't stop there – Dropbox defaults to expiring links at 4 hour
intervals – that won't work for us when we are relative linking assets
straight into the Dropbox folder. But, it turns out Dropbox uses a
guessable URL scheme for it's "permanent" asset URLs.

A big HTML blob just gets rendered and shoved in Redis so it can be served
directly to a incoming HTTP request. This Redis instance is free and hosted
who knows where with who knows what latency. Kind of feels funny to me.

## Open Sourcing It

Small Victories obviously appealed to some folks out there, as evident by the traffic it attracted
on the first day. But it takes blood and sweat to take something from a
zero day success to something tangibly useful for many. There are many
applications trying to solve this same problem

There are thousands of projects like this out there, I am sure. Small services – quick hacks – built fast in a few days and then left to hang around the gates
waiting for someone to care.

Sometimes you go searching for inspiration, or specific
solutions, and one of those projects comes across and teaches you
something that you should or shouldn't do.

Jacob is a big believer in following through on things, pushing a bit
harder to take something to the next level. We differ on this subject. I tend to
 – for side projects – shove the minimum polished product out there, and
if people don't *really* like it I move on.

It's a crowded space and we have a lot we want to do with limited resources. Maybe open sourcing and writing about this will keep us motivated to make this
thing into something, or find someone who wants to.

## Technical Stuff

So, Small Victories is now "Open Source". Any changes we make will be in
the open now. It's written in Golang, mostly to continue my learning on the
subject, but also because it seemed to suit the concurrent worker on one machine problem.

There are two components to the service. Both have README's describing what
they do. Both are MIT licensed.

- [pearkes/sv-fetcher](https://github.com/pearkes/sv-fetcher) on GitHub
- [pearkes/sv-frontend](https://github.com/pearkes/sv-frontend) on GitHub
