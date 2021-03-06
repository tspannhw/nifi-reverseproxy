
# Apache NiFi ReverseProxy

This is an experiment project to improve Apache NiFi S2S so that it can work with Reverse Proxies such as Nginx or HA proxy in between S2S clients and remote NiFi cluster nodes.

Includes following resources:

- Different NiFi setups:
  - Standard Plain, Standard Secure, Cluster Plain, Cluster Secure
  - These NiFi instances are running directly on a localhost, using symbolic links to the local nifi SNAPSHOT buid directory to reduce disk usage
- Nginx docker files for various configuration evaluation
  - Configurations in this project assumes:
    - a docker host is running on a Virtual Box instance whose IP is 192.168.99.100
    - localhost has a network interface whose IP is 192.168.99.1 which can be accessed by the docker host and containers
- proxyclient: A java project to test S2S against different NiFi environment with or without a Reverse Proxy in the middle

## NiFi updates

This branch has improvements on NiFi side.
https://github.com/ijokarumawak/nifi/tree/nifi-4932

UPDATE: NIFI-4932 has been merged and available since NiFi 1.7.0.

## PORTS

In order to run different setup at the same time, following ports are used to avoid conflicts.

|Name|host|sp|ss|cp|cs|
|----|----|--|--|--|--|
|Nginx RAW|Docker Host|7080,7081|7444,7481|17090,17091,17092|17490,17491,17492|
|Nginx RAW Server name|Docker Host|-|-|17190,17191|17590,17591|
|Nginx HTTP|Docker Host|7070|7443|17080|17443|
|Nginx HTTPS Terminate|Docker Host|-|-|17453|-|
|nifi debug|localhost|8000|8001|8002,8003|8004,8005|
|nifi.web.http.port|localhost|8080|8443|18080,18081|18443,18444|
|nifi.remote.input.socket.port|localhost|8081|8481|18091,18092|18491,18492|
|nifi.cluster.node.protocol.port|localhost|N/A|N/A|18071,18072|18471,18472|
|Embed Zookeeper client port|localhost|N/A|N/A|2181|2182|
|Embed Zookeeper server|localhost|N/A|N/A|2888:3888|2889:3889|
|CacheServer|localhost|8010|8011|8012|8013|
|HandleHttpRequest|localhost|8020|8021|8022|8023|
|ListenHttp|localhost|8030|8031|8032,8033|8034,8035|

## How to use

### NiFi

- Go to one of the NiFi environment dirs: `$ cd nifi/cluster-secure/nifi0`
- Create symlinks to an installed NiFi resources. This project only provides predefined conf dirs, you need to install NiFi in different directory.
    - `$ ln -s <your-nifi-home>/lib`
    - `$ ln -s <your-nifi-home>/docs`
    - `$ cp -rp <your-nifi-home>/bin`
- Then, start NiFi: `$ bin/nifi.sh start`

### Nginx

You need to build a Nginx docker image you want to use:

- Go to one of the Nginx docker dirs: `$ cd nginx/cluster-secure-http`
- Build a docker image, the script uses current location to resolve name: `../../docker-build.sh`

Then, run a Nginx docker container:

```
$ ../../docker-run.sh
```


