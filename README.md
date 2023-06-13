# CNE370 DATABASE SHARDING

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
Output from the script:
1. Retrieve the last 10 rows of data from the zipcodes_one shard.
```
(705, 'STANDARD', 'AIBONITO', 'PR', 'PRIMARY', '18.14', '-66.26', 'NA-US-PR-AIBONITO', 'FALSE', '', '', '')

(610, 'STANDARD', 'ANASCO', 'PR', 'PRIMARY', '18.28', '-67.14', 'NA-US-PR-ANASCO', 'FALSE', '', '', '')

(611, 'PO BOX', 'ANGELES', 'PR', 'PRIMARY', '18.28', '-66.79', 'NA-US-PR-ANGELES', 'FALSE', '', '', '')

(612, 'STANDARD', 'ARECIBO', 'PR', 'PRIMARY', '18.45', '-66.73', 'NA-US-PR-ARECIBO', 'FALSE', '', '', '')

(601, 'STANDARD', 'ADJUNTAS', 'PR', 'PRIMARY', '18.16', '-66.72', 'NA-US-PR-ADJUNTAS', 'FALSE', '', '', '')

(631, 'PO BOX', 'CASTANER', 'PR', 'PRIMARY', '18.19', '-66.82', 'NA-US-PR-CASTANER', 'FALSE', '', '', '')

(602, 'STANDARD', 'AGUADA', 'PR', 'PRIMARY', '18.38', '-67.18', 'NA-US-PR-AGUADA', 'FALSE', '', '', '')

(603, 'STANDARD', 'AGUADILLA', 'PR', 'PRIMARY', '18.43', '-67.15', 'NA-US-PR-AGUADILLA', 'FALSE', '', '', '')

(604, 'PO BOX', 'AGUADILLA', 'PR', 'PRIMARY', '18.43', '-67.15', 'NA-US-PR-AGUADILLA', 'FALSE', '', '', '')

(605, 'PO BOX', 'AGUADILLA', 'PR', 'PRIMARY', '18.43', '-67.15', 'NA-US-PR-AGUADILLA', 'FALSE', '', '', '')

```
2. Retrieve the first 10 rows of data from the zipcodes_two shard.
```
(42040, 'STANDARD', 'FARMINGTON', 'KY', 'PRIMARY', '36.67', '-88.53', 'NA-US-KY-FARMINGTON', 'FALSE', '465', '896', '11562973')

(41524, 'STANDARD', 'FEDSCREEK', 'KY', 'PRIMARY', '37.4', '-82.24', 'NA-US-KY-FEDSCREEK', 'FALSE', '', '', '')

(42533, 'STANDARD', 'FERGUSON', 'KY', 'PRIMARY', '37.06', '-84.59', 'NA-US-KY-FERGUSON', 'FALSE', '429', '761', '9555412')

(40022, 'STANDARD', 'FINCHVILLE', 'KY', 'PRIMARY', '38.15', '-85.31', 'NA-US-KY-FINCHVILLE', 'FALSE', '437', '839', '19909942')

(40023, 'STANDARD', 'FISHERVILLE', 'KY', 'PRIMARY', '38.16', '-85.42', 'NA-US-KY-FISHERVILLE', 'FALSE', '1884', '3733', '113020684')

(41743, 'PO BOX', 'FISTY', 'KY', 'PRIMARY', '37.33', '-83.1', 'NA-US-KY-FISTY', 'FALSE', '', '', '')

(41219, 'STANDARD', 'FLATGAP', 'KY', 'PRIMARY', '37.93', '-82.88', 'NA-US-KY-FLATGAP', 'FALSE', '708', '1397', '20395667')

(40935, 'STANDARD', 'FLAT LICK', 'KY', 'PRIMARY', '36.82', '-83.76', 'NA-US-KY-FLAT LICK', 'FALSE', '752', '1477', '14267237')

(40997, 'STANDARD', 'WALKER', 'KY', 'PRIMARY', '36.88', '-83.71', 'NA-US-KY-WALKER', 'FALSE', '', '', '')

(41139, 'STANDARD', 'FLATWOODS', 'KY', 'PRIMARY', '38.51', '-82.72', 'NA-US-KY-FLATWOODS', 'FALSE', '3692', '6748', '121902277')

```
3. Find the largest zipcode value in the zipcodes_one shard.
```
(47750,)
```
4. Find the smallest zipcode value in the zipcodes_two shard.
```
(38257,)
```
## Special Thanks!
Thank you Dre Owens and Josh Brown for bouncing of ideas for this project!
