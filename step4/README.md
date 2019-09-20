# Link Extractor: Step 4

A web application to extract links and anchor texts from a given web page and
analyze link statistics.

## Changes from the previous step

* Another `Dockerfile` is added for the PHP web application to avoid live file
  mounting
* A Redis container is added for caching using the official Redis Docker image
* The API service talks to the Redis service to avoid downloading and parsing
  pages that were already scraped before

* The API service written in Python is replaced with a similar Ruby implementation
* The `API_ENDPOINT` is updated to point to the new Ruby API service
* Newly added link extraction log is persisted using volumes

## Try it out

```
$ docker-compose up --build
```
