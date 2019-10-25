Assuming you have a KDC container running and your `$CWD` is the top level of
this project, you can obtain a SASL+GSSAPI Zookeeper server and a client like
so.

Start the server:
-----------------

```sh
docker run \
    -d --rm \
    --name test-zk \
    --net=container:${KDC_CONTAINER} \
    -v "$(pwd)/examples/zookeeper/zkconf:/conf" \
    -v "$(pwd)/data:/kdc-data:ro" \
    -e SERVER_JVMFLAGS="-Djava.security.auth.login.config=/conf/jaas.conf
                        -Djava.security.krb5.conf=/kdc-data/krb5.conf" \
    zookeeper:3.5.6
```

Connect a client:
-----------------

```sh
docker exec \
    -it \
    -e CLIENT_JVMFLAGS="-Djava.security.auth.login.config=/conf/jaas.conf
                        -Djava.security.krb5.conf=/kdc-data/krb5.conf
                        -Dzookeeper.sasl.client.username=server" \
    test-zk \
    zkCli.sh -server localhost:2181
```
