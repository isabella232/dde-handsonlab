# Link Extractor: Step 1

A basic page scraping script that prints out all the hyper references of the
given web page.

## Try it out

```bash
./linkextractor.py http://example.com/
```

## Possible difficulties

* Is the script executable? (`chmod a+x linkextractor.py`)
* Is `Python` installed on the machine?
* Can you install software on the machine?
* Is `pip` installed?
* Are `requests` and `beautifulsoup4` Python libraries installed?

## Containerize it!

**Option 1:** Create a Dockerfile
Create a file named `Dockerfile` and type or copy the following contents in to it:

```dockerfile
FROM       python:3.7-alpine
LABEL      maintainer="Jim Armstrong <@jdarmstro>"

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

`docker build . -t linkextractor:v1`

## Run the container

`docker run linkextractor:v1 https://docker.com`

You can use any URL you want above.

## Run through your inner loop

To demonstrate how you might code and test locally we'll make a minor
modification to the Python code (it's a small change -- you don't need to know
Python).

1. Open `linkextractor.py` in your favorite code editor
2. The last line of the current code looks like this:

```python
    print(link.get("href"))
```

3. Change that line to look like this:

```python
    print("-->", link.get("href"))
```

4. Build and run the containers again. Notice the `v1.1` tag. This helps tell
   the images apart and prevents overwriting the first image you created.

```bash
docker build . -t linkextractor:v1.1
docker run linkextractor:v1.1 https://docker.com
```

## Move ahead to Step 2

```
$ cd ../step2
```
