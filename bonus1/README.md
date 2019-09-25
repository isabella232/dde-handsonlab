# Development with Containers - Logging

As you integrate container-based development and start running your applications in Kubernetes or Swarm clusters you will, of course, need to gather logs.

One important note about containers as it pertains to logs: containers are ephemeral. That means everytime you launch a new instance of a container image, you're starting fresh. Anything that may have been written inside a previous container instance is gone, unless you take care to offload the data you want to keep, including logs.

## Standard log access
The Docker Engine has a built-in mechanism for gathering the STDERR and STDOUT streams. We'll use an earlier version of the Link Extractor app to see this.

```bash
# build the image
> docker build -t linkextractor:logs1 .

# start the container
> docker run --detach --publish 5000:5000 --name=linkextractor linkextractor:logs1

# access the linkextractor to generate some traffic - you should be 
# able to access it via browswer at http://localhost:5000/api/<URL>

# Once you've generated a little work, view the standard logs
> docker logs linkextractor
 * Serving Flask app "main" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
172.17.0.1 - - [24/Sep/2019 15:39:12] "GET / HTTP/1.1" 200 -
172.17.0.1 - - [24/Sep/2019 15:39:28] "GET /api/https://commondesk.com HTTP/1.1" 200 -
172.17.0.1 - - [24/Sep/2019 15:39:35] "GET /api/https://www.commondesk.com HTTP/1.1" 200 -
172.17.0.1 - - [24/Sep/2019 15:39:41] "GET /api/https://osu.edu HTTP/1.1" 200 -
```

This is exactly the same output you would see if you just ran the main.py script from the command line manually. Docker is gathering the STDOUT and STDERR streams and displaying that information to you.

This is well and good if you only use STDOUT and STDERR but it's quite common to generate your own custom logs as well.

Take a look at the Python code in this exercise - you can open either `main.py` or `linkextractor.py`. You'll see some lines like this:

```python
# create logger
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# create console handler and set level to debug
ch = logging.FileHandler('debug.log', mode='w')
ch.setLevel(logging.DEBUG)
```

This time around your app is creating debug logs in a file named `debug.log`. But as you saw from the `docker logs` output, none of these log messages show up because they aren't going to STDERR or STDOUT.

## Where are the logs?

We can take a look in the running container to view the debug output:

```bash
# exec in to the running container named "linkextractor" and run "/bin/sh"
> docker exec -it linkextractor /bin/sh

# you should now be inside the container - quick way to tell is to 
# run "ps" and see what process is PID 1. In a container it will be
# your CMD or ENTRYPOINT by default:
/app > ps -ef
PID   USER     TIME  COMMAND
    1 root      0:00 python ./main.py
   23 root      0:00 /bin/sh
   29 root      0:00 ps -ef

# look for the debug.log
/app > ls
__pycache__       debug.log         linkextractor.py  main.py           requirements.txt

# look at what's been logged so far
/app > cat debug.log
2019-09-24 16:06:34,529 - linkextractor - DEBUG - debug message
2019-09-24 16:06:34,529 - linkextractor - INFO - info message
2019-09-24 16:06:34,529 - linkextractor - WARNING - warn message
2019-09-24 16:06:34,529 - linkextractor - ERROR - error message
2019-09-24 16:06:34,529 - linkextractor - CRITICAL - critical message
2019-09-24 16:06:34,530 - __main__ - DEBUG - debug message
2019-09-24 16:06:34,530 - __main__ - INFO - info message
2019-09-24 16:06:34,530 - __main__ - WARNING - warn message
2019-09-24 16:06:34,530 - __main__ - ERROR - error message
2019-09-24 16:06:34,530 - __main__ - CRITICAL - critical message
2019-09-24 16:06:42,850 - linkextractor - INFO - starting extraction
2019-09-24 16:06:47,748 - linkextractor - INFO - starting extraction
```

## Making log files available
You've found the debug.log, but as mentioned earlier, if you stop the container and start it again, your previous logs will be gone and you'll have a new debug.log file. So how do you get the debug.log out to a place where you can capture the output and use it?

There a several approaches, but we'll setup our container so that `debug.log` is symbolically linked to our STDOUT so we can capture the logs with the `docker log` command.

Look at Dockerfile-log and notice the new RUN command that links `debug.log` to STDOUT:

```Dockerfile
FROM python:3.7-alpine
LABEL maintainer="<your name>"

WORKDIR /app
COPY requirements.txt /app/
RUN pip install -r requirements.txt

COPY *.py /app/
RUN chmod a+x *.py

RUN ln -sf /dev/stdout /app/debug.log

CMD ["python", "./main.py"]
```

Stop the current linkextractor, then build and run the container using this new Dockerfile:

```bash
# if you're still `exec`ed in to the container:
app > exit

# stop & cleanup the existing linkextractor. `stop` sends a SIGHUP 
# to ask the container to shut itself down. You can use 'kill' to 
# force misbehaving containers to stop running
> docker container stop linkextractor && docker container rm linkextractor
linkextractor
linkextractor

# build the new container
> docker build -t linkextractor:logs -f Dockerfile-log .

# run the new container
> docker run --detach --publish 5000:5000 --name=linkextractor linkextractor:logs

# generate some traffic to your app then check the logs again
> docker logs linkextractor
2019-09-24 16:58:48,100 - linkextractor - DEBUG - debug message
2019-09-24 16:58:48,101 - linkextractor - INFO - info message
2019-09-24 16:58:48,102 - linkextractor - WARNING - warn message
2019-09-24 16:58:48,103 - linkextractor - ERROR - error message
2019-09-24 16:58:48,103 - linkextractor - CRITICAL - critical message
2019-09-24 16:58:48,104 - __main__ - DEBUG - debug message
2019-09-24 16:58:48,104 - __main__ - INFO - info message
2019-09-24 16:58:48,105 - __main__ - WARNING - warn message
2019-09-24 16:58:48,109 - __main__ - ERROR - error message
2019-09-24 16:58:48,110 - __main__ - CRITICAL - critical message
```

And there they are - our debug.log messages are now being sent to STDOUT and so we can view them with the `docker logs` command.

This is a simple way to get logs from your containers and it works fine in development. For production you may want something a bit more robust and so there are a number of logging drivers you can use. That's beyond the scope of this exercise, but you can find more information:

* [Docker Engine logging drivers](https://docs.docker.com/config/containers/logging/configure/)
* [Kubernetes logging architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)


## Clean-up
```bash
> docker container stop linkextractor
```