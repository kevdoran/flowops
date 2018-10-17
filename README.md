# FlowOps

Automated, repeatable tasks for [Apache NiFi](https://nifi.apache.org) data flow operations.

## Quickstart Guide

### FlowOps Docker Image

The FlowOps Docker image comes with useful tools for automating Apache NiFi data flow operations, such as the [Apache NiFi Toolkit CLI](https://github.com/apache/nifi/tree/master/nifi-toolkit/nifi-toolkit-cli) and [NiPyAPI](https://github.com/Chaffelson/nipyapi)

Pull from [Dockerhub](https://hub.docker.com/r/kevdoran/flowops/):

    docker pull kevdoran/flowops:latest

Run interactively:

    docker run -it kevdoran/flowops:latest
    
This will drop you into a bash shell on a flowops container. Most of the commands you need to get started are already on the path (e.g., the NiFi Toolkit /bin directory). Also, there are a number of [scripts](#scripts) located in `/scripts` that you may find useful. 

If you want to use the FlowOps image to control NiFi or Registry containers running in an existing environment (e.g., one created by docker-compose), just specify the network those containers are bound too:

    docker run -it --network <docker-network-name> flowops:latest

Lastly, FlowOps is designed to be included as part of a docker-compose configuration. For an example, see the NiFi & NiFi Registry [docker-compose.yml](https://github.com/kevdoran/flowops/blob/master/docker/demo/nifi-registry/docker-compose.yml) demo.

## Scripts

The FlowOps Docker image comes with a number of scripts to facilitate common actions. These are located in the `/scripts` directory in the flowops image.

TODO, provide documentation for provided / built-in scripts.

## Demos

### Apache NiFi & NiFi Registry

This demo leverages FlowOps scripts to connect Apache NiFi and NiFi Registry containers in a docker-compose environment. See the [docker-compose.yml](https://github.com/kevdoran/flowops/blob/master/docker/demo/nifi-registry/docker-compose.yml) file in this repo for details.

#### From master branch

    wget -qO- https://raw.githubusercontent.com/kevdoran/flowops/master/docker/demo/nifi-registry/docker-compose.yml | docker-compose -f - up
    
#### From a stable release

    FO_VERSION=0.1.0 \
    && wget https://github.com/kevdoran/flowops/archive/v${FO_VERSION}.tar.gz -O flowops-${FO_VERSION}.tar.gz \
    && tar -xzf flowops-v${FO_VERSION}.tar.gz \
    && cd flowops-0.1.0/docker/demo/nifi-registry \
    && docker-compose up

In the above example, replace `FO_VERSION=0.1.0` with the [release version](https://github.com/kevdoran/flowops/releases) you wish to use.
