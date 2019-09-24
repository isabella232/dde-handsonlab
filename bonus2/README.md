# Avoid using root

One best practice you will hear is to avoid running your applications as root. In the world of containers, the fear is that if someone compromises your container and your application is running as root, then if they can get access to the host system (break out of the container) they will also have root access.

## Fire up the Link Extractor

We'll take a look at Link Extractor again to see this in action:

```bash
> docker build -t linkextractor:root .
> docker run --detach --publish 5000:5000 --name=l
inkextractor linkextractor:root

# now we can see the user linkextractor uses:
> docker top linkextractor
PID                 USER                TIME                COMMAND
89732               root                0:00                python ./main.py
```

Link Extractor is using `root`! Ack!

## I am not (g)root

It's fairly easy to specify different users and groups to use in a container and you can do it all in the Dockerfile. Open Dockerfile-noroot:

```Dockerfile
FROM python:3.7-alpine
LABEL maintainer="<your name>"

RUN addgroup -g 10000 -S linkextractor && \
    adduser  -u 10000 -S linkextractor -G linkextractor -H -s /bin/false && \
    apk --no-cache add su-exec

WORKDIR /app
COPY --chown=linkextractor:linkextractor requirements.txt /app/ 
RUN pip install -r requirements.txt && chown linkextractor:linkextractor /app

COPY --chown=linkextractor:linkextractor *.py /app/
RUN chmod a+x *.py

CMD ["su-exec", "linkextractor", "python", "./main.py"]
```

Notice the changes:
* There's a new RUN command that adds a `linkextractor` user and group and installs the `su-exec` package, which we'll use to run as non-root
* The COPY commands now have a `--chown` option added, which instructs Docker Engine to change ownership of files as they're copied in to the container
* The CMD changed to now use `su-exec` and run Link Extractor using the `linkextractor` user.

## See it in action

Clean up any running containers, build using the new Dockerfile-noroot, and run Link Extractor again:

```bash
> docker container stop linkextractor && docker container rm linkextractor

> docker build -t linkextractor:root -f Dockerfile-noroot .

> docker run --detach --publish 5000:5000 --name=linkextractor linkextractor:root

> docker top linkextractor
PID                 USER                TIME                COMMAND
6374                10000               0:00                python ./main.py
```

This time we are using the user ID 10000, which from the Dockerfile-noroot is the ID we assigned to our linkextractor user.

We're safe and sound!

## More info on root & container safety

There are many, many techniques to protect yourself from root user privilege escalation issues with containers. Whether running on Linux or Windows, a container is basically just a process so you can isolate it in number of ways. Here's some extra reading, and a fun "Escape the Container" game you can try out (well, fun in a computer science fashion):
* [Docker Security](https://docs.docker.com/engine/security/security/)
* [Kubernetes Security](https://kubernetes.io/docs/concepts/security/overview/)
  * [Pod Security](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/#controlling-what-privileges-containers-run-with)
* [Game: Contained.af](https://contained.af/): _"A stupid game for learning about containers, capabilities, and syscalls."_ NOTE that this requires knowledge of Linux system commands.

## Clean up

```bash
> docker container stop linkextractor && docker container rm linkextractor
```