# eXo Platform Community Docker image <!-- omit in toc -->

## This image is deprecated and is no longer maintained

## Latest version is available under [Meeds-io/meeds-docker](https://github.com/Meeds-io/meeds-docker)



![Docker Stars](https://img.shields.io/docker/stars/exoplatform/exo-community.svg) ![Docker Pulls](https://img.shields.io/docker/pulls/exoplatform/exo-community.svg)

The eXo Platform Community edition Docker image support `HSQLDB` (for testing) and `MySQL` (for production).

| Image                             | JDK | eXo Platform          |
|-----------------------------------|-----|-----------------------|
| exoplatform/exo-community:develop | 8   | 5.3 Community edition |
| exoplatform/exo-community:latest  | 8   | 5.3 Community edition |
| exoplatform/exo-community:5.3     | 8   | 5.3 Community edition |
| exoplatform/exo-community:5.2     | 8   | 5.2 Community edition |
| exoplatform/exo-community:5.1     | 8   | 5.1 Community edition |
| exoplatform/exo-community:5.0     | 8   | 5.0 Community edition |
| exoplatform/exo-community:4.4     | 8   | 4.4 Community edition |
| exoplatform/exo-community:4.3     | 8   | 4.3 Community edition |
| exoplatform/exo-community:4.2     | 7   | 4.2 Community edition |
| exoplatform/exo-community:4.1     | 7   | 4.1 Community edition |

- [This image is deprecated and is no longer maintained](#this-image-is-deprecated-and-is-no-longer-maintained)
- [Latest version is available under Meeds-io/meeds-docker](#latest-version-is-available-under-meeds-iomeeds-docker)
- [Quick start](#quick-start)
- [Configuration options](#configuration-options)
  - [Add-ons](#add-ons)
  - [JVM](#jvm)
  - [Frontend proxy](#frontend-proxy)
  - [Tomcat](#tomcat)
    - [Data on disk](#data-on-disk)
  - [Database](#database)
    - [MySQL](#mysql)
  - [Mongodb](#mongodb)
  - [ElasticSearch](#elasticsearch)
  - [JOD Converter](#jod-converter)
  - [Mail](#mail)
  - [JMX](#jmx)
  - [Reward Wallet](#reward-wallet)
- [How-to](#how-to)
  - [configure eXo Platform behind a reverse-proxy](#configure-exo-platform-behind-a-reverse-proxy)
  - [use MySQL database](#use-mysql-database)
  - [see eXo Platform logs](#see-exo-platform-logs)
  - [install eXo Platform add-ons](#install-exo-platform-add-ons)
  - [list eXo Platform add-ons available](#list-exo-platform-add-ons-available)
  - [list eXo Platform add-ons installed](#list-exo-platform-add-ons-installed)
  - [customize some eXo Platform settings](#customize-some-exo-platform-settings)
- [Image build](#image-build)

## Quick start

The prerequisites are :

- Docker daemon version 12+ + internet access
- 4GB of available RAM + 1GB of disk

The most basic way to start eXo Platform Community edition for *evaluation* purpose is to execute

```bash
docker run -v exo_data:/srv/exo -p 8080:8080 exoplatform/exo-community
```

and then waiting the log line which say that the server is started

```log
2017-05-22 10:49:30,176 | INFO  | Server startup in 83613 ms [org.apache.catalina.startup.Catalina<main>]
```

When ready just go to <http://localhost:8080> and follow the instructions ;-)

## Configuration options

### Add-ons

Some add-ons are already installed in eXo image but you can install other one or remove some of the pre-installed one :

| VARIABLE               | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                               |
|------------------------|-----------|---------------|-------------------------------------------------------------------------------------------|
| EXO_ADDONS_LIST        | NO        | -             | commas separated list of add-ons to install (ex: exo-answers,exo-skype:1.0.x-SNAPSHOT)    |
| EXO_ADDONS_REMOVE_LIST | NO        | -             | commas separated list of add-ons to uninstall (ex: exo-chat,exo-es-embedded) (since: 5.0) |
| EXO_ADDONS_CATALOG_URL | NO        | -             | The url of a valid eXo Catalog                                                            |

### JVM

The standard eXo Platform environment variables can be used :

| VARIABLE                   | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                                      |
|----------------------------|-----------|---------------|--------------------------------------------------------------------------------------------------|
| EXO_JVM_SIZE_MIN           | NO        | `512m`        | specify the jvm minimum allocated memory size (-Xms parameter)                                   |
| EXO_JVM_SIZE_MAX           | NO        | `3g`          | specify the jvm maximum allocated memory size (-Xmx parameter)                                   |
| EXO_JVM_PERMSIZE_MAX       | NO        | `256m`        | (Java 7) specify the jvm maximum allocated memory to Permgen (-XX:MaxPermSize parameter)         |
| EXO_JVM_METASPACE_SIZE_MAX | NO        | `512m`        | (Java 8+) specify the jvm maximum allocated memory to MetaSpace (-XX:MaxMetaspaceSize parameter) |
| EXO_JVM_USER_LANGUAGE      | NO        | `en`          | specify the jvm locale for langage (-Duser.language parameter)                                   |
| EXO_JVM_USER_REGION        | NO        | `US`          | specify the jvm local for region (-Duser.region parameter)                                       |
| EXO_JVM_LOG_GC_ENABLED     | NO        | `false`       | activate the JVM GC log file generation (location: $EXO_LOG_DIR/platform-gc.log) (5.1.0-RC12+)   |

INFO: This list is not exhaustive (see eXo Platform documentation or {EXO_HOME}/bin/setenv.sh for more parameters)

### Frontend proxy

The following environment variables must be passed to the container to configure Tomcat proxy settings:

| VARIABLE        | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                                                                                |
|-----------------|-----------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| EXO_PROXY_VHOST | NO        | `localhost`   | specify the virtual host name to reach eXo Platform                                                                                        |
| EXO_PROXY_PORT  | NO        | -             | which port to use on the proxy server ? if empty it will automatically defined regarding EXO_PROXY_SSL value (true => 443 / false => 8080) |
| EXO_PROXY_SSL   | NO        | `false`       | is ssl activated on the proxy server ? (true / false)                                                                                      |

### Tomcat

The following environment variables can be passed to the container to configure Tomcat settings

| VARIABLE               | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                  |
|------------------------|-----------|---------------|------------------------------------------------------------------------------|
| EXO_HTTP_THREAD_MAX    | NO        | `200`         | maximum number of threads in the tomcat http connector                       |
| EXO_HTTP_THREAD_MIN    | NO        | `10`          | minimum number of threads ready in the tomcat http connector                 |
| EXO_ACCESS_LOG_ENABLED | NO        | `false`       | activate Tomcat access log with combine format and a daily log file rotation |

#### Data on disk

The following environment variables must be passed to the container in order to work :

| VARIABLE                   | MANDATORY | DEFAULT VALUE                | DESCRIPTION                                                                                  |
|----------------------------|-----------|------------------------------|----------------------------------------------------------------------------------------------|
| EXO_DATA_DIR               | NO        | `/srv/exo`                   | the directory to store eXo Platform data                                                     |
| EXO_JCR_STORAGE_DIR        | NO        | `${EXO_DATA_DIR}/jcr/values` | the directory to store eXo Platform JCR values data                                          |
| EXO_FILE_STORAGE_DIR       | NO        | `${EXO_DATA_DIR}/files`      | the directory to store eXo Platform data                                                     |
| EXO_FILE_STORAGE_RETENTION | NO        | `30`                         | the number of days to keep deleted files on disk before definitively remove it from the disk |
| EXO_UPLOAD_MAX_FILE_SIZE   | NO        | `200`                        | maximum authorized size for file upload in MB.                                               |
| EXO_FILE_UMASK             | NO        | `0022`                       | the umask used for files generated by eXo                                                    |

### Database

The following environment variables must be passed to the container in order to work :

| VARIABLE                  | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                           |
|---------------------------|-----------|---------------|---------------------------------------------------------------------------------------|
| EXO_DB_TYPE               | NO        | `hsqldb`      | Community edition only support hsqldb and mysql                                       |
| EXO_DB_HOST               | NO        | `mysql`       | the host to connect to the database server                                            |
| EXO_DB_PORT               | NO        | `3306`        | the port to connect to the database server                                            |
| EXO_DB_NAME               | NO        | `exo`         | the name of the database / schema to use                                              |
| EXO_DB_USER               | NO        | `exo`         | the username to connect to the database                                               |
| EXO_DB_PASSWORD           | YES       | -             | the password to connect to the database                                               |
| EXO_DB_POOL_IDM_INIT_SIZE | NO        | `5`           | the init size of IDM datasource pool                                                  |
| EXO_DB_POOL_IDM_MAX_SIZE  | NO        | `20`          | the max size of IDM datasource pool                                                   |
| EXO_DB_POOL_JCR_INIT_SIZE | NO        | `5`           | the init size of JCR datasource pool                                                  |
| EXO_DB_POOL_JCR_MAX_SIZE  | NO        | `20`          | the max size of JCR datasource pool                                                   |
| EXO_DB_POOL_JPA_INIT_SIZE | NO        | `5`           | the init size of JPA datasource pool                                                  |
| EXO_DB_POOL_JPA_MAX_SIZE  | NO        | `20`          | the max size of JPA datasource pool                                                   |
| EXO_DB_TIMEOUT            | NO        | `60`          | the number of seconds to wait for database availability before cancelling eXo startup |

#### MySQL

| VARIABLE             | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                                       |
|----------------------|-----------|---------------|---------------------------------------------------------------------------------------------------|
| EXO_DB_MYSQL_USE_SSL | NO        | `false`       | connecting securely to MySQL using SSL (see MySQL Connector/J documentation for useSSL parameter) |

### Mongodb

The following environment variables should be passed to the container in order to work if you installed eXo Chat add-on :

| VARIABLE           | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                                        |
|--------------------|-----------|---------------|----------------------------------------------------------------------------------------------------|
| EXO_MONGO_HOST     | NO        | `mongo`       | the hostname to connect to the mongodb database for eXo Chat                                       |
| EXO_MONGO_PORT     | NO        | `27017`       | the port to connect to the mongodb server                                                          |
| EXO_MONGO_USERNAME | NO        | -             | the username to use to connect to the mongodb database (no authentification configured by default) |
| EXO_MONGO_PASSWORD | NO        | -             | the password to use to connect to the mongodb database (no authentification configured by default) |
| EXO_MONGO_DB_NAME  | NO        | `chat`        | the mongodb database name to use for eXo Chat                                                      |
| EXO_MONGO_TIMEOUT  | NO        | `60`          | the number of seconds to wait for mongodb availability before cancelling eXo startup               |

INFO: you must configure and start an external MongoDB server by yourself

### ElasticSearch

The following environment variables should be passed to the container in order to configure the search feature on an external Elastic Search server:

| VARIABLE                | MANDATORY | DEFAULT VALUE  | DESCRIPTION                                                                                                                                                                                                                                                                    |
|-------------------------|-----------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| EXO_ES_EMBEDDED         | NO        | `true`         | do we use an elasticsearch server embedded in the eXo Platform JVM or do we use an external one ? (using an embedded elasticsearch server is not recommanded for production purpose) (if set to `false` the add-on `exo-es-embedded` is uninstalled during container creation) |
| EXO_ES_EMBEDDED_DATA    | NO        | `/srv/exo/es/` | The directory to use for storing elasticsearch data (in embedded mode only).                                                                                                                                                                                                   |
| EXO_ES_SCHEME           | NO        | `http`         | the elasticsearch server scheme to use from the eXo Platform server jvm perspective (http / https).                                                                                                                                                                            |
| EXO_ES_HOST             | NO        | `localhost`    | the elasticsearch server hostname to use from the eXo Platform server jvm perspective.                                                                                                                                                                                         |
| EXO_ES_PORT             | NO        | `9200`         | the elasticsearch server port to use from the eXo Platform server jvm perspective.                                                                                                                                                                                             |
| EXO_ES_USERNAME         | NO        | -              | the username to connect to the elasticsearch server (if authentication is activated on the external elasticsearch).                                                                                                                                                            |
| EXO_ES_PASSWORD         | NO        | -              | the password to connect to the elasticsearch server (if authentication is activated on the external elasticsearch).                                                                                                                                                            |
| EXO_ES_INDEX_REPLICA_NB | NO        | `0`            | the number of replicat for elasticsearch indexes (leave 0 if you don't have an elasticsearch cluster).                                                                                                                                                                         |
| EXO_ES_INDEX_SHARD_NB   | NO        | `0`            | the number of shard for elasticsearch indexes.                                                                                                                                                                                                                                 |
| EXO_ES_TIMEOUT          | NO        | `60`           | the number of seconds to wait for elasticsearch availability before cancelling eXo startup (only if EXO_ES_EMBEDDED=false)                                                                                                                                                     |

INFO: the default embedded ElasticSearch in not recommended for production purpose.

### JOD Converter

The following environment variables should be passed to the container in order to configure jodconverter :

| VARIABLE               | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                               |
|------------------------|-----------|---------------|-------------------------------------------------------------------------------------------|
| EXO_JODCONVERTER_PORTS | NO        | `2002`        | comma separated list of ports to allocate to JOD Converter processes (ex: 2002,2003,2004) |

### Mail

The following environment variables should be passed to the container in order to configure the mail server configuration to use :

| VARIABLE               | MANDATORY | DEFAULT VALUE             | DESCRIPTION                                         |
|------------------------|-----------|---------------------------|-----------------------------------------------------|
| EXO_MAIL_FROM          | NO        | `noreply@exoplatform.com` | "from" field of emails sent by eXo platform         |
| EXO_MAIL_SMTP_HOST     | NO        | `localhost`               | SMTP Server hostname                                |
| EXO_MAIL_SMTP_PORT     | NO        | `25`                      | SMTP Server port                                    |
| EXO_MAIL_SMTP_STARTTLS | NO        | `false`                   | true to enable the secure (TLS) SMTP. See RFC 3207. |
| EXO_MAIL_SMTP_USERNAME | NO        | -                         | authentication username for smtp server (if needed) |
| EXO_MAIL_SMTP_PASSWORD | NO        | -                         | authentication password for smtp server (if needed) |

### JMX

The following environment variables should be passed to the container in order to configure JMX :

| VARIABLE                    | MANDATORY | DEFAULT VALUE | DESCRIPTION                                                                                                                               |
|-----------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| EXO_JMX_ENABLED             | NO        | `true`        | activate JMX listener                                                                                                                     |
| EXO_JMX_RMI_REGISTRY_PORT   | NO        | `10001`       | JMX RMI Registry port                                                                                                                     |
| EXO_JMX_RMI_SERVER_PORT     | NO        | `10002`       | JMX RMI Server port                                                                                                                       |
| EXO_JMX_RMI_SERVER_HOSTNAME | NO        | `localhost`   | JMX RMI Server hostname                                                                                                                   |
| EXO_JMX_USERNAME            | NO        | -             | a username for JMX connection (if no username is provided, the JMX access is unprotected)                                                 |
| EXO_JMX_PASSWORD            | NO        | -             | a password for JMX connection (if no password is specified a random one will be generated and stored in /opt/exo/conf/jmxremote.password) |

With the default parameters you can connect to JMX with `service:jmx:rmi://localhost:10002/jndi/rmi://localhost:10001/jmxrmi` without authentication.

### Reward Wallet

The following environment variables should be passed to the container in order to configure eXo Rewards wallet:

| VARIABLE                                      | MANDATORY | DEFAULT VALUE                                                    | DESCRIPTION                                                                                                                                                                                                                       |
|-----------------------------------------------|-----------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| EXO_REWARDS_WALLET_ADMIN_KEY                  | YES       | `changeThisKey`                                                  | password used to encrypt the Admin wallet’s private key stored in database. If its value is modified after server startup, the private key of admin wallet won’t be decrypted anymore, preventing all administrative operations |
| EXO_REWARDS_WALLET_ACCESS_PERMISSION          | NO        | `/platform/users`                                                | to restrict access to wallet application to a group of users (ex: member:/spaces/internal_space)                                                                                                                                  |
| EXO_REWARDS_WALLET_NETWORK_ID                 | NO        | `1` (mainnet)                                                    | ID of the Ethereum network to use (see: <https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md#list-of-chain-ids>)                                                                                                         |
| EXO_REWARDS_WALLET_NETWORK_ENDPOINT_HTTP      | NO        | `https://mainnet.infura.io/v3/a1ac85aea9ce4be88e9e87dad7c01d40`  | https url to access to the Ethereum API for the chosen network id                                                                                                                                                                 |
| EXO_REWARDS_WALLET_NETWORK_ENDPOINT_WEBSOCKET | NO        | `wss://mainnet.infura.io/ws/v3/a1ac85aea9ce4be88e9e87dad7c01d40` | wss url to access to the Ethereum API for the chosen network id                                                                                                                                                                   |
| EXO_REWARDS_WALLET_TOKEN_ADDRESS              | NO        | `0xc76987d43b77c45d51653b6eb110b9174acce8fb`                     | address of the contract for the official rewarding token promoted by eXo                                                                                                                                                          |                                                                                                  |

## How-to

### configure eXo Platform behind a reverse-proxy

You have to specify the following environment variables to configure eXo Platform (see upper section for more parameters and details) :

```bash
docker run -d \
  -p 8080:8080 \
  -e EXO_PROXY_VHOST="my.public-facing-hostname.org" \
  exoplatform/exo-community
```

You can also use Docker Compose (see the provided `docker-compose.yml` file as an example).

### use MySQL database

You have to specify the following environment variables to point to an external MySQL database server (see upper section for more parameters and details) :

```bash
docker run -d \
  -p 8080:8080 \
  -e EXO_DB_TYPE="mysql" \
  -e EXO_DB_HOST="mysql.server-hostname.org" \
  -e EXO_DB_USER="exo" \
  -e EXO_DB_PASSWORD="my-secret-pw" \
  exoplatform/exo-community
```

You can also use Docker Compose (see the provided `docker-compose.yml` file as an example).

### see eXo Platform logs

```bash
docker logs --follow <CONTAINER_NAME>
```

### install eXo Platform add-ons

To install add-ons in the container, provide a commas separated list of add-ons you want to install in a `EXO_ADDONS_LIST` environment variable to the container:

```bash
docker run -d \
  -p 8080:8080 \
  -e EXO_ADDONS_LIST="exo-tasks:1.3.x-SNAPSHOT,exo-answers:1.3.x-SNAPSHOT" \
  exoplatform/exo-community
```

INFO: the provided add-ons list will be installed in the container during the container creation.

### list eXo Platform add-ons available

In a *running container* execute the following command:

```bash
docker exec <CONTAINER_NAME> /opt/exo/addon list
```

### list eXo Platform add-ons installed

In a *running container* execute the following command:

```bash
docker exec <CONTAINER_NAME> /opt/exo/addon list --installed
```

### customize some eXo Platform settings

All previously mentioned [environment variables](#configuration-options) can be defined in a standard Docker way with `-e ENV_VARIABLE="value"` parameters :

```bash
docker run -d \
  -p 8080:8080 \
  -e EXO_JVM_SIZE_MAX="8g" \
  exoplatform/exo-community
```

Some [eXo configuration properties](https://docs.exoplatform.org/PLF50/PLFAdminGuide.Configuration.Properties_reference.html) can also be defined in an `exo.properties` file (starting from exoplatform/exo-community:5.1 version). In this case, just create this file and bind mount it in the Docker container :

```bash
docker run -d \
  -p 8080:8080 \
  -v /absolute/path/to/exo.properties:/etc/exo/exo.properties:ro \
  exoplatform/exo-community
```

## Image build

The simplest way to build this image is to use default values :

    docker build -t exoplatform/exo-community .

This will produce an image with the current eXo Platform Community edition.

The build can be customized with the following arguments :

| ARGUMENT NAME | MANDATORY | DEFAULT VALUE                                          | DESCRIPTION                                                                         |
|---------------|-----------|--------------------------------------------------------|-------------------------------------------------------------------------------------|
| ADDONS        | NO        | `exo-chat exo-tasks:1.1.0 exo-jdbc-driver-mysql:1.1.0` | a space separated list of add-ons to install (default: exo-jdbc-driver-mysql:1.1.0) |
