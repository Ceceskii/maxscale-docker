# DATABASE SHARDING

Sharding is a technique for splitting a large data set into smaller pieces that can be kept in separate databases on different computers. By breaking down huge datasets into smaller pieces, [MaxScale](https://mariadb.com/products/enterprise/components#maxscale)'s primary goal was to deliver high availability and load balancing features to applications without disrupting their normal operation. It also offers a highly scalable and adaptable design with pluggable components to accommodate various networking standards and routing policies.

By breaking down huge datasets into smaller pieces, the storage requirements can be spread among more data nodes.A sharded SQL database containing zipcodes will be created in this project using docker-compose containers running MaxScale. The [Python script](https://github.com/Ceceskii/maxscale-docker/blob/master/maxscale/main.py) will demonstate how to query a sharded database as if it were a single database, which will enhance performance and reliability as the data volume increases.

## Prerequisites
To proceed further, you must have the following installed:
* [Docker](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04) on Ubuntu 22.04 Jelly Fish version.
* Install MySQL Connector
```
sudo apt install python3-pip
pip3 install mysql-connector
```
* Install Docker Compose
```
sudo apt install docker-compose
```
* Run UPDATE on your VM.
```
sudo apt update
sudo apt upgrade -y
```
* Install [MariaDB](https://www.digitalocean.com/community/tutorials/how-to-install-mariadb-on-ubuntu-22-04)on Ubuntu 22.04 Jelly Fish version.
* Ensure that Maxscale Container has been created.
```
git clone https://github.com/zohan/maxscale-docker/
cd maxscale-docker/maxscale
docker-compose up –d
```

## Configuration & Setting Up

### Python Set up

1. Identify the IP address of your Maxscale container.
```
docker inspect maxscale_maxscale_1
```
2. Edit your [Python Script](https://github.com/Ceceskii/maxscale-docker/blob/master/maxscale/main.py)
```
nano main.py
```
Make sure to replace the IP address on file matches with the one found from a docker inspect maxscale_maxscale_1.

### Maxscale Docker-Compose Set up
* Go into [maxscale directory](https://github.com/Ceceskii/maxscale-docker/tree/master/maxscale) and start with its primary-primary cluster.
```
docker-compose up -d
```
* To stop the containers, execute the following command. Optionally, use the -v
flag to also remove the volumes.

* To run maxctrl in the container to see the status of the cluster:
```
$ docker-compose exec maxscale maxctrl list servers
┌─────────┬──────────┬──────┬─────────────┬─────────────────┬──────────┬─────────────────┐
│ Server  │ Address  │ Port │ Connections │ State           │ GTID     │ Monitor         │
├─────────┼──────────┼──────┼─────────────┼─────────────────┼──────────┼─────────────────┤
│ server1 │ primary1 │ 3306 │ 0           │ Master, Running │ 0-3000-5 │ MariaDB-Monitor │
├─────────┼──────────┼──────┼─────────────┼─────────────────┼──────────┼─────────────────┤
│ server2 │ primary2 │ 3306 │ 0           │ Running         │ 0-3001-5 │ MariaDB-Monitor │
└─────────┴──────────┴──────┴─────────────┴─────────────────┴──────────┴─────────────────┘
```

### Connect to MariaDB
* Connect to MariaDB to verify that the database is up and running.
```
$ mariadb -umaxuser -pmaxpwd -h 127.0.0.1 -P 4000

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 10.11.3-MariaDB-1:10.11.3+maria~ubuntu-log mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [test]>
```

## Running the Script

```
python3 main.py
```
Expected Output from the script:
1. The largest zipcode in zipcodes_one.
2. All zipcodes where state=KY (Kentucky).
3. All zipcodes between 40000 and 41000.
4. The TotalWages column where state=PA.

## Special Thanks!
Thank you Dre Owens and Josh Brown for bouncing off tips and tricks for this project!
