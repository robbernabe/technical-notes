## Local MongoDB Setup on Mac OS X Mountain Lion

### Installation

First install mongodb with homebrew:

        brew install mongodb

Now we'll need to run multiple instances of mongodb, so we can configure each as
part of the replica set.  We'll copy the default configuration that homebrew
installed for us as a basis:

        cd /usr/local/etc
        cp mongodb.conf rs0.conf

Now edit rs0.conf to look like the following:

        dbpath = /usr/local/var/rs0
        logpath = /usr/local/var/log/rs0/mongo.log
        logappend = true
        bind_ip = 127.0.0.1
        port = 10000
        verbose = true
        vv = true
        replSet = replica_set1

Now we'll need two additional configs for two slaves. Be sure to edit the port
numbers/dbpath/logpath appropriately (I'll be using port 10001 and 10002 here):

        cp rs0.conf rs{1..2}.conf
        vim rs1.conf
        vim rs2.conf

Now's a great time to create those directories we're referring to:

        for i in 0 1 2; do mkdir -p usr/local/var/rs$i; done

Make sure you have an entry for your local host in /etc/hosts, otherwise mongodb
will complain:

        0 ✓ rbernabe@cipher ~> hostname
        cipher.local
        0 ✓ rbernabe@cipher ~> grep cipher.local /etc/hosts
        127.0.0.1    localhost cipher.local cipher

Now we can start all three instances like so:

        for i in 0 1 2; do mongod --config /usr/local/etc/rs$i.conf & done

Now that they're running, lets initialize the replica sets. We'll have to
connect to one mongo instance, which will become the master:

        0 ✓ rbernabe@cipher > mongo --port 10000
        MongoDB shell version: 2.2.3
        connecting to: 127.0.0.1:10000/test
        > rs.initiate()
        {
            "info2" : "no configuration explicitly specified -- making one",
            "me" : "cipher.local:10000",
            "info" : "Config now saved locally.  Should come online in about a minute.",
            "ok" : 1
        }
        > rs.status()
        {
            "set" : "replica_set1",
            "date" : ISODate("2013-02-21T18:50:06Z"),
            "myState" : 1,
            "members" : [
                {
                    "_id" : 0,
                    "name" : "cipher.local:10000",
                    "health" : 1,
                    "state" : 1,
                    "stateStr" : "PRIMARY",
                    "uptime" : 122,
                    "optime" : Timestamp(1361472592000, 1),
                    "optimeDate" : ISODate("2013-02-21T18:49:52Z"),
                    "self" : true
                }
            ],
            "ok" : 1
        }

Now we have a master, but no slaves.  So let's add the two other nodes:

        replica_set1:PRIMARY> rs.add("cipher.local:10001")
        { "ok" : 1 }
        replica_set1:PRIMARY> rs.add("cipher.local:10002")
        { "ok" : 1 }

Done! You can check the status of the replica set with rs.status().
