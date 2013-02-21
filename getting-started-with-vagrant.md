# Getting Started with Vagrant

Vagrant projects should be kept separated in their own directories, just like
code.  For example, for my puppet testing I created
`~/vagrant/puppet-project/`.  Here is where I will work on a specific project.

## Boxes

Boxes are the "templates" if you will. The boxes you have can be used in any
vagrant project you create.  `vagrant box list` will list all your boxes, and
it is these boxes (names of boxes) that you can use in your Vagranfiles.

If you notice later in these notes we'll use the box
called `precise64` twice in a Vagrantfile to create a multi-vm environment.

## My Test Environment

Here's how I created my initial vagrant setup for messing with puppet:

    0 ✓ rbernabe@cipher vagrant> mkdir puppet-project
    0 ✓ rbernabe@cipher vagrant> cd puppet-project
    0 ✓ rbernabe@cipher puppet-project> vagrant init
    A `Vagrantfile` has been placed in this directory. You are now
    ready to `vagrant up` your first virtual environment! Please read
    the comments in the Vagrantfile as well as documentation on
    `vagrantup.com` for more information on using Vagrant.
    0 ✓ rbernabe@cipher puppet-project> vim Vagrantfile

Edit the vagrant file to include the boxes we want in the environment. In this
case I wanted a multi-vm environment containing the puppetmaster and the
puppetnode, so my Vagrantfile looked like this (per the Vagrant docs):

    config.vm.define :puppetmaster do |master_config|
        master_config.vm.box = "precise64"
        # master_config.vm.forward_port 80, 8080
    end

    config.vm.define :puppetnode do |node_config|
        node_config.vm.box = "precise64"
    end
