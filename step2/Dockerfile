FROM       python:3.7-alpine
LABEL      maintainer="Jim Armstrong <@jdarmstro>"

RUN        pip install beautifulsoup4
RUN        pip install requests

WORKDIR    /app
COPY       linkextractor.py /app/
RUN        chmod a+x linkextractor.py

ENTRYPOINT ["python", "./linkextractor.py"]
