# stono/ike
This container came about because in newer version of openssl I'm unable to connect to peers using older 1024bit certificates.

Unfortuantely those targets aren't going to update their certifcates any time soon, and at the same time I am unwilling to downgrade openssl on my machine to accomodate them!  

This container encapsulates ike(c/d) with `openssl-1.0.1e-30` which is the most current version of openssl that I found which still works with these older certificates.

## Using the container
The container needs to be run with `privileged` and `net=host` as at the end of the day, you're creating a VPN tunnel and it needs to play with your network stack.

You just need to mount in your site(s) to `/sites` and pass in the name of the site, your username and password.

For example:
```
docker run --rm -it --privileged --net=host -v ~/.ike/sites:/sites stono/ike SiteName Username Password
```

`username` and `password` are optional and will only be appended to the ikec command if you pass them.

From that, you'll get:
```
IKEd started.
Starting IKEc for SiteName...
ii : ## : VPN Connect, ver 2.2.1
## : Copyright 2013 Shrew Soft Inc.
## : press the <h> key for help
>> : config loaded for site 'EMEA'
>> : attached to key daemon ...
ii : created ike socket 0.0.0.0:500
ii : created natt socket 0.0.0.0:4500
## : IKE Daemon, ver 2.2.1
## : Copyright 2013 Shrew Soft Inc.
## : This product linked OpenSSL 1.0.1e-fips 11 Feb 2013
>> : peer configured
>> : iskamp proposal configured
>> : esp proposal configured
>> : client configured
>> : local id configured
>> : remote id configured
>> : server cert configured
K! : recv X_SPDDUMP message failure ( errno = 2 )
ii : bringing up tunnel ...
!! : peer violates RFC, transform number mismatch ( 1 != 14 )
!! : phase1 packet ignored, resending last packet ( phase1 already mature )
!! : phase1 packet ignored, resending last packet ( phase1 already mature )
>> : network device configured
!! : config packet ignored ( config already mature )
ii : tunnel enabled
!! : config packet ignored ( config already mature )
!! : peer violates RFC, transform number mismatch ( 1 != 32 )
^CDetected SIGTERM, shuting down...
Stopping ikec...
Stopping iked...
```

I've trapped SIGTERM to gracefully clean up the connection to so just CTRL+C to end it.

## Running as a daemon
Of course you probably dont want a terminal window open all of the time so subsequently you can run it with a daemon like this:

````
docker run -d --privileged --net=host -v ~/.ike/sites:/sites stono/ike SiteName Username Password
```
