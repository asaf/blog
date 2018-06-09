---
tags:
- ec2
- aws
- nodejs
comments: true
date: 2013-07-10T00:00:00Z
title: 'Nodejs Deployment: Building and Configuring on Amazon Linux AMI'
url: /2013/07/10/nodejs-deployment-building-and-configuring-on-amazon-linux-ami/
---

### Logging in and updating system to latest

SSH your shiny new VM,

Now lets update the system to the latest:

``` bash
sudo yum update
```

### Install OS dependencies

We'r going to build Node.js from sources, some dependencies (such as gcc) are required:

``` bash
sudo yum install gcc-c++ make openssl-devel git
```

### Cloning n Building Node.js

``` bash
cd
git clone git://github.com/joyent/node.git
cd node
git checkout v0.10.13 #check for other stable tags by executing 'git tag'
./configure --prefix=/usr/local/node
make
sudo make install
```

Go grab a cup of coffee... :-)
...


### Configuration

``` bash
sudo useradd _yourappuser_
passwd _yourappuser_
sudo su - _yourappuser_
```

### Put your app

Now put your app in ~, for instance:

``` bash
cd
pwd
#/home/_yourappuser_
git clone _https://myhost.com/myapp myapp_
```

### Init.d

We would like to have nodejs to start automatically as a service, to do so, lets create an init.d file
_Note: you have to change the properties in the file such as yourappuser, myapp to your app folder and server.js to your node app file._


``` bash
cat << 'EOF' > /etc/init.d/nodejs

#!/bin/sh

#
# chkconfig: 35 99 99
# description: Node.js /home/yourappuser/myapp/app.js
#

. /etc/rc.d/init.d/functions

USER="_yourappuser_"

NODE_ENV="production"
DAEMON="/usr/local/node/bin/node"
ROOT_DIR="/home/yourappuser/myapp"

SERVER="$ROOT_DIR/server.js"
LOG_FILE="$ROOT_DIR/app.js.log"

LOCK_FILE="/var/lock/subsys/node-server"

do_start()
{
        if [ ! -f "$LOCK_FILE" ] ; then
                echo -n $"Starting $SERVER: "
                runuser -l "$USER" -c "NODE_ENV=$NODE_ENV $DAEMON $SERVER >> $LOG_FILE &" && echo_success || echo_failure
                RETVAL=$?
                echo
                [ $RETVAL -eq 0 ] && touch $LOCK_FILE
        else
                echo "$SERVER is locked."
                RETVAL=1
        fi
}
do_stop()
{
        echo -n $"Stopping $SERVER: "
        pid=`ps -aefw | grep "$DAEMON $SERVER" | grep -v " grep " | awk '{print $2}'`
        kill -9 $pid > /dev/null 2>&1 && echo_success || echo_failure
        RETVAL=$?
        echo
        [ $RETVAL -eq 0 ] && rm -f $LOCK_FILE
}

case "$1" in
        start)
                do_start
                ;;
        stop)
                do_stop
                ;;
        restart)
                do_stop
                do_start
                ;;
        *)
                echo "Usage: $0 {start|stop|restart}"
                RETVAL=1
esac

exit $RETVAL

EOF
```

Add execution permission to the nodejs init script

``` bash
sudo chmod +x /etc/init.d/nodejs
```


### Pre Routing to port 80

Linux does not allow non super users to listen to ports < 1024, assuming your application listen to port _8080_,
You would probably like to pre route any traffic arriving from port _80_ to your node app that listens to port _8080_

You can do this by the pre routing nat capability of _Iptables_

``` bash
chkconfig iptables on
service iptables start
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8443 #if you want SSL too
service iptables save
```



### Configuring node-http-proxy

It is common to install http proxies such as _nginx_ on front of nodejs,
This architecture has many advantages such as raising security level, listening natively to port 80, load balancing, multiple node apps support
via url rewrite, etc...

I personally think that the best approach, which is very native to node apps is to use https://github.com/nodejitsu/node-http-proxy,

Which have several advantages:

1. Reverse proxies incoming http.ServerRequest streams, WebSockets, HTTPS
1. Minimal request overhead and latency
1. Battled-hardened through production usage
1. Very native for nodejs apps


TODO: Will post more details in the future but you can simply visit ["https://github.com/nodejitsu/node-http-proxy"](node-http-proxy) site.



https://github.com/pkrumins/nodejs-proxy
