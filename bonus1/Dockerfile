FROM python:3.7-alpine
LABEL maintainer="<your name>"

WORKDIR /app
COPY requirements.txt /app/
RUN pip install -r requirements.txt

COPY *.py /app/
RUN chmod a+x *.py

CMD ["python", "./main.py"]
