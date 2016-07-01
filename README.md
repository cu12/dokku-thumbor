# dokku thumbor (beta) [![Build Status](https://img.shields.io/travis/cu12/dokku-thumbor.svg?branch=master "Build Status")](https://travis-ci.org/cu12/dokku-thumbor) [![IRC Network](https://img.shields.io/badge/irc-freenode-blue.svg "IRC Freenode")](https://webchat.freenode.net/?channels=dokku)

Thumbor plugin for dokku. Currently defaults to installing [thumbor latest](https://hub.docker.com/r/apsl/thumbor/).

## requirements

- dokku 0.4.x+
- docker 1.8.x

## installation

```shell
# on 0.4.x+
dokku plugin:install https://github.com/cu12/dokku-thumbor.git thumbor
```

## commands

```
thumbor:create <name>            Create a thumbor service with environment variables
thumbor:destroy <name>           Delete the service and stop its container if there are no links left
thumbor:expose <name> [port]     Expose a thumbor service on custom port if provided (random port otherwise)
thumbor:info <name>              Print the connection information
thumbor:link <name> <app>        Link the thumbor service to the app
thumbor:list                     List all thumbor services
thumbor:logs <name> [-t]         Print the most recent log(s) for this service
thumbor:promote <name> <app>     Promote service <name> as THUMBOR_URL in <app>
thumbor:restart <name>           Graceful shutdown and restart of the thumbor service container
thumbor:start <name>             Start a previously stopped thumbor service
thumbor:stop <name>              Stop a running thumbor service
thumbor:unexpose <name>          Unexpose a previously exposed thumbor service
thumbor:unlink <name> <app>      Unlink the thumbor service from the app
```

## usage

```shell
# create a thumbor service named lolipop
dokku thumbor:create lolipop

# you can also specify the image and image
# version to use for the service
# it *must* be compatible with the
# apsl/thumbor image
export THUMBOR_IMAGE="thumbor"
export THUMBOR_IMAGE_VERSION="5.2.0"

# create a thumbor service
dokku thumbor:create lolipop

# get connection information as follows
dokku thumbor:info lolipop

# a thumbor service can be linked to a
# container this will use native docker
# links via the docker-options plugin
# here we link it to our 'playground' app
# NOTE: this will restart your app
dokku thumbor:link lolipop playground

# the following environment variables will be set automatically by docker (not
# on the app itself, so they won’t be listed when calling dokku config)
#
#   DOKKU_THUMBOR_LOLIPOP_NAME=/lolipop
#   DOKKU_THUMBOR_LOLIPOP_PORT=tcp://172.17.0.1:8000
#   DOKKU_THUMBOR_LOLIPOP_PORT_3306_TCP=tcp://172.17.0.1:8000
#   DOKKU_THUMBOR_LOLIPOP_PORT_3306_TCP_PROTO=tcp
#   DOKKU_THUMBOR_LOLIPOP_PORT_3306_TCP_PORT=8000
#   DOKKU_THUMBOR_LOLIPOP_PORT_3306_TCP_ADDR=172.17.0.1
#
# and the following will be set on the linked application by default
#
#   THUMBOR_URL=http://dokku-thumbor-lolipop:8000
#
# NOTE: the host exposed here only works internally in docker containers. If
# you want your container to be reachable from outside, you should use `expose`.

# another service can be linked to your app
dokku thumbor:link other_service playground

# since THUMBOR_URL is already in use, another environment variable will be
# generated automatically
#
#   DOKKU_THUMBOR_BLUE_URL=http://dokku-thumbor-other-service:8000

# you can then promote the new service to be the primary one
# NOTE: this will restart your app
dokku thumbor:promote other_service playground

# this will replace THUMBOR_URL with the url from other_service and generate
# another environment variable to hold the previous value if necessary.
# you could end up with the following for example:
#
#   THUMBOR_URL=http://dokku-thumbor-other_service:8000
#   DOKKU_THUMBOR_BLUE_URL=http://dokku-thumbor-other-service:8000
#   DOKKU_THUMBOR_SILVER_URL=http://dokku-thumbor-lolipop:8000

# you can also unlink a thumbor service
# NOTE: this will restart your app and unset related environment variables
dokku thumbor:unlink lolipop playground

# you can tail logs for a particular service
dokku thumbor:logs lolipop
dokku thumbor:logs lolipop -t # to tail

# finally, you can destroy the container
dokku thumbor:destroy lolipop
```
