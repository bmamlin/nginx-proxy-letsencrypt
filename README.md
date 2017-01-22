Automatic, secure reverse proxy of subdomains.

## Requirements

1. Your host server must be publicly reachable (required to use the automatically 
generated & renewed certificates from [LetEncrypt](https://letsencrypt.org)).
2. You must have Docker and Docker Compose installed on the host server.

## Setup

First, set up your DNS to forward a subdomain. For example, by creating two `A` records:
one for the subdomain and a second with a wildcard, both pointing to the IP address 
of the host server.

`foo.example.com    300  A  1.2.3.4`<br>
`*.foo.example.com  300  A  1.2.3.4`

Second, create a "web" network. Docker containers must be on this network to be proxied.
This only needs to be done once.

`$ docker network create web`

or, use the convenience script, to create the network:

`./init`

Lastly, edit the `config.env` file to specify your postmaster email address. This email 
address will be associated with each LetsEncrypt certificate, where you will get notifications 
of expiring certificates (if the automatic renewal has failed for some reason).

## Start Proxy Server

Start the web server proxy machinery. This starts up a central nginx web server,
a [docker-gen](https://github.com/jwilder/docker-gen) container to automatically 
reverse proxy new containers, and a letsencrypt companion container that 
automatically generates and renews a TLS certificate for each site using 
[LetsEncrypt](https://letsencrypt.org).

`$ docker-compose up -d`

## Add images to be securely proxied

Then add any images you like to be securely proxied:

```bash
$ docker run -d -e VIRTUAL_HOST="foo.example.com" \
                -e LETSENCRYPT_HOST="foo.example.com" \
                -e LETSENCRYPT_EMAIL="postmaster@example.com"
                --net web myimage \
                --name foo
```

or use the provided convenience `add` script that uses settings from `config.env`:

`$ ./add foo.example.com myimage --name foo`

It can take a minute or two for secure certs to be generated. You can monitor 
the certs folder for evidence of certificates.
