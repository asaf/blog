---
tags:
- amazon
- ec2
- ami
- couchdb
comments: true
date: 2013-07-10T00:00:00Z
title: Installing CouchDB instance on Amazon AMI using build-couchdb
url: /2013/07/10/installing-couchdb-instance-on-amazon-ami-using-build-couchdb/
---

{% img http://couchdb.apache.org/image/couch.png 160 180 Couch %}


This post explains how to install CouchDB on Amazon AMI using [build-couchdb](https://github.com/iriscouch/build-couchdb)/

If you want to install couch from sources without build-couchdb tool, read:
[Installing CouchDB instance on Amazon AMI](/blog/2013/07/08/installing-couch-db-on-amazon-ami/) is a working link


build-couchdb does the following things:

1. Install general dependencies via yum (such as gcc, ruby, automake, rubygems, etc.)
1. Clone the 'build-couchdb' project, initalize and update its sub modules (will clone CouchDB itself, curl, gnulib, etc), 
These are all the dependencies that CouchDB needs in order to be compiled.
1. Compiles all dependencies
1. Compiles CouchDB linked with the compiled dependencies.


Okay, go grab coffee and lets start!


### Launch Instance

Login into EC2, and launch a new instanc and choose _Amazon Linux AMI_,
You can of course choose other Linux flavours (you are not going to use Windows right?),

But I really suggest going with Amazon AMI, it is EBS backed, ships with AWS tools installed and has a
RHEL style package management using _yum_.

Once your instance is launched, SSH it, i'm sure you can do.

Now lets updated the system to latest:

``` bash
sudo yum update
```

### Installation

#### Installing OS dependencies

``` bash
sudo yum install gcc gcc-c++ libtool curl-devel python27 help2man texinfo ruby-rdoc zlib-devel openssl-devel make automake rubygems perl git-core 
sudo gem install rake --no-ri --no-rdoc
```

If you don't want to get into troubles where build-couch tries to patch autoconf-2.69 because of old python version (< 2.6) then link to python 2.7,
at least during the time couch is being built.

``` bash
sudo mv /usr/bin/python /usr/bin/python_org
sudo ln -s /usr/bin/python2.7 /usr/bin/python
```

#### Clone build-couchdb

git clone git://github.com/iriscouch/build-couchdb
cd build-couchdb
git submodule init
git submodule update


#### Optional: Upgrade SpiderMonkey

build-couch uses not an official Mozilla SpiderMonkey version, but considered to be very stable, 
if you would like to replace it with other versions then do the following,
*You can safely skip this, proceed only if you have a good reason to do so*

``` bash
sudo yum install mercurial
cd
hg clone --verbose http://hg.mozilla.org/releases/mozilla-release  
cd mozilla-release
hg checkout FIREFOX_XX_0_RELEASE --clean #replace XX with the tag you wish
rm -rf ~/build-couchdb/dependencies/spidermonkey/js
rm -rf ~/build-couchdb/dependencies/spidermonkey/mfbt
cp -r js mfbt ~/build-couchdb/dependencies/spidermonkey
```


#### Building CouchDB and its dependencies

Now lets build Couch, you can replace _1.3.1_ with any CouchDB release you wish to build,
Or change the installation location.

``` bash
sudo rake git="git://git.apache.org/couchdb.git tags/1.3.1" install=/usr/local/couchdb-1.3.1
sudo ln -s /usr/local/couchdb-1.3.1 /usr/local/couchdb
```

Now go eat something, drink (another?) coffee, take a shower (you will have time for all of them, I promise.)


You should expect the following output:
> Plugins done

Now lets see what we've built:

``` bash
cd /usr/local/couchdb-1.3.1
ls bin

> aclocal       autoheader2.13  autom4te2.69    autoreconf2.69  autoupdate2.59  ct_run       erl       genctd       ifnames2.62  pkgdata
> aclocal-1.11  autoheader2.59  automake        autoscan2.13    autoupdate2.62  curl         erlc      genrb        ifnames2.69  run_erl
> autoconf2.13  autoheader2.62  automake-1.11   autoscan2.59    autoupdate2.69  curl-config  escript   icu-config   js-config    run_test
> autoconf2.59  autoheader2.69  autoreconf2.13  autoscan2.62    couch-config    derb         genbrk    icuinfo      libtool      to_erl
> autoconf2.62  autom4te2.59    autoreconf2.59  autoscan2.69    couchdb         dialyzer     gencfu    ifnames2.13  libtoolize   typer
> autoconf2.69  autom4te2.62    autoreconf2.62  autoupdate2.13  couchjs         epmd         gencnval  ifnames2.59  makeconv     uconv
```


Lets execute Couch to ensure it can be executed.

``` bash
sudo ./bin/couchdb

> Apache CouchDB 1.3.1 (LogLevel=info) is starting.
> Apache CouchDB has started. Time to relax.
> [info] [<0.32.0>] Apache CouchDB has started on http://127.0.0.1:5984/
```


You can open another terminal to ensure Couch replies properly:

``` bash
curl http://127.0.0.1:5984

> {"couchdb":"Welcome","uuid":"9db29433ea76ccfaa81726f6ace742f4","version":"1.3.1","vendor":{"name":"The Apache Software Foundation","version":"1.3.1"}}
```


### Configuration

#### Replace Python link

To avoid breaking yum and other things, it is adviced to return the python binary link back to its original version

``` bash
sudo rm /usr/bin/python
sudo mv /usr/bin/python_org /usr/bin/python
```

#### Run Couch on isolated user and correct files ownership

``` bash
# add couchdb user
sudo adduser --system --home /usr/local/couchdb/var/lib/couchdb -M --shell /bin/bash --comment "CouchDB" couchdb

# change file ownership
sudo chown -R couchdb:couchdb /usr/local/couchdb/etc/couchdb /usr/local/couchdb/var/lib/couchdb /usr/local/couchdb/var/log/couchdb /usr/local/couchdb/var/run/couchdb
```

#### couch ini file

Lets create couch ini file

``` bash
cat << 'EOF' > /usr/local/couchdb/etc/couchdb/local.ini
[couchdb]
#read more here: http://guide.couchdb.org/draft/performance.html
delayed_commits = false

[httpd]
port = 80
bind_address = 0.0.0.0
socket_options = [{recbuf, 262144}, {sndbuf, 262144}, {nodelay, true}]
WWW-Authenticate = Basic realm="administrator"
;WWW-Authenticate = bummer

[couch_httpd_auth]
require_valid_user = true

[log]
level = error

[admins]
EOF
```

#### Password protection

``` bash
export PASS=`</dev/urandom tr -dc '12345!@#$%qwertQWERTasdfgASDFGzxcvbZXCVB' | head -c20; echo ""`
echo "admin = ${ADMIN_PASSWORD}" >> /usr/local/etc/couchdb/local.ini
```
Couch is smart enough to hash the passwords.


#### Logs Rotation

``` bash
if [ ! -e "/etc/logrotate.d/couchdb" ]
then
  # add couch.log to logrotate
  sudo ln -s /usr/local/couchdb/etc/logrotate.d/couchdb /etc/logrotate.d/
  # change to daily rotation
  sudo sed -e s/weekly/daily/g -i /usr/local/couchdb/etc/logrotate.d/couchdb
  #logrotate -v -f /etc/logrotate.d/couchdb 
fi
```

#### Run CouchDB on startup

``` bash
# run couchdb on startup
sudo ln -s /usr/local/couchdb/etc/rc.d/couchdb /etc/init.d/couchdb
sudo chkconfig --add couchdb
sudo chkconfig --level 345 couchdb on

sudo service couchdb status
#Apache CouchDB is not running.

sudo service couchdb start
#Apache CouchDB is running as process 5047, time to relax.

```

Now it's really time to relax, enjoy :-)
