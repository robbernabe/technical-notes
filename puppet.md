# Puppet Environment Setup

## Install Puppet

On puppetmaster:

    wget http://apt.puppetlabs.com/puppetlabs-release-precise.deb
    sudo apt-get update
    sudo apt-get install puppetmaster

On puppet node:

    wget http://apt.puppetlabs.com/puppetlabs-release-precise.deb
    sudo apt-get update
    sudo apt-get install puppet

## Configure Puppet

Now on the node, we'll need to generate a cert then tell the master to sign it
to setup initial authentication:

    puppet agent --server puppetmaster --waitforcert 60 --test

Then on the master, we should notice a certificate:

    puppet cert --list

We can sign it with:

    puppet cert --sign node1

Now to create our inital environment, edit `/etc/puppet/puppet.conf` and add
the following to the [main] section:

    environment=puppet_testing

add the following to the end of the file:

    [puppet_testing]
    manifest    = /var/puppet/puppet_testing/site.pp
    modulepath  = /var/puppet/puppet_testing/modules
    templatedir = /var/puppet/puppet_testing/templates

In `/var/puppet/puppet_testing/` is where I cloned the PayPerks puppet repo,
but clearly you can setup your own manifests here if you're starting fresh.

Then on the node, run the following to apply any manifests:

    puppet agent --onetime --verbose --debug --test --no-daemonize
