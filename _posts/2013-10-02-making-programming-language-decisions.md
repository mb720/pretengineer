---
title: Choosing Programming Languages for Web Development
layout: post
summary: What matters, what doesn't, and where your challenges actually are
filename: _posts/2013-10-02-making-programming-language-decisions.md
draft: true
---

I've heard a lot of smart people talk like this, and I have now
adopted and regurgatate this theory, but I'd like to write about
it as I still see pervasive delusions from both sides of development /
business on teams.


When you load a web page, a lot of stuff happens. As an example,
I'll use `www.nytimes.com` to point out the different actions your browser
takes to give you that pretty looking newspaper page.

### The First Connection

This is how you first connect to the `www.nytimes.com` webpage. You
still haven't seen anything in your browser yet.

#### Resolving the Domain Name

Your browser, be it on your phone or desktop computer, looks up the
domain you gave it, in this case, `www.nytimes.com`. It does this
by talking to a DNS server and figuring out what IP address it should
speak in HTTP to.

DNS is cached all over the place. It's cached in your browser, potentially
by your operating system, by your networking hardware, by your ISP's networking
hardware, the list goes on. Usually DNS requests are returned
quickly, and almost instantly if you've visited that webpage recently.

So, let's assume that you or someone close to you resolves the `www.nytimes.com`
domain, and it points to the IP address `170.149.168.130`.

You now have an IP address to network with.

#### Connecting to the Server

Since this is just a plain-text HTTP request, and not an HTTPS request,
making the first connection is relatively simple.

Your browser now starts a TCP connection via a 3-way handshake with
the server at the address `170.149.168.130`.[1]

After that handshake is complete, your browser makes an HTTP request
to the web server. Something like `GET / HTTP/1.1`. The server,
if it wants, responds with HTTP headers and potentially a body.

This HTTP body is what you start to see in your browser. Typically,
it's HTML. HTML is just text that your browser knows how to render in a
certain way. HTML also contains pointers to CSS and JavaScript. Again,
text that your browser knows how to render or run in a certain way.

#### Rendering the Page

So, you've looked up the domain, made a TCP connection and subsequently
sent an HTTP request and recieved a response that included HTML in the body.

Now, your browser kicks in and does it's job. It takes the HTML you
gave it and renders it, with visual style from the CSS that was included
in the HTML.

But – sometimes you include something in the HTML that has an `http` link.

That tells your browser to go get that data from somewhere else. So,
it looks up that domain, say, `assets.nytimes.com`, and makes a new TCP/HTTP
connection to *that* server, download that data and renders it as it should.


[^1]: TCP itself is a massive beast of complexity, in my humble opinion. It's
not worth going to in-depth for these purposes, but if you're curious
you can read the excellent [Wikipedia article](http://en.wikipedia.org/wiki/Transmission_Control_Protocol)
on the subject. If you'd like to "play" with TCP, open up a shell
and use the `telnet` command. `telnet 170.149.168.130 80`. You're now
speaking raw TCP on port 80 with the `www.nytimes.com` web server.
