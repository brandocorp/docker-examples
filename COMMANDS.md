# docker commands

## Working with Containers

### Overriding the `ENTRYPOINT`

Some containers have a custom entrypoint, such as `/docker/run` which serves as the initializer for the container process. It may be useful for troubleshooting to get in front of the initializer. This can be achieved by overriding the entrypoint on the command line.

Example: Run a new container with a bash shell

```
docker run -it --entrypoint bash myimage:latest
```

### Overriding the `CMD`

All of the arguments following the image name in a docker run command become an override for the `CMD` value of the container.

Example: Run the `ls` command and exit

```
docker run myimage:tag ls -l /
```

### Listing All Running Containers

You can see a list of all running containers by running the `docker ps` command.

Example: See running containers

```
docker ps
```

Example: See all containers, including those which have exited

```
docker ps -a
```

### Getting advanced details for a running container

You can use the `docker inspect` command to see all the details of a running container.

Example: get the details of a container

```
docker inspect ${cid}
```

You may also provide an optional formatting to return specific details about the container

Example: get the IP address of a container

```
docker inspect --format='{{.NetworkSettings.IPAddress}}' ${cid}
```

### Running commands in a running container

If you need to run a command against a currently running container, you can use the `docker exec` command.

Example: see the content of a file

```
docker exec ${cid} cat /path/to/file
```

## Getting Logs

Docker stores all output sent to `stdout` and `stderr` in its own internal logging. You can view these log messages by running the `docker logs` command.

In addition to using the `logs` command, you can also find the log data in a json file stored under `/var/lib/docker/containers/${cid}/${cid}-json.log`

Example: view the logs from a container

```
docker logs ${cid}
```

**Note:** This command becomes less and less useful over the life of a container as the size of the logs grow. Each time you run the command, it will return all log messages, similar to the behavior of the `cat` command.

## Working with Images

Example: Show all images

```
docker images
```

Example: See the modification history of an image

```
docker history ${iid}
```

Example: Remove an image

```
docker rmi ${iid}
```

Example: Remove images no longer in use

```
docker images -q --filter='dangling=true' | xargs docker rmi
```

Example: cleanup older tags of an image between 3.0.0 and 3.0.2 leaving 3.0.3

```
docker images | grep image-name | grep '3.0' | grep -v '3.0.[3]' | awk '{print $3}' | xargs docker rmi
```

## Orphaned Volumes

See: https://github.com/chadoe/docker-cleanup-volumes

```
docker run -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker:/var/lib/docker --rm martin/docker-cleanup-volumes:1.4.1 --dry-run
```
