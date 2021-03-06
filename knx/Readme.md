General remark:
you can pull all images from docker-hub
Thus, from Git you only need docker-compose.yml and the folder config

You need docker, curl and unzip installed for this readme to work.
## Preparation and installation of Docker
```   
sudo apt-get install curl unzip subversion

sudo echo "deb https://apt.dockerproject.org/repo debian-jessie main" >> /etc/apt/sources.list.d/docker.list
sudo apt-get update 
sudo apt-get install docker-engine
sudo service docker start
sudo docker run hello-world
```

## Install Docker-Compose
```
curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose
```


## Background
We will use several docker containers. Most come from the makers of the specific tool (grafana) or docker itself (influxdb) or from other docker contributors (nginx)
Some were created by myself (knxd, owfs, smarthome-ng)
Docker-compose is a tool for orchestration of several containers. The docker-compose.yml explains how the containers interact
Docker containers are intended to be read only. If data shall be persistent, a directory on the Host-System (your physical machine) must be mounted into the docker container. This is configured in the docker-compose.yml.
Generally, only two things need to be configured: docker-compose.yml and the configuration files in /mnt2/Docker_configs/
After this, you can run everything with "docker-compose up". Below, well go through everything. I provide a sample config. Let's get started.


## Get Configuration
Create a folder to store your configuration files, e.g. 
```
mkdir -p /mnt2/Docker_Configs/
cd /mnt2/Docker_Configs/
svn checkout https://github.com/henfri/docker//trunk/knx/config
``` 
(via git it is not possible to download a single folder; you can also use git clone, but then you will get more than just this directory)


Create a folder for your docker-compose.yml and download it
```
curl -L https://raw.githubusercontent.com/henfri/docker/master/knx/docker-compose.yml
```

## Configuration of Smarthome.py
Edit the name/IP of the knx-interface in /mnt2/Docker_configs/config/smarthomeNGconfig/etc/plugin.conf in the knx section
If you use custom plugins, add them as shown in the example. 
e.g.
``` 
cd /mnt2/Docker_Configs/config/smarthomeNGconfig/plugins/
git clone https://github.com/SgtSeppel/influxdb.git
mv influxdb influx
```

Also you can pass devices from /dev/ to the container.



## Configuration of KNXD
Change the commandline of knxd in the docker-compose.yml to match your situation. If you use a USB device, adopt the configuration from OWFS (see below) do pass the Device into the container


## Configuration of OWFS
Change the commandline Argument to owfs in docker-compose.yml (under command:)
Pass your hardware to the docker container as in the example.
To make sure that your device does always have the same name on your host, please use UDEV rules (here /dev/myPBM)

## Configuration of smartvisu
create a folder for your smartvisu configuration, e.g. /mnt2/DockerConfigs/smartvisu/ and extract smartVISU_2.7.zip there
```
cd /mnt2/DockerConfigs/config/smartvisu
curl http://www.smartvisu.de/download/smartVISU_2.7.zip
mv smartVISU/* ./
rm -R smartVISU
```

## And start
```
run docker-compose build && docker-compose up in the folder of docker-compose.yml
```

You can reach Smartvisu at Dockerhost:8080
