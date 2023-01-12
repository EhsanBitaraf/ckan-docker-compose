

# CKAN docker-compose Installation

I had a problem with [CKAN](https://ckan.org/) docker-compose file, I wrote another docker-compose that can create Docker files separately and then run them.

The problems I had with it are because of the strange network I am using. I would have preferred that the Docker images used in the docker-compose were already built.

![](https://ckan.org/static/img/logo.svg)
is an open-source DMS (data management system) for powering data hubs and data portals. CKAN makes it easy to publish, share and use data.



# Tip

Docker build with --build-arg with multiple arguments from file

build-arg with multiple arguments from file with bash[.](https://ilhicas.com/2018/11/03/docker-build-with-build-arg-wit-multiple-arguments.html)

```
docker build -t custom:stuff $(for i in `cat build.args`; do out+="--build-arg $i " ; done; echo $out;out="") .
```


# Preparation

## Create `.env` File

in directory `{ckanproject}/ckan/contrib/docker`

```
sudo vim .env
```

sample `.env` file:
```
CKAN_SITE_ID=default
CKAN_SITE_URL=http://localhost:5000
CKAN_PORT=5000
CKAN_SMTP_SERVER=smtp.corporateict.domain:25
CKAN_SMTP_STARTTLS=True
CKAN_SMTP_USER=user
CKAN_SMTP_PASSWORD=pass
CKAN_SMTP_MAIL_FROM=ckan@localhost
POSTGRES_PASSWORD=ckan
POSTGRES_USER=ckan
POSTGRES_PORT=5432
DATASTORE_READONLY_PASSWORD=datastore
; DS_RO_PASS=${DATASTORE_READONLY_PASSWORD}
DS_RO_PASS=datastore
DATASTORE_READONLY_USER=datastore_ro
POSTGRES_HOST=db
```

## Link `.env` file to ARG

```
ln -s .env solr/build.args
```

```
ln -s .env postgresql/build.args
```

# Build CKAN Docker Image

goto `{ckanproject}`

change ckan Docker file with this docker [file](/ckan/Dockerfile)

this file change proxy in Vegas envirement :)

then link to `.env` file:
```
ln -s contrib/docker/.env build.args
```

build docker image:
```
sudo docker build --no-cache=true -t ckan/cckan:0.1 $(for i in `cat build.args`; do out+="--build-arg $i " ; done; echo $out;out="") . 
```


# Build Posgres Docker Image

goto `{ckanproject}/contrib/docker/postgresql`

Build image:
```
sudo docker build -t ckan/postgres:0.1 $(for i in `cat build.args`; do out+="--build-arg $i " ; done; echo $out;out="") . 
```

# Build Solr Docker Image

goto `{ckanproject}/contrib/docker/solr`

copy `schema.xml` file
```
cp ../../../ckan/config/solr/schema.xml schema.xml
```

change this tag:

```
<schema name="ckan-2.10" version="1.6">
```

to this:
```
<schema name="ckan-2.10" version="2.8">
```

Build image:
```
sudo docker build -t ckan/solr:0.1 $(for i in `cat build.args`; do out+="--build-arg $i " ; done; echo $out;out="") . 
```

# Run docker-compose

use this [file](/docker-compose.yml) for `docker-compose.yml`

```
sudo docker-compose up
```
