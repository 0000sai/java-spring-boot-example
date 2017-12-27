# WIP WIP WIP 

## TODO 
 * java part out of this repo and into its own and use git submodule
 * build and run in seperate processes

# Description 

Uses NodePKI to generate SSL certificates for each server/service and preconfigures those servers to use the certificates. 

Installs the following servers:
 * Socks5 proxy
 * NodePKI
 * Nexus
 * Gitlab
 * RabbitMQ
 * Example java application

# Installation

## Generate and install a new Root Certificate

```
$ docker-compose up -d proxy nodepki
$ docker-compose run nodepki ash -c \
    "cd /root/nodepki && node /root/nodepki/nodepkictl.js useradd --username admin --password admin"

```

After that:

 * Install a proxy switcher
 * Set proxy url to socks5://localhost:1080
 * Goto http://nodepki and login with `username=admin` and `password=admin`
 * Goto http://nodepki:5000/cacerts and install the newly gegerated `Root Certificate` 
 * http://nodepki:5000/request and install the following servers with the `Common name`
   * gitlab
   * nexus
   * rabbitmq
   * graylog

## Gitlab

```
$ docker-compose run gitlab bash -c \
    "cd /etc/gitlab/ssl &&\ 
     ln -s domain.key gitlab.key &&\
     ln -s cacert.crt gitlab.crt &&\
     cp root.pem /etc/gitlab/trusted-certs"
$ docker-compose up -d gitlab
```

Goto https://gitlab, set admin password for user `root` and login 


## Git clone project 

```
$ git clone
```

## Build
```
$ gradle assemble
```

## Test
```
$ gradle test
```

## Run
```
$ gradle bootRun
```

## Documentation
```
$ gradle javadoc
```

# Deploy

## Nexus

`~/.gradle/gradle.properties`:

```
nexusUsername = admin
nexusPassword = admin123
```

```
$ gradle uploadArchives
```

## Docker

```
$ gradle dockerPushImage
```

```
$ docker-compose up -d proxy graylog rabbitmq
$ docker-compose up producer
```

# "I want to fix a bug!!!"

```
$ git clone \
 cd producer-xml &&\
 docker-compose up -d proxy rabbitmq graylog2 &&\
 gradle dockerBuildImage &&\
 docker-compose up producer
```

and attach a debugger on port 5005 (see docker-compose.yml, JAVA_OPTS)
