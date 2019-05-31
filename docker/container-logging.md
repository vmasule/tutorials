
## Configuring syslog on one of the Docker instances

#### Configure syslog.

You will need to open rsyslog.conf and make a few changes:

~~~~
 vim /etc/rsyslog.conf
~~~~

Uncomment the two UDP syslog receptions:

~~~~
#$ModLoad imudp
#$UDPServerRun 514
~~~~

to

~~~~
$ModLoad imudp
$UDPServerRun 514
~~~~


#### Configure Docker to use syslog.

Create the daemon.json file.

~~~~
sudo mkdir /etc/docker
vim /etc/docker/daemon.json
~~~~

Add the following content.

~~~~
{
  "log-driver": "syslog",
  "log-opts": {
    "syslog-address": "udp://PRIVATE_IP:514"
  }
}
~~~~

#### Create a container using syslog.

Enable and start the Dockere service.

~~~~
 sudo systemctl enable docker
 sudo systemctl start docker
~~~~

Create a container called syslog-logging using the httpd image.

~~~~
docker container run -d --name syslog-logging httpd
~~~~

#### Create a container using a JSON file.

Create a container that uses the JSON file for logging.

~~~~
docker container run -d --name json-logging --log-driver json-file httpd
~~~~

## Verify that the `syslog-logging` container is sending its logs to syslog.

Make sure that the syslog-logging container is logging to syslog by checking the message log file:

~~~~
tail /var/log/messages
~~~~

#### Verify that the `json-logging` container is sending its logs to the JSON file.

Execute docker logs for the json-logging container.

~~~~
 docker logs json-logging
~~~~
