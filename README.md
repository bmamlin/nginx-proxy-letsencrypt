Automatic, secure reverse proxy of subdomains.

## Requirements

1. Your host server must be publicly reachable (required to use the automatically generated & renewed certificates from [LetEncrypt](https://letsencrypt.org)).
2. You must have Docker and Docker Compose installed on the host server.

## Setup

First, set up your DNS to forward a subdomain. For example, by creating 
two `A` records: one for the subdomain and a second with a wildcard, 
both pointing to the IP address of the host server.

`foo.example.com    300  A  1.2.3.4`<br>
`*.foo.example.com  300  A  1.2.3.4`

Copy the `sample.env` file to `.env` and edit it to specify your 
postmaster email address. This email address will be associated with 
each LetsEncrypt certificate, where you will get notifications of 
expiring certificates (if the automatic renewal has failed for some 
reason).

## Start Proxy Server

Start the web server proxy machinery. This starts up a central nginx 
web server, a [docker-gen](https://github.com/jwilder/docker-gen) 
container to automatically reverse proxy new containers, and a 
letsencrypt companion container that automatically generates and renews 
a TLS certificate for each site using [LetsEncrypt](https://letsencrypt.org).

`$ docker-compose up -d`

## Adding images to be securely proxied

By default, the proxy server will only have access to the bridge
network. To give the proxy access to other docker containers, you 
must connect their networks.

You can find out the networks of your containers using:

```bash
$ docker network ls
```

Assuming your proxy network is `nginxproxyletsencrypt_default` and 
the container you wish to proxy is on the `foo` network, you can 
enable the proxy with:

```bash
$ docker network connect foo nginxproxyletsencrypt_default
```

It can take a minute or two for secure certs to be generated. You 
can monitor the certs folder for evidence of certificates.
