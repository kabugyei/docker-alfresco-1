# Alfresco Community Docker

Deployment template based in official [Docker Composition](https://github.com/Alfresco/acs-community-deployment/tree/master/docker-compose) provided by Alfresco.

You should review volumes, configuration, modules & tuning parameters before using this composition in **Production** environments.

## Source Images

* [alfresco 6.0.7-ga](https://github.com/Alfresco/acs-community-packaging/blob/master/docker-alfresco/Dockerfile)
* [share 6.0](https://github.com/Alfresco/share/blob/6.0/packaging/docker/Dockerfile)
* [postgres 10](https://github.com/docker-library/postgres/blob/master/10/Dockerfile)
* [alfresco-search-services 1.1.1.1](https://github.com/Alfresco/SearchServices/blob/master/packaging/src/docker/Dockerfile)
* [content-app latest](https://hub.docker.com/r/alfresco/alfresco-content-app/)

## Volumes

A directory named `volumes` is located in the root folder to store configuration, data and log files.

```bash
$ tree volumes
volumes
├── config
│   ├── alfresco-global.properties
│   ├── ext-share-config-custom.xml
│   └── nginx.conf
├── data
│   ├── alf-repo-data
│   ├── postgres-data
│   └── solr-data
└── logs
    ├── alfresco
    ├── nginx
    ├── postgres
    └── share
```

**Configuration** files are available at `config` folder:

* `alfresco-global.properties` for Repository
* `ext-share-config-custom.xml` for Share
* `nginx.conf` for HTTP Proxy

**Data** wil be persisted automatically in `data` folder. Once launched, Docker will create three subfolders for following services:

* `alf-repo-data` for Content Store
* `postgres-data` for Database
* `solr-data` for Indexes

>> For Linux hosts, set `solr-data` folder permissions to user with UID 1001, as `alfresco-search-services` is using an container user named `solr` with UID 1001.

**Logs** folder includes log files for:

* `alfresco` contains Tomcat repository logs
* `nginx` contains HTTP Proxy logs
* `postgres` contains database logs
* `share` contains Tomcat share logs

## Tuning options

Memory resources for the containers can be changed in `.env` file.

Sample values are set by default.

```
# Alfresco JVM Memory Settings
ALFRESCO_XMX=2g
ALFRESCO_XMS=2g

# Share JVM Memory Settings
SHARE_XMX=1g
SHARE_XMS=1g

# Solr 6 JVM Memory Settings
SOLR_XMX=1g
SOLR_XMS=1g

# Postgres Tuning Settings
# Default: pg_tune with 100 connections, 1GB RAM & 1 CPU
PG_MAX_CONNECTIONS=100
PG_SHARED_BUFFERS=256MB
PG_EFFECTIVE_CACHE_SIZE=768MB
PG_MAINTENANCE_WORK_MEM=64MB
PG_CHECKPOINT_COMPLETION_TARGET=0.7
PG_WAL_BUFFERS=7864kB
PG_DEFAULT_STATISTICS_TARGET=100
PG_RANDOM_PAGE_COST=4
PG_EFFECTIVE_IO_CONCURRENCY=2
PG_WORK_MEM=2621kB
PG_MIN_WAL_SIZE=1GB
PG_MAX_WAL_SIZE=2GB
PG_MAX_WORKER_PROCESSES=1
PG_MAX_PARALLEL_WORKERS_PER_GATHER=1
PG_MAX_PARALLEL_WORKERS=1
```

# How to use this composition

## Start Docker

Start docker and check the ports are correctly bound.

```bash
$ docker-compose up -d
$ docker ps
nginx:stable-alpine 						0.0.0.0:80->80/tcp
alfresco/alfresco-search-services:1.1.1.1	8983/tcp
docker-alfresco_share_1 					8080/tcp
alfresco/alfresco-content-app				80/tcp
docker-alfresco_alfresco 					8080/tcp
```

### Viewing System Logs

You can view the system logs by issuing the following.

```bash
$ docker-compose logs -f
```

Logs for every service are also available at `volumes/logs` folder.

## Access

Use the following username/password combination to login.

 - User: admin
 - Password: admin

Alfresco and related web applications can be accessed from the below URIs when the servers have started.

```
http://localhost               - Alfresco Content Application
http://localhost/share         - Alfresco Share WebApp
http://localhost/alfresco      - Alfresco Repository (REST)
http://localhost/solr          - Alfresco Search Services
```

## Further configuration

### Deploying additional Addons

You can copy additional Alfresco addons to following paths.

```
alfresco/modules/amps
alfresco/modules/jars
share/modules/amps_share
share/modules/jars
```

After you `rebuild` the image, they will be available within the Alfresco instance.

### Adding configuration to repository

You can set additional properties modifying default files available at `volumes/config` folder.