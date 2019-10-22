Random Dockerfiles

## Development KDC

Run a dev KDC and exposes a usable generated config and as many keytabs are
requested SPNs.

``` sh
$ docker build -t dev-kdc kdc
```

``` sh
$ mkdir data
$ chmod 1777 data
$ KDC_CONTAINER=$(docker run --rm \
    -e REALM=KAZOOTEST.ORG \
    -e SPNS="client zookeeper/localhost" \
    -v "$(pwd)/data":/kdc-data \
    -p 1080:1080
    dev-kdc)
```

Note the files will be owned by the `daemon` user.

``` sh
$ find data
data
data/krb5kdc
data/krb5kdc/principal
data/krb5kdc/kdc.pid
data/krb5kdc/principal.kadm5.lock
data/krb5kdc/principal.kadm5
data/krb5kdc/principal.ok
data/krb5kdc/stash
data/keytabs
data/keytabs/zookeeper#localhost.keytab
data/keytabs/client.keytab
data/logs
data/logs/krb5kdc.log
data/logs/kadmind.log
data/krb5.conf
```

Example usage from another container:

``` sh
$ docker run --rm -it \
           -v "$(pwd)/data":/kdc-data:ro \
           --net=container:${KDC_CONTAINER} \
           debian:buster-slim \
           /bin/bash

root@a5f219496923:/# apt update && apt install -y krb5-user
root@a5f219496923:/# KRB5_CONFIG=/kdc-data/krb5.conf \
    kinit \
        -kt /kdc-data/keytabs/client.keytab \
        client@KAZOOTEST.ORG
Using default cache: /tmp/krb5cc_0
Using principal: client@KAZOOTEST.ORG
Using keytab: /kdc-data/keytabs/client.keytab
Authenticated to Kerberos v5
root@a5f219496923:/# klist -dfean
Ticket cache: FILE:/tmp/krb5cc_0
Default principal: client@KAZOOTEST.ORG

Valid starting     Expires            Service principal
10/22/19 02:53:49  10/23/19 02:53:49  krbtgt/KAZOOTEST.ORG@KAZOOTEST.ORG
        renew until 10/22/19 02:53:49, Flags: FRI
        Etype (skey, tkt): aes256-cts-hmac-sha1-96, aes256-cts-hmac-sha1-96 , AD types:
        Addresses: (none)
```
