# Cyverse_Setup
A repository for documentation about how the Cyverse VM was set up.

## Installed programs

The VM runs CentOS7, but comes as a clean install.  A number of programs need to be installed.  The first set of commands will show got to install the programs needed to set up the current configuration:

```bash
sudo yum update
sudo yum upgrade
sudo yum install R git nano libcurl-devel -y
```
The package `libcurl-devel` is used in the `httr` and `curl` R packages that we will install later.

### Docker

Docker required some additional configuration:

```bash
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce -y
``` 

To install `docker-compose` for some of the work we use:

```
sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

This allows us to use `docker-compose` files in other repositories to facilitate some of our development workflows.


### neo4j

To install `neo4j`, the graph database used in this project:

```
sudo su
rpm --import http://debian.neo4j.org/neotechnology.gpg.key
cat <<EOF>  /etc/yum.repos.d/neo4j.repo
[neo4j]
name=Neo4j RPM Repository
baseurl=http://yum.neo4j.org/stable
enabled=1
gpgcheck=1
EOF
```

Then install as normal:

```bash
sudo yum install neo4j-3.3.0
```

At this point everything is installed.

## Setting up the `neo4j` Service

Following installation:

The `neo4j` Documentation provides a guide to [Linux file Locations](https://neo4j.com/docs/operations-manual/current/configuration/file-locations/) for reference.

### Set the Admin Password

Before running `neo4j` for the first time you need to set the admin password (and then start the service):

```bash
sudo neo4j-admin set-initial-password <yourpasswordhere>
sudo service neo4j start
```
### Add APOC Plugins

The [APOC plugins](https://github.com/neo4j-contrib/neo4j-apoc-procedures) allow us to parse XML files for the NSF awards, to add them to the larger graph database.  They are installed by downloading the latest version of the `jar` package:

```
sudo curl -o /var/lib/neo4j/plugins/apoc-3.2.3.5-all.jar "https://github.com/neo4j-contrib/neo4j-apoc-procedures/releases/download/3.2.3.5/apoc-3.2.3.5-all.jar"
```

### Setting up Remote Access

[Documentation](https://neo4j.com/developer/kb/how-do-i-enable-remote-https-access-with-neo4j-30x/) how to set up remote access for the `neo4j` server is provided. 
 
 > it etc/neo4j/conf/neo4j.conf to change dbms.connector.https.address=0.0.0.0:7473
 
 Then access the server using the Cyverse host's IP address (using `hostname -I`).  Note that a firewall exception must be made.
 
