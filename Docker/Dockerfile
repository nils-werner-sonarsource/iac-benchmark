# This file defines how the Docker container for Liddle is built. This is
# noteworthy as this is what will pushed to Micros upon deployment.
#
# NB. Whilst many of these commands could be stacked in order to reduce the
# number of layers the Docker container has in the build process, this is
# intentionally avoided as Docker will use cached layers rather than rebuilding
# them if no changes to that layer are detected. This means a faster build
# time!

FROM node:8.4.0
FROM python:3

MAINTAINER Clancy Rye <crye@atlassian.com>

# Create a user called 'app' so we can drop root privileges ASAP.
RUN mkdir /src /app /static
RUN groupadd -r app && useradd -rm -g app app

# Copy across the source code for the application.
WORKDIR /app
COPY src/ /app/
COPY gunicorn_config.py run.sh /app/

## Install Python dependencies.

## NB. It is necessary to install these from a file as we are using internal Pip
## modules and we do not want to store the credentials required to download them
## from within the container.
#COPY _docker_build /src/_docker_build
#RUN pip install -f _docker_build -r requirements.txt

## Copy configuration and database dependencies.
COPY run.sh /app/
COPY package.json /app/
COPY requirements.txt /app/

# Change perms on our copied files to our unpriviledged user, 'app'.
RUN chown -R app:app /src /app /static

# NB. It is necessary to install these from a file as we are using internal Pip
# modules and we do not want to store the credentials required to download them
# from within the container.
COPY _docker_build /app/_docker_build
RUN pip install -f _docker_build -r requirements.txt

# Change perms on our copied files to our unpriviledged user, 'app'.
RUN chown -R app:app /src /app /static

# Drop root privileges and run Liddle's bootstrap script.
USER app
EXPOSE 3333
CMD ["sh", "/app/run.sh"]
