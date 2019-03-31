# cartodb-postgis
### PostGIS database server for cartodb-docker

`ihme/cartodb-postgis` image on DockerHub: https://hub.docker.com/r/ihme/cartodb-postgis/

This image contains a PostGIS database server with extensions specific to Carto, taken from the [cartodb-postgresql](https://github.com/CartoDB/cartodb-postgresql) GitHub repo. Its image tag references the corresponding version of that repo.  

[More information about CartoDB Components]( https://cartodb.readthedocs.io/en/latest/components/postgresql.html)

## Data Persistence

If you want to preserve the contents of the database beyond the life of the container itself (recommended), you'll need to mount a volume to the data directory (`/var/lib/postgresql/data` by default). The sample `docker-compose.yml` provided in this repo doesn't do so, because we don't know where you want to store the data.

## Configuration

There are several options for passing custom configuration to the server. The simplest option is to specify command-line flags to the `postgres` executable by passing them as the `command` to the container (`entrypoint` already invokes the executable itself via a startup script). We use the official [Postgres Docker image](https://hub.docker.com/_/postgres/) as a parent image. Consult the documentation for that image for details on how to further configure the server.
