### Docker postgres

Postgres database with docker image from https://hub.docker.com/_/postgres/.

From app directory run `mkdir data` and `mkdir dumpall`

Copy `sample.docker-compose.yml` to `docker-compose.yml`. 
Open `docker-compose.yml` and set postgres version, volumes for data and
dumpfiles as well as user and password (NOTE if you want to change
password for user initally created one need to do with psql or delete
the database if there is no important suff in it). 

Run `docker-compose up` -d in app directory to start service.

## Migrate
To migrate data at major database change e.g. 9.5->9.6 look at

https://www.postgresql.org/docs/9.X/static/upgrading.html X=4,5,6 or other

In short
* Enter container (sudo docker exec -i -t {container name} /bin/bash) and run pg_dumpall -U postgres -f /dumpall/{file_name} (can use other user than postgres)
* Exit container, stop container, move data directory e.g mv data data.old.9.X.
* Change image to new in docker-compose.yml and run docker-compose up -d
* Enter container and run psql -d postgres -f {path to dumpall file}
* Done!!!

Alternative with less downtime
* Start new postgres in another container mapped to another port (5433)
* Then enter container and run pg_dumpall -p 5432 | psql -d postgres -p 5433
* Stop old and new container. Change port on new. Bring it up again with docker-compose up -d
* Remove old container
* Done!!!!
