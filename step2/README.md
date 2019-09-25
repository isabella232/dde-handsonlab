# Link Extractor: Step 2

In this section you'll make your simple module into a functional API and optimize the Dockerfile.

## Setup

## Changes from the previous step

The `linkextractor.py` script has been updated as follows:

* Paths are normalized to full URLs
* Reporting both links and anchor texts
* Usable as a module in other scripts

You can see the code in your editor of choice, if interested.

## Try it out

```bash
  > docker build . -t linkextractor:v2
  > docker container run -it --rm linkextractor:v2 http://docker.com/
```
* --rm removes the container after exiting it.

The only difference between v1 and v2 of the ‘linkextractor’ app at this point, is that we now have full URLs in the output once the app is executed as seen below.

```bash
  > docker container run -it --rm linkextractor:v2 http://docker.com/
  [[IMG]](http://docker.com/)
  [Why Docker?](http://docker.com/why-docker)
  [What is a Container?](http://docker.com/resources/what-container)
  [Company](http://docker.com/company)
  [Partners](http://docker.com/partners)
  [Products](http://docker.com/products)
```

The main changes in the `linkextractor.py` are for this next section :D

## Time to make it an API

You'll notice there is a now a second python file in this directory, `main.py`.

   * `main.py` uses Flask to create a live app with an API path and returns JSON instead of the raw text.
   * Our container image doesn't have Flask installed so we can't simply build it again and run it - we need to modify the Dockerfile.

Before we do that, we'll do a little hygiene on the Dockerfile. The current Dockerfile has two `RUN` steps that execute `pip` commands and install some Python libraries. 

This is OK but the Dockerfile will get tedious to maintain if we keep adding more Python packages like Flask to our program over time. Plus, Python coders would typically expect to see a file called `requirements.txt` that has all the required packages listed, so we should stick to normal Python code usage so people can figure out our code.

Create a file named `requirements.txt` with the following contents:

```text
  beautifulsoup4
  flask
  requests
```

We need to update our Dockerfile as we now have a `requirements.txt` file that we need to include as part of the build. Plus we also have two Python modules and `main.py`, where we want our program to start so we need to provide a new default command. Overwrite the current contents of your `Dockerfile` with the following:

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
  > docker build . -t linkextractor:v3
  > docker run --detach --publish 5000:5000 --name=linkextractor linkextractor:v3
```

You're using different `docker run` options this time because our API should keep running in the background, instead of just running once and exiting, as it did before.

* The `--detach` option runs the container detached (in the background)
* `--publish` exposes a port so you can connect to the API. You're mapping port 5000 on the outside of the container to port 5000 on the inside.
* `--name=linkextractor` gives our running container a name. If you omit this the Docker Engine will make up a random name

Now send some commands to your API. You should be able open a browser and connect to port 5000: `http://localhost:5000/`

If you do that it should return a usage note indicating you need to access the API on the `/api/` endpoint:

  `Usage: http://<hostname>[:<prt>]/api/<url>`

Try `http://localhost:5000/api/https://docker.com` in your web browswer and you should see the API's output, now enhanced to use JSON format.

You can access the logs of the running container with the following command

```bash
  > docker container logs linkextractor
  ```

## The inner code-build-test loop so far
So far, each time we update our code we have to rebuild the container and run it again to see our changes. It's not terribly difficult and rebuilds happen pretty fast because the Docker Engine caches the layers it has already built, but with Docker Desktop we can do
some clever things to see our changes "live" as we save our code. We'll explore this in the next step.

## Clean-up
Stop and remove the container app:

```bash
  > docker container rm -f linkextractor
```

## Proceed to Step 3
```bash
  > cd ../step3
  ```
