FROM python:3.6
LABEL maintainer="hadoe-api"

RUN apt-get update && apt-get install -y libpq5

RUN pip3 install --upgrade pip
RUN pip3 install --upgrade virtualenv

RUN virtualenv -p python3 /env

ENV VIRTUAL_ENV /env
ENV PATH /env/bin:$PATH

RUN mkdir -p /usr/credit_api/app
WORKDIR /usr/credit_api/app

ADD requirements.txt /usr/credit_api/app/requirements.txt
RUN pip3 install --no-cache-dir -r /usr/credit_api/app/requirements.txt

ADD . /usr/credit_api/app

#EXPOSE 8080
#CMD export DJANGO_PASSWORD=$(cat /etc/secrets/djangouserpw); export NODB=1; gunicorn -b :8080 credit_api.wsgi:application

#ENV NODB=1
CMD gunicorn -b :8080 credit_api.wsgi
