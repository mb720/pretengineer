---
title: Packer, Vagrant and your Infrastructure – Fitting the Pieces Together
layout: post
summary: Where does Packer, the image creation tool, fit into your workflow?
filename: _posts/2013-09-11-packer-vagrant-infra.md
---

Questions like this come up on the [Packer](http://packer.io) mailing list and in IRC
a fair bit:

> Ok, I'm trying to understand how Packer relates to Vagrant. In particular,
> what would I use a Vagrant provision-er to do vs. a Packer provision-er.
> Or will Packer eventually replace vagrant entirely?

It's a good question. I'm going to try to answer this from a high level, explaining
each component along the way. The goal here is to eliminate some confusion that
apparently exists about the tools.

## Packer

Packer, in short, is an [open source](https://github.com/mitchellh/packer)
tool for automating the creation of Virtual Machine images. That could be, among others:

- A VMware compatible virtual machine
- A VitualBox `OVF` formatted appliance
- An Amazon AMI
- An OpenStack compatible image

The part of Packer that creates those images is called
a "Builder".

But, what does the "creation of virtual machine images" actually mean?

Essentially, it's taking a base image and applying "provisioners" on top
of it. A provisioner could be:

- A shell script
- A Puppet manifest
- Chef cookbooks

Base images vary depending on the format you're outputting. For example,
Virtualbox expects an `.iso` image of an operating system, like Ubuntu,
as does VMware. But, while creating an Amazon AMI, you need to give it
a base `ami`, which again, is an operating system like Ubuntu.

To summarize: You have a base image, you apply provisioning of some kind
on top of it, you output a final image. This is all done locally and
automated by Packer.

You simply give it configuration (called a "template") that looks like this:

    {
      "builders": [{
        "type": "virtualbox",
        "iso_url": "http://releases.ubuntu.com/12.04/ubuntu-12.04.3-server-amd64.iso",
        "iso_checksum": "2cbe868812a871242cdcdd8f2fd6feb9"
        ...
      }],
      "provisioners": [{
        "type": "shell",
        "inline": [
          "sudo apt-get install -y redis-server"
          ...
        ]
      }],
      "post-processors": ["vagrant"]
    }

This isn't a complete example, but hopefully this helps demonstrate how
you configure Packer.

## Vagrant

How does Vagrant fit into this, then?

Vagrant is a tool for managing development environments using Virtual Machines.
That development environment could be in VMware, Virtualbox or even AWS.

That means you can take a "Vagrantfile" with configuration for a virtual
machine, looking something like this:

    Vagrant.configure("2") do |config|
      config.vm.box = "precise64"
      config.vm.network :forwarded_port, guest: 80, host: 8080
      config.vm.synced_folder "../data", "/vagrant_data"

      config.vm.provision :puppet do |puppet|
        puppet.manifest_file  = "site.pp"
      end
    end

A developer can now do a `vagrant up` and Vagrant will automate the launching and
communication with that virtual machine, configured above.

You have your development environment automatically provisioning with
Puppet. It bootstraps your application, installs dependencies and so forth.

However, it takes time to do that, so developers have to wait when
they create new machines. Additionally, small issues with stateful resources
can make the provisioner fail, creating a hassle for the developer and potentially
their teammates.

So, here's where Packer fits in. Notice the above line in the Vagrantfile:

    ...
      config.vm.box = "precise64"
    ...

That's telling Vagrant to use the base box `precise64`. It will create a new
Virtual Machine from that box. In this case, it's just a bare Ubuntu VM
with VM tools installed.

What if we could just start from a base box that is already bootstrapped
for your application?

## Packer + Vagrant in Development

So, you intend to build base boxes for your developers with Packer, running
your Puppet scripts over a base image (like Ubuntu 12.04), and creating a Vagrant
box. Packer can automate that procedure.

However, this doesn't replace provisioning in development. If you make changes
to your Puppet scripts, simply re-run them with `vagrant provison`. No
need to re-build the base image.

So, that's how you use Packer to improve your development workflow. But
we can go further.

## Packer and Your Infrastucture

Using Packer to build images for a production environment is identical
to how you use it in development. Let's expand the example from above
to include Amazon AWS.

    {
      "builders": [{
        "type": "virtualbox",
        "iso_url": "http://releases.ubuntu.com/12.04/ubuntu-12.04.3-server-amd64.iso",
        "iso_checksum": "2cbe868812a871242cdcdd8f2fd6feb9"
        ...
      }],
      "builders": [{
        "type": "amazon-ebs",
        "source_ami": "ami-de0d9eb7",
        "region": "us-east-1",
        ...
      }],
      "provisioners": [{
        "type": "puppet-masterless",
        "manifest_file": "site.pp"
      }],
      "post-processors": ["vagrant"]
    }

This tells Packer to build two things:

- A Virtualbox compatible virtual machine
- An Amazon AMI

The provisioners, however, are exactly the same. This means that your
production and development environments come closer to parity, and you get
to launch and development your application from a pre-configured image.

## A Typical Workflow

So, given all of the potential of this, how does it actually effect your
workflow or operations?

Here's a recent workflow I used for a project. This was a simple web application
that talked to external database services and caches. The intent was to scale
it horizontally by adding more servers.

1. Use Packer to create a base box for Vagrant VMware, provisioned with only
basic stuff, like Ruby, for Puppet.
2. Use that Vagrant box to develop my Puppet scripts for the application,
such as running the Python web application.
3. Once I was happy with the Puppet scripts, I used Packer to build a development VM for
VMware that had all of the applications dependencies installed.
4. I used that Vagrant box to do my application development.
5. When I was ready to test on real infrastructure, I simply added the `amazon-ebs`
builder to my Packer template. My same provisioning scripts I developed
locally are now run on EC2. This creates an AMI.
6. I then launched a machine with the AMI created above. It then joined the load balancer
and started responding to requests.
7. After making Puppet changes, I then rebuilt my AMI and created a new instance. Small
development changes could be made by re-running Puppet. [^1]

In this example, adding more app instances is trivial, as we have a fully-configured
AMI to start from. It also reduces the time from launch to responding to requests
to minutes, instead of waiting for provisioning scripts to run.

But, this isn't all brand new technology – snapshots and AMIs have been around for
a while. Developers are certainly aware of the boons of the "golden image" approach.

However, what Packer does is introduce a workflow that assists in doing this
across both production (which could be multiple cloud services) and development, which
could require multiple image formats.

## In Conclusion

Packer can really help automate painful workflows in certain scenarios. It also
lowers the bar to creating immutable infrastructure with lots of that good 'ole development/production
parity.

Packer has excellent documentation. Here is some further reading:

- The Packer [Intro](http://www.packer.io/intro/), which walks you through using it and answers common questions
- Packer Builders, such as [VMware](http://www.packer.io/docs/builders/vmware.html), [Amazon EBS](http://www.packer.io/docs/builders/amazon-ebs.html), [DigitalOcean](http://www.packer.io/docs/builders/digitalocean.html)
- Packer Provisioners, such as [Puppet](http://www.packer.io/docs/provisioners/puppet-masterless.html), [Chef](http://www.packer.io/docs/provisioners/chef-solo.html) and [Salt](http://www.packer.io/docs/provisioners/salt-masterless.html)
- The full Packer [Documentationn](http://www.packer.io/docs)

[^1]: This, to me, is where things can be improved. It's a bit of a hassle
to re-build images and re-launch them into your infrastructure. Using Amazon's tools,
such as Elastic Load Balancer, can greatly reduce pain, but there's definitely still
things to fix.
