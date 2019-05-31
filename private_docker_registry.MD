# Set up the private docker registry.

## Create an htpasswd file containing the login credentials for the initial account.

mkdir -p ~/registry/auth
docker run --entrypoint htpasswd \
  registry:2 -Bbn docker d0ck3rrU73z > ~/registry/auth/htpasswd
## Create a self-signed certificate for the registry. For common name, enter the hostname of the registry server, which is ip-10-0-1-101. For the other prompts, just hit enter to accept the default.

mkdir -p ~/registry/certs
openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout ~/registry/certs/domain.key \
  -x509 -days 365 -out ~/registry/certs/domain.crt
  
## Create a container to run the registry.

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

## Once the registry starts up, verify that it is responsive. It's OK if this command returns nothing, just make sure it does not fail.

curl -k https://localhost:443
