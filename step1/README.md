# Link Extractor: Step 1

A basic page scraping script that prints out all the hyperlink references of a given web page.

## Try it out

```bash
cd step1
./linkextractor.py http://example.com/
```

## Possible difficulties

The Python script may or may not run, depending on how your machine is setup. If it didn't run, here are some potential reasons:

* Is the script executable? (`chmod a+x linkextractor.py`)
* Is `Python` installed on the machine?
* Can you install software on the machine?
* Is `pip` installed?
* The script uses the `requests` and `beautifulsoup4` Python libraries. Are those libraries installed on your machine?

These types of complications are one of the drivers for using containers in development. You can package up all of those requirements in a portable format - a container image - that you can use on any machine and it will work, as long as you have the Docker Engine. In this lab and on development workstations running Windows or macOS, the Docker Engine is provided by Docker Desktop, but developers also use the Docker Engine on a wide range of Linux distributions.

## Containerize it!

You need to instruct the Docker Engine to build the container image you want and that's done with a Dockerfile. There are two options for creating the Dockerfile below:

**Option 1:** Create a Dockerfile
Create a file named `Dockerfile` and type or copy the following contents in to it:

```dockerfile
FROM       python:3.7-alpine
LABEL      maintainer="Your name <email@domain.co.uk>"

RUN        pip install beautifulsoup4
RUN        pip install requests

WORKDIR    /app
COPY       linkextractor.py /app/
RUN        chmod a+x linkextractor.py

ENTRYPOINT ["python", "./linkextractor.py"]
```

**Option 2:** Copy the cheats file
`cp cheats/Dockerfile .`

## Build the image
Build the docker image with a name:tag of linkextractor:v1

```bash
docker build . -t linkextractor:v1
```

## Run the container
Run the container from the image, passing any URL as the parameter.  In the example below we have used https://docker.com as the URL.

```bash
docker run linkextractor:v1 https://docker.com
```

## Run through your inner loop

To demonstrate how you might code and test locally we'll make a minor modification to the Python code (it's a small change -- you don't need to know Python).

1. Open `linkextractor.py` in VS Code or in your favourite code editor
2. The last line of the current code looks like this:

```python
    print(link.get("href"))
```

3. Change that line to look like this:

```python
    print("-->", link.get("href"))
```

4. Build and run the containers again. Notice the `v1.1` tag. This helps tell the images apart and prevents overwriting the first image you created.

```bash
docker build . -t linkextractor:v1.1
docker run linkextractor:v1.1 https://docker.com
```

Notice you now see `--->` before every link! Your app is coming right along.

```
$ docker run linkextractor:v1.1 https://docker.com
--> /
--> /why-docker
--> /resources/what-container
--> /company
--> /partners
```

So far you've seen a very basic method for getting your code into a container and running it. With containerization, all the dependencies your code need are isolated in the container itself, instead of installing all that software on your own machine.

You can run that container on any machine where the Docker Engine resides and it will work the same way. The Dockerfile is the code for creating the container image. It can be stored with the source code so that if the container image doesn't exist locally, the Docker Engine can build it for you. Later, we'll see how to push the image to a repository so that you can share it with others.

## Move ahead to Step 2
