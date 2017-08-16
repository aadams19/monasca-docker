Dockerfiles for Monasca
=======================

This repository contains resources for building and deploying a full Monasca
stack in Docker and Kubernetes environments.

Requirements
------------

 * docker, version >= 1.13 recommended
 * docker-compose >= 1.9.0
 * at least 4 GiB of RAM
 * 2 CPUs (cores) or more recommended

Quick Start
-----------

To quickly get a Monasca environment running, you can use [docker-compose][1]:

    docker-compose up

Assuming all goes well, the following services should be exposed on your host
machine:

 * keystone on ports 5000 and 35357
  * see [`preload.yml`][2] for full account info
 * monasca-api on port 8070
 * grafana on port 3000
  * log in with `mini-mon` and `password` (or any valid keystone account)

If needed, `docker-compose rm` can be used to completely clean the environment
between runs.

Repository Layout
-----------------

 * `monasca-api-python/`: Dockerfile for monasca-api (python version)
 * `monasca-persister-python/`: Dockerfile for monasca-persister (python
   version)
 * `monasca-notification/`: Dockerfile for the notification engine
 * `monasca-agent/`: Dockerfile for Monasca Agent

There are also several run-once init containers used to bootstrap a new
Monasca installation:
 * `grafana-init/`: pre-loads Grafana with a Monasca datasource and several
   default dashbaord
 * `influxdb-init/`: creates influxdb users and databases
 * `monasca-thresh/`: job to submit the monasca-thresh Storm topology
 * `mysql-init/`: initializes MySQL users, databases, and schemas

A number of custom dependency containers are also here:

 * `grafana/`: Dockerfile for Grafana with Keystone auth and Monasca plugins
 * `keystone/`: Dockerfile for dev keystone
 * `kafka/`: Dockerfile for k8s-compatible kafka
 * `storm/`: Dockerfile for Storm container

Version control
---------------

**monasca-docker** leverages the [environmental variable handling][3] of 
[docker-compose][1]. The latest release, a.k.a. versions of the images that
are used for *master* branch is controlled with the help of [.env](./.env) file.
In overall the file contains all the images' versions and resembles the following
code snippet:

```sh
INFLUXDB_VERSION=1.3.2-alpine
INFLUXDB_INIT_VERSION=latest
MYSQL_VERSION=5.5
MYSQL_INIT_VERSION=1.5.1
MEMCACHED_VERSION=1.5.0-alpine
CADVISOR_VERSION=v0.26.1
ZOOKEEPER_VERSION=3.4

MON_SIDECAR_VERSION=1.0.0
MON_KEYSTONE_VERSION=1.1.1
MON_KAFKA_VERSION=0.9.0.1-2.11-1.0.2
MON_ALARMS_VERSION=1.1.0
MON_GRAFANA_VERSION=4.0.0-1.1.1
MON_GRAFANA_INIT_VERSION=1.1.0

MON_API_VERSION=master
MON_PERSISTER_VERSION=master
MON_THRESH_VERSION=latest
MON_NOTIFICATION_VERSION=master
MON_AGENT_FORWARDER_VERSION=master
MON_AGENT_COLLECTOR_VERSION=master
```

This file is automatically picked by [docker-compose][1], hence there are no ```export```
instructions. 

In order to override certain images versions, for example to create OpenStack Ocata based deployment,
you may want to create a file, similar to this one:

```sh

export INFLUXDB_VERSION=1.1.0-alpine
export MON_API_VERSION=ocata
export MON_PERSISTER_VERSION=ocata
export MON_THRESH_VERSION=ocata
export MON_NOTIFICATION_VERSION=ocata
export MON_AGENT_FORWARDER_VERSION=ocata
export MON_AGENT_COLLECTOR_VERSION=ocata

```

That file, called ```.ocata.env```, differs from [.env](./.env), as it contains ```export```
instructions. That is mandatory approach, as sourced variables takes precedence over
aformentioned environmental file. 

    Alternative approach, that achieves the same result, is to override values
    of certain variables directly in ```.env``` file.

[1]: https://docs.docker.com/compose/
[2]: https://github.com/hpcloud-mon/monasca-docker/blob/master/keystone/preload.yml
[3]: https://docs.docker.com/compose/environment-variables/
