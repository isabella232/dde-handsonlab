# Lab-01 Link Extractor

Link Extractor app scrapes links from a given web page.  This repository
illustrates step by step approach to learn [Docker](https://www.docker.com/).

This version of the workshop is designed to be used with [Docker Desktop
Community](https://www.docker.com/products/docker-desktop) but should work just
about anywhere you can install or run a [Docker
Engine](https://www.docker.com/products/container-runtime), including [Play
With Docker](https://labs.play-with-docker.com).

It starts from running a very basic Python script and gradually leads to a
multi-service container orchestration (AKA micro-service architecture).

**No Python skills required** This workshop uses a Python example but no Python
expertise is assumed or required. Step 6 switches to Ruby, but again, no Ruby
knowledge is required.

## How to Use the Repository

There are multiple folders in this repo which correspond to steps in the
workshop. These folders also correspond to git branches to make it easier to
step through the workshop without getting lost in the folder structure.

Verify the Docker Desktop Enterprise is working on your machine. Open Windows
Terminal and run:

```bash
PS C:\Users\oppom> docker version

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
PS C:\Users\oppom>
```

> If there was a problem, please notify an instructor!

2. Navigate to Step 1 to start working through the lab. Enjoy!

## References

* The original version of this workshop was created by [Sawood
  Alam](https://twitter.com/ibnesayeed) and is available on [Play With
Docker](https://training.play-with-docker.com/microservice-orchestration/).
   * [Original repo](https://github.com/ibnesayeed/linkextractor)
