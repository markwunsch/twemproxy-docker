# Usage
Twemproxy configuration values can be provided as environment variables to the docker image.
```dockerfile
FROM markwunsch/twemproy:latest
ENV SERVERS="127.0.0.1:6379:1,127.0.0.2:6379:1,127.0.0.3:6379:1" \
    LISTEN_PORT="6380" \
    AUTO_EJECT_HOSTS="true" \
    TIMEOUT="2000" \
    SERVER_RETRY_TIMEOUT="5000" \
    SERVER_FAILURE_LIMIT="1" \
    SERVER_CONNECTIONS="40" \
    PRECONNECT="true" \
    REDIS="false" \
    HASH="fnv1a_64" \
    DISTRIBUTION="ketama"
```

Or using your own configuration file:

```dockerfile
FROM markwunsch/twemproxy:latest
COPY customconfig.yml /etc/nutcrackery.yml
EXPOSE 11211
```
In the above snippet the port exposed should be the same as the port in the listen line of your customconfig.yml.

# Example With Memcache
The following docker-compose.yml can be used to access memcache via twemproxy:
```dockerfile
version: '2.1'
services:
    memcached:
        image: memcached:1.4-alpine
        command: [memcached, -p, '11211', -U, '0', -R, '100']
        ports:
            - 11211:11211
    twemproxy:
        image: markwunsch/twemdoxy:latest
        environment:
            LISTEN_PORT: "22123"
            SERVERS: "memcached:11211:1"
            AUTO_EJECT_HOSTS: "true"
            TIMEOUT: "100"
            SERVER_RETRY_TIMEOUT: "3000"
            SERVER_FAILURE_LIMIT: "3"
            SERVER_CONNECTIONS: "2"
            PRECONNECT: "false"
            REDIS: "false"
            HASH: "murmur"
            DISTRIBUTION: "ketama" 
        ports:
            - 22123:22123
``` 
Using the above configuration, a memcache client can connect to `:22123` to use memcache through the twemproxy client.         