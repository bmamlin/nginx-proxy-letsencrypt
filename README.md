Automatic, secure reverse proxy of subdomains.

## Requirements

1. Your host server must be publicly reachable (required to use the automatically 
generated & renewed certificates from [LetEncrypt](https://letsencrypt.org)).
2. You must have Docker and Docker Compose installed on the host server.

## Setup

Set up your DNS to forward a subdomain. For example, by creating two `A` records:
one for the subdomain and a second with a wildcard, both pointing to the IP address 
of the host server.

`foo.example.com    300  A  1.2.3.4`<br>
`*.foo.example.com  300  A  1.2.3.4`

Create a "web" network. Docker containers must be on this network to be proxied.
This only needs to be done once.

`$ docker network create web`

## Start Proxy Server

Start the web server proxy machinery. This starts up a central nginx web server,
a [docker-gen](https://github.com/jwilder/docker-gen) container to automatically 
reverse proxy new containers, and a letsencrypt companion container that 
automatically generates and renews a TLS certificate for each site using 
[LetsEncrypt](https://letsencrypt.org).

`$ docker-compose up -d`

## Add images to be securely proxied

Then add any images you like to be securely proxied.

`$ ./add foo.example.com myimage --name foo`

It can take a minute or two for secure certs to be generated. You can monitor 
the certs folder for evidence of certificates.
