Oracle Enterprise Edition 11g Release 2
============================

Oracle Enterprise Edition 11g Release 2 on Oracle Linux

This **Dockerfile** is based on [trusted build](https://registry.hub.docker.com/u/sath89/oracle-ee-11g/) of [Docker Registry](https://registry.hub.docker.com/). It has some Oracle components removed in an effort to shrink the size.

### Build
#### Build the minimal base
Download files from [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/112010-linx8664soft-100572.html). You'll need an Oracle login (free) and you'll need to accept the OTN License Agreement.
*	linux.x64_11gR2_database_1of2.zip
*	linux.x64_11gR2_database_2of2.zip

Start a web server to serve install files

    ruby -run -e httpd /tmp/orad -p 9090
Build the base image

    docker build --add-host od.com:10.10.3.211 -t docker.artifactory.granicuslabs.com/oracledev-ee-11g-granicus-base ./oracle-11g-ee-granicus-base/

#### Build layer that completes installation

   docker build -t docker.artifactory.granicuslabs.com/oracledev-ee-11g-granicus .

### Installation

Run with 1521 opened:

    docker run -d -p 1521:1521 oracle-11g-ee-granicus

Run with data on host and reuse it:

    docker run -d -p 1521:1521 -v /my/oracle/data:/u01/app/oracle oracle-11g-ee-granicus

Run with Custom DBCA_TOTAL_MEMORY (in Mb):

    docker run -d -p 1521:1521 -v /my/oracle/data:/u01/app/oracle -e DBCA_TOTAL_MEMORY=1024 oracle-11g-ee-granicus

Connect database with following setting:

    hostname: localhost
    port: 1521
    sid: EE
    service name: EE.oracle.docker
    username: system
    password: oracle

To connect using sqlplus:

<pre>
sqlplus system/oracle@//localhost:1521/EE.oracle.docker
</pre>

Password for SYS & SYSTEM:

    oracle

Apex install up to v 5.*

    docker run -it --rm --volumes-from ${DB_CONTAINER_NAME} --link ${DB_CONTAINER_NAME}:oracle-database -e PASS=YourSYSPASS sath89/apex install
Details could be found here: https://github.com/MaksymBilenko/docker-oracle-apex

Connect to Oracle Enterprise Management console with following settings:

    http://localhost:8080/em
    user: sys
    password: oracle
    connect as sysdba: true

By Default web management console is enabled. To disable add env variable:

    docker run -d -e WEB_CONSOLE=false -p 1521:1521 -v /my/oracle/data:/u01/app/oracle oracle-11g-ee-granicus
    #You can Enable/Disable it on any time

Start with additional init scripts or dumps:

    docker run -d -p 1521:1521 -v /my/oracle/data:/u01/app/oracle -v /my/oracle/init/SCRIPTSorSQL:docker-entrypoint-initdb.d oracle-11g-ee-granicus
By default Import from `docker-entrypoint-initdb.d` enabled only if you are initializing database(1st run). If you need to run import at any case - add `-e IMPORT_FROM_VOLUME=true`
**In case of using DMP imports dump file should be named like ${IMPORT_SCHEME_NAME}.dmp**
**User credentials for imports are  ${IMPORT_SCHEME_NAME}/${IMPORT_SCHEME_NAME}**

If you have an issue with database init like DBCA operation failed, please refer to this [issue](https://github.com/MaksymBilenko/docker-oracle-11g/issues/16)
