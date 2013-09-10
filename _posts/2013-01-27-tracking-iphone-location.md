---
title: Tracking Your iPhone's Location
layout: post
summary: Using Apple APIs to track the your phones location
filename: _posts/2013-01-27-tracking-iphone-location.md
---
<iframe width='720' height='400' frameBorder='0' src='http://a.tiles.mapbox.com/v3/pearkes.sf.html#12/37.7806/-122.4103'> </iframe>

This is an interactive map[^1] of The Bay Area / San Francisco, where I currently live,
with my hourly locations from about half of 2012. Each location is marked with a green dot.
You can probably see where I live, hangout and work.

Some interesting highlights:

- About halfway through the period my office moved within the SOMA neighborhood.
- I mostly hang out in the Hayes Valley / Mission neighborhoods. I never
go to North Beach, the Marina, The Sunset, Richmond or Potrero Hill.
- I enjoy spending time at Ocean Beach.
- Took a few trips to Bolinas, a small beach town an hour and a half
north of San Francisco.
- I spent a fair amount of time at the airport, apparently.
- I've been spotted on the Golden Gate Bridge, twice.

## The Backstory

Location data is really cool. Location data about where your phone (and your person) has been
is even cooler.

Luckily, some of us carry a certain type of beacon in our pockets - commonly called an
iPhone - this article focuses on how to get the location from your beacon.

Existing methods of iOS location tracking require heavy battery usage,
with an application listening in the background and collecting data. Though this may
work for short periods of time, running these applications over a period
of months simply isn't practical.

However, Apple happened to build an API to receive the location of your
phone, remotely. This tool rocks when you get your phone nabbed by a stranger,
but also has potential for recording your movements.

Almost 2 years ago I came across a PHP client made by Tyler Hall
called [Sosumi](https://github.com/tylerhall/sosumi) that does exactly
what we want.

I ported an unmaintained[^2] Python version of Sosumi into
its own package, called [findi](https://github.com/pearkes/findi). I
set up a monitoring service for this to alert me when Apple modifies their
API, which they occasionally do, and promise to maintain it to the best
of my ability.

I find, using this system, that my battery isn't noticeably affected.

*Note: Find My iPhone isn't really designed for this, and you may be breaking some
user agreement buried somewhere by using it in this way. I'm not responsible
for you doing that.*

## Fetching Your Location

First, grab the source of findi:

    $ git clone git@github.com:pearkes/findi.git

Then open a python shell and get your location:

    $ python
    >> from findi import FindMyIPhone
    >> findi = FindMyIPhone('email@example.com', 'password')
    >> iphone = findi.devices[0]
    >> print iphone
    {'latitude': 37.771251512, 'timestamp': datetime.datetime(2013, 1, 27, 16, 0, 54), 'longitude': -122.411252151, 'accuracy': 10.0}

The location data you get back is pretty good, typically. There's an "accuracy"
value included, so you could potentially filter out inaccurate locations given
by cell towers and the like.

Sam Soffes also made a [great RubyGem](https://github.com/soffes/findi) that does the same thing.

In order to store your location over time, you may want to run a
simple Heroku app, or something like it. I've created an example of such a service with detailed instructions,
called [findi-heroku-example](https://github.com/pearkes/findi-heroku-example#findi-heroku-example).

[^1]: For the curious, the above map is a custom map built with [TileMill](http://mapbox.com/tilemill/),
hosted by [MapBox](http://mapbox.com/) with data from [OpenStreetMap](http://www.openstreetmap.org/).
[^2]: I was digging around for a Python version of Sosumi and found [recordmylatitude](https://github.com/comfuture/recordmylatitude/blob/master/findmyiphone/__init__.py),
a project by comfuture. I fixed a bug or two, took it out of the package and gave it a home of its own. recordmylatitude used the MIT license, which I left intact.
