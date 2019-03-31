# cartodb-redis
### Redis cache server for cartodb-docker

`ihme/cartodb-redis` image on DockerHub: https://hub.docker.com/r/ihme/cartodb-redis/

This image lightly extends the [official Redis Docker image](https://hub.docker.com/_/redis/), configuring it to persist data on disk. Note, however, that you'll need to mount a volume on which to store this data (using the container path `/data`). The sample `docker-compose.yml` provided in this repo doesn't do so, because we don't know where you want to store the data.

Further configuration can be passed to the Redis server by supplying a custom `command`. Existing configuration can be overriden by supplying a custom `entrypoint`.

**Note:** 
> At this moment CARTO requires Redis version 4.0 or newer with the redis-cell extension."
https://cartodb.readthedocs.io/en/latest/components/redis.html 
