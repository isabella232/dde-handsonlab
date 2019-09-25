# Docker Desktop Enterpise Hands on Lab

Welcome to a Docker Desktop Enterprise hands on Lab. This lab will walk you
through:

- Creating your first Containerised Micro Service
- Creating a Multi-Service Containerised Application
- Leverage Docker Templates and Docker Application Designer to jump start your
  Micro Service Application development. 
- Deploy your Micro Serviced based application on to Kubernetes.
- Distribute your Micro Serviced based application through Docker App and Docker
  Hub.

## Pre-Requisities

- Docker Desktop Enterprise installed.
- The Lab's Application Library pre-loaded into your DDE's Application Designer.
- The IDE of your choice locked and loaded.

## Link Extractor App

Link Extractor app scrapes links from a given web page.  This repository
illustrates step by step approach to learn [Docker](https://www.docker.com/).

It starts from running a very basic Python script and gradually leads to a
multi-service container orchestration (AKA micro-service architecture).

**No Python skills required**
This workshop uses a Python example but no Python expertise is assumed or required. Step 6 switches to Ruby, but again, no Ruby knowledge is required.

## How to Use the Repository

There are multiple folders in this repo which correspond to steps in the
workshop.

Before you get started, verify the Docker Desktop Enterprise is working on your
machine. Open a Terminal and run:

```bash
> docker version
Client: Docker Engine - Enterprise
 Version:           19.03.2
 API version:       1.40
 Go version:        go1.12.8
 Git commit:        c92ab06
 Built:             Thu Aug 29 13:12:28 2019
 OS/Arch:           windows/amd64
 Experimental:      true

Server: Docker Engine - Enterprise
 Engine:
  Version:          19.03.2
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.8
  Git commit:       c92ab06
  Built:            Thu Aug 29 13:15:55 2019
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.2.6
  GitCommit:        894b81a4b802e4eb2a91d1ce216b8817763c29fb
 runc:
  Version:          1.0.0-rc8
  GitCommit:        425e105d5a03fabd737a126ad93d62a9eeede87f
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```

> If there was a problem, please notify an instructor!

Open your IDE (Visual Studio Code is installed on all lab machines) and start working
through the labs. Enjoy!

## References

* The original version of this workshop was created by [Sawood
  Alam](https://twitter.com/ibnesayeed) and is available on [Play With
  Docker](https://training.play-with-docker.com/microservice-orchestration/).

* [Original repo](https://github.com/ibnesayeed/linkextractor)
