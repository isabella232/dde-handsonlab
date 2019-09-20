# Link Extractor: Step 2

In this section you'll make your simple module into a functional API and
optimize the Dockerfile.

## Setup

## Changes from the previous step

The `linkextractor.py` has been updated as follows:

* Paths are normalized to full URLs
* Reporting both links and anchor texts
* Usable as a module in other scripts

You can see the code in your editor of choice, if interested.

## Try it out

```bash
docker build . -t linkextractor:v2
docker container run -it --rm linkextractor:v2 http://example.com/
docker container run -it --rm linkextractor:v2 http://odu.edu/
```

## Time to make it an API

1. `cp cheats/main.py .`
   * `main.py` uses Flask to create a live app with an API path and returns
     JSON instead of the raw text.
   * Our container image doesn't have Flask installed so we can't simply build
     it again and run - we need to modify the Dockerfile.

2. Your current Dockerfile has two `RUN` steps that execute `pip` commands.
This is OK but will get tedious to maintain if we keeping adding more Python
packages like flask to our program over time. Plus, Python coders would
typically expect to see a file called `requirements.txt` that has all the
required packages listed, so we should stick to normal Python usage so people
can figure out our code.
   * Create a file named `requirements.txt` with the following contents:

```text
beautifulsoup4
flask
requests
```

3. We need to update our Dockerfile now since we've got a `requirements.txt`
   file. Plus we have two Python modules and `main.py` is where we want our
   program to start. Overwrite the current contents of your `Dockerfile` with the
   following:

   ```Dockerfile
   FROM python:3.7-alpine
   LABEL maintainer="<your name>"

   WORKDIR /app
   COPY requirements.txt /app/
   RUN pip install -r requirements.txt

   COPY *.py /app/
   RUN chmod a+x *.py

   CMD ["python", "./main.py"]
   ```

## Build and run your new linkextractor API

```bash
docker build . -t linkextractor:v3
docker run --detach --publish 5000:5000 --name=linkextractor linkextractor:v3
```

You're using different `run` options this time because our API should keep
running in the background, instead of just running once and exiting as it did
before.

* The `--detach` option runs the container detached (in the background)
* `--publish` exposes a port so you can connect to the API. You're mapping port 5000
  on the outside to port 5000 on the inside.
* `--name=linkextractor` gives our running container a name. If you omit this
  the Docker Engine will make up a random name

Now send some commands to your API. You should be able open a browser and
connect to port 5000: `http://localhost:5000/`

If you do that it should return a usage note indicating you need to access the
API on the `/api/` endpoint: `Usage: http://<hostname>[:<prt>]/api/<url>`

Try `http://localhost:5000/api/http://docker.com` and you should see the API's
JSON output.

You can access the running container logs with the command `docker container
logs linkextractor`

## The inner code-build-test loop so far
So far, each time we update our code we have to rebuild the container and run
it again to see our changes. It's not terribly difficult and rebuilds happen
pretty fast due to Docker Engine caching, but with Docker Desktop we can do
some clever things to see our changes "live" as we save our code. We'll explore
this in the next step.

## Cleanup
Stop and remove the container app:

```bash
$ docker container rm -f linkextractor
```

And proceed to Step 3:
