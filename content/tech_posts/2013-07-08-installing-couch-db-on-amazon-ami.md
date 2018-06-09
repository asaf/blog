---
tags:
- amazon
- ec2
- ami
- couchdb
comments: true
date: 2013-07-08T00:00:00Z
published: true
title: Installing CouchDB instance on Amazon AMI
url: /2013/07/08/installing-couch-db-on-amazon-ami/
---

{% img http://couchdb.apache.org/image/couch.png 160 180 Couch %}

One of our platform is based on CouchDB, we host everything on Amazon EC2 and while Amazon lacks with CouchDB support,
I have decided to build our own CouchDB server.

So here's a guide, how to install CouchDB on Amazon AMI (Amazon Linux):

### Launch Instance

Login into EC2, and launch a new instanc and choose _Amazon Linux AMI_,
You can of course choose other Linux flavours (you are not going to use Windows right?),

But I really suggest going with Amazon AMI, it is EBS backed, ships with AWS tools installed and has a
RHEL style package management using _yum_.

Once your instance is launched, SSH it, i'm sure you can do.

Now lets updated the system to latest:


``` bash
[ec2-user@ip ~]$ sudo yum update
```


## UPDATE

*Before proceeding with this guide, which compiles CouchDB from sources without any automated tool*

I found this project:
https://github.com/iriscouch/build-couchdb

Which maintains its own CouchDB dependencies and perform the build for you, if you choose this approach, check out this post:
[Installing CouchDB instance on Amazon AMI using build-couchdb](/blog/2013/07/10/installing-couchdb-instance-on-amazon-ami-using-build-couchdb/) is a working link



But if you still want to build from sources (which is more depdendent on Amazon's dependencies) proceed reading.


### Installing CouchDB via EPEL

Enabling EPEL repo, we need some packages missing in standard Amazon repos:

``` bash
[ec2-user@ip ~]$ vi /etc/yum.repos.d/epel.repo

...

#change to enabled=1
enabled=1
```


Now if you don't care to work with old CouchDB version, you can simply install it by:

``` bash
[ec2-user@ip ~]$ yum info couchdb
[ec2-user@ip ~]$ yum install couchdb
```

But if this is not sufficient for you like us and you want a more updated version, proceed reading :-)


### Installing latest CouchDB

Current CouchDB version is _1.3.1_, but this guide should work for future versions as well (unless CouchDB folks decides to do major changes)


#### Install Dependencies

We are going to build CouchDB from sources, Couch is written in [http://www.erlang.org/](Erlang), so few dependencies are needed:

(Note: Don't forget to enable EPEL repo, see above!)


``` bash
[ec2-user@ip ~]$ sudo yum groupinstall "Development Tools"
[ec2-user@ip ~]$ sudo yum install perl-Test-Harness
[ec2-user@ip ~]$ sudo yum install erlang-erts
[ec2-user@ip ~]$ sudo yum install erlang-os_mon
[ec2-user@ip ~]$ sudo yum install erlang-eunit
[ec2-user@ip ~]$ sudo yum install libicu-devel
[ec2-user@ip ~]$ sudo yum install autoconf-archive #only available in EPEL repo
[ec2-user@ip ~]$ sudo yum install curl-devel
[ec2-user@ip ~]$ sudo yum install erlang-etap
```

From Couch folks:
>While CouchDB builds against the default js-devel-1.7.0 included in some
>distributions, it's recommended to use a more recent js-devel-1.8.5.

And since Amazon AMI only ships (via EPEL repo) with 1.7.0, we'r going to build js-devel-1.8.5:
If you feel comfortable with js-devel 1.7.0, you can just:

``` bash
[ec2-user@ip ~]$ sudo yum install js-devel
```

OR: if you have the energy, or this is going to be a production environment, then install js-devel from sources:


``` bash
[ec2-user@ip ~]$ cd ~
[ec2-user@ip ~]$ wget http://ftp.gnu.org/gnu/autoconf/autoconf-2.13.tar.gz
[ec2-user@ip ~]$ cd autoconf-2.13
[ec2-user@ip ~]$ ./configure --prefix=/usr/local/autoconf-2.13
[ec2-user@ip ~]$ sudo make install

[ec2-user@ip ~]$ cd ~
[ec2-user@ip ~]$ wget http://ftp.mozilla.org/pub/mozilla.org/js/js185-1.0.0.tar.gz
[ec2-user@ip ~]$ tar zxvfl js185-1.0.0.tar.gz
[ec2-user@ip ~]$ cd js-1.8.5/js/src/
[ec2-user@ip ~]$ /usr/local/autoconf-2.13/bin/autoconf
[ec2-user@ip ~]$ ./configure
[ec2-user@ip ~]$ make
[ec2-user@ip ~]$ sudo make install #this installs js-devel under /usr/local
```


Some references:

* Mozila SpiderMonkey reference: https://developer.mozilla.org/en-US/docs/SpiderMonkey/Build_Documentation
* CouchDB SpiderMonkey reference: http://wiki.apache.org/couchdb/Installing_SpiderMonkey
* CouchDB dependencies requirements: https://github.com/apache/couchdb/blob/master/INSTALL.Unix
* Installing CouchDB on RHEL5: http://wiki.apache.org/couchdb/Installing_on_RHEL5


#### Building CouchDB from sources

``` bash
[ec2-user@ip ~]$ cd ~
[ec2-user@ip ~]$ git clone https://github.com/apache/couchdb.git
[ec2-user@ip ~]$ cd couchdb
[ec2-user@ip ~]$ git checkout -b 1.3.1 1.3.1
[ec2-user@ip ~]$ ./bootstrap #should output: You have bootstrapped Apache CouchDB, time to relax.
[ec2-user@ip ~]$ ./configure --with-erlang=/usr/lib64/erlang/usr/include --with-js-lib=/usr/local/lib/ --with-js-include=/usr/local/include/js/ #should output: You have configured Apache CouchDB, time to relax.
[ec2-user@ip ~]$ make
[ec2-user@ip ~]$ make test
[ec2-user@ip ~]$ make install
```

_Note:_ you can of course replace _1.3.1_ tag with the latest stable version if there's any.

Hey, we'r almost done, lets configure the environment.


#### Create a couch user

For security reasons, create a dedicated couch user and correct files ownership and permissions:

``` bash
[ec2-user@ip ~]$ sudo adduser -r --home /usr/local/var/lib/couchdb -M --shell /bin/bash --comment "CouchDB Administrator" couchdb
[ec2-user@ip ~]$ sudo chown -R couchdb:couchdb /usr/local/etc/couchdb
[ec2-user@ip ~]$ sudo chown -R couchdb:couchdb /usr/local/var/lib/couchdb
[ec2-user@ip ~]$ sudo chown -R couchdb:couchdb /usr/local/var/log/couchdb
[ec2-user@ip ~]$ sudo chown -R couchdb:couchdb /usr/local/var/run/couchdb
[ec2-user@ip ~]$ sudo chmod 0770 /usr/local/etc/couchdb
[ec2-user@ip ~]$ sudo chmod 0770 /usr/local/var/lib/couchdb
[ec2-user@ip ~]$ sudo chmod 0770 /usr/local/var/log/couchdb
[ec2-user@ip ~]$ sudo chmod 0770 /usr/local/var/run/couchdb
```


#### Starting Couch

``` bash
[ec2-user@ip ~]$ sudo /usr/local/etc/rc.d/couchdb start
[ec2-user@ip ~]$ sudo /usr/local/etc/rc.d/couchdb status #should result: Apache CouchDB is running as process 3548, time to relax.
```

Check that things are working:

``` bash
[ec2-user@ip ~]$ curl http://localhost:5984

{"couchdb":"Welcome","uuid":"6d597578de8744f92bdbc99c368c10ca","version":"1.3.1","vendor":{"name":"The Apache Software Foundation","version":"1.3.1"}}
```

#### Starting services when system starts

``` bash
[ec2-user@ip ~]$ sudo ln -s /usr/local/etc/rc.d/couchdb /etc/init.d/couchdb
[ec2-user@ip ~]$ sudo chkconfig --level 345 couchdb on
[ec2-user@ip ~]$ service couchdb stop
[ec2-user@ip ~]$ service couchdb start
```

I suggest you to create a template at this stage, so you can simply create new instances whenever you need,

Hey, you are done, now it's really time to relax :-)