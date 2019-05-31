## Set up the private docker registry.

#### Create an `htpasswd` file containing the login credentials for the initial account.

~~~~
mkdir -p ~/registry/auth
docker run --entrypoint htpasswd \
  registry:2 -Bbn docker d0ck3rrU73z > ~/registry/auth/htpasswd
~~~~
#### Create a self-signed certificate for the registry. For common name, enter the hostname of the registry server, which is `ip-10-0-1-101`. For the other prompts, just hit enter to accept the default.

~~~~
mkdir -p ~/registry/certs
openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout ~/registry/certs/domain.key \
  -x509 -days 365 -out ~/registry/certs/domain.crt
~~~~

#### Create a container to run the registry.

~~~~
docker run -d -p 443:443 --restart=always --name registry \
  -v /home/cloud_user/registry/certs:/certs \
  -v /home/cloud_user/registry/auth:/auth \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:443 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -e REGISTRY_AUTH=htpasswd \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2
~~~~

#### Once the registry starts up, verify that it is responsive. It's OK if this command returns nothing, just make sure it does not fail.

~~~~
curl -k https://localhost:443
~~~~

## Test the registry from the Docker workstation server.

#### Get the public hostname from the registry server. It should be ip-10-0-1-101.

~~~~
echo $HOSTNAME
~~~~

#### Add the registry's public self-signed certificate to `/etc/docker/certs.d`. The scp command is copying the file from the registry server to the workstation. The password is the normal `cloud_user` password provided by the lab.

~~~~
sudo mkdir -p /etc/docker/certs.d/ip-10-0-1-101:443
sudo scp cloud_user@ip-10-0-1-101:/home/cloud_user/registry/certs/domain.crt /etc/docker/certs.d/ip-10-0-1-101:443
~~~~

#### Log in to the private registry from the workstation. The credentials should be username docker and password `d0ck3rrU73z`.

~~~~
docker login ip-10-0-1-101:443
~~~~

#### Test the registry by pushing an image to it. You can pull any image from Docker hub and tag it appropriately to push it to the registry as a test image.

~~~~
docker pull ubuntu
docker tag ubuntu ip-10-0-1-101:443/test-image:1
docker push ip-10-0-1-101:443/test-image:1
~~~~

#### Verify image pulling by deleting the image locally and re-pulling it from the private repository.

~~~~
docker image rm ip-10-0-1-101:443/test-image:1
docker image rm ubuntu:latest
docker pull ip-10-0-1-101:443/test-image:1
~~~~
