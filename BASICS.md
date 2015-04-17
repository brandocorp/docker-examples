# Docker Basics

## Installing Docker

* https://docs.docker.com/installation/

## Docker Hub

* Create an [account](https://hub.docker.com/account/login/)

## Images
### pulling an existing image

```
docker pull busybox
docker pull debian
```

### Containers
#### Running a Container

```
docker run debian echo 'Hello, world.'
```

#### Running in the Background

```
docker run -d debian echo 'Hello, world.'
```

#### List Running Containers

```
docker ps
```

#### Listing All Containers

```
docker ps -a
```

#### auto-remove

```
docker run --rm debian echo 'Auto-removing myself'
```

#### Arbitrary Container Names

Container names by default are random, but you can force a container to use a specific name. Keep in mind that container names must be unique, so attempting to create a container with a name that is in use will result in an error.

```
docker run --name hello_world debian echo 'Hello, world.'
```

#### arbitrary hostname

By default your container's host name will be its ID. You can force a container to use a specified host name, rather the default.

```
docker run --rm -it --hostname hello-world debian bash
```

#### stop / start / kill / rm

```
cid=$(docker run -d httpd)
docker stop ${cid}
docker ps -a
docker start ${cid}
docker ps -a
docker kill ${cid}
docker ps -a
docker rm ${cid}
```

#### Using Exec to Mimic SSH

Most containers will not be running an SSH process, so to access a running container you can use the exec to provide SSH-like access.

```
cid=$(docker run -d httpd)
docker exec -it ${cid}
```

#### Container Logs

Logging to `STDOUT` means there's no file to view logs inside the container.

```
cid=$(docker run -d -p 80 httpd)
docker logs ${cid}
docker logs -f ${cid}
```

#### Container Processes

Most base images do not include the `procps` package.

```
docker top ${cid}
```

#### additional hosts

```
cid=$(docker run -d httpd)
ip=$(docker inspect --format '{{ .NetworkSettings.IPAddress }}' ${cid})

docker run --rm -it --add-host apache2:${ip} debian bash
```

#### linking containers

```
docker run --rm -it --link ${cid}:apache2 debian bash
```

#### mapping ports

```
docker run -d -p 8080:80 httpd
```

* mappings are host:container or automatically mapped

```
docker run -d --name apache-uno -p 80 httpd
docker run -d --name apache-dos -p 80 httpd
```

#### attaching volumes from the host

* Note about boot2docker limitations for mounted volumes

```
mkdir -p ~/tmp/httpd
echo '<h1>Ohai</h1>' > ~/tmp/httpd/index.html
```

* create a container with a local volume

```
docker run -d --name apache-uno -p 80 -v "$PWD":/usr/local/apache2/htdocs/ httpd
```

#### attaching volumes from another container

* create a container with a shared volume

```
docker run -d --name apache-dos -p 80 --volumes-from=apache-uno httpd
```
