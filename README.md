Random Dockerfiles

## Development KDC

Run a dev KDC and exposes a usable generated config and as many keytabs are
requested SPNs.

``` sh
docker run --rm \
    -e REALM=KAZOOTEST.ORG \
    -e SPNS="client zookeeper/localhost" \
    -v "$(pwd)/data":/kdc-data \
    -p 1080:1080 
    dev-kdc
```

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
```
