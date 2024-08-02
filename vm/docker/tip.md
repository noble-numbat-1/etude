# DOCKER TIP

## CREATE PG CONTAINER

```bash
# Create PGDB container
$ docker run --name containerName -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres \
-p 5433:5432 -v volumeName:/var/lib/postgresql/data -d postgres:pgVersion
```

## COPY FILE TO DOCKER VOLUME

```bash
# Create a dummy container and mount the volume
$ docker container create --name dummy -v volumeName:/root alpine tail -f /dev/null

# Copy file to volume through the dummy container
$ docker cp c:\path\to\file.txt dummy:/root/file.txt

# Remove the dummy container
$ docker rm dummy
```

## CONNECT TO RUNNING CONTAINER

```bash
# Connect/execute to container
# Use the container supported shell: bash, sh, etc
$ docker exec -it containerName bash
```
