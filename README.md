# CaddyStack

A biased, containerized local web development stack.

This stack provides:

- A reverse proxy based on [Caddy](https://caddyserver.com/) and [Caddy Docker Proxy](https://github.com/lucaslorentz/caddy-docker-proxy) that can be configured via labels on _other_ web containers.
- A Mailpit instance for testing mail delivery via SMTP on port 1025.
- A DNS server running on port 1053 that resolves `.test` and `.internal` domains.

## Requirements

- Docker (Podman and other runtime support is possible, but not documented)
- Docker Compose

This stack has been tested on Debian Linux using the included `docker.io` package and on macOS using `colima`.

## Quick Start

```sh
git clone https://github.com/freethoughtdesign/caddystack
cd caddystack
docker network create caddystack  # Only needs to be run once.
docker compose up -d
```

Next, configure your system DNS to resolve `*.test` and `*.internal` to `127.0.0.1`.

There are a few options:

1. Add a resolver to your local system that points to the included DNS server. (See below.)
2. If you have a local DNS server on your network, add records to resolve all `*.test` and `*.internal` hosts to `127.0.0.1`.
3. Manually add lines to `/etc/hosts`. For example:
   ```
   127.0.0.1  mailpit.internal anything.test
   ```

Once DNS is configured, `ping mailpit.internal` to see if you get a response of `127.0.0.1`. If so, visit http://mailpit.internal in your browser to see the Mailpit UI.

In another directory, create a `compose.yml` file containing the following:

```
# TBD
```

## Using the included DNS server

```
# Test the DNS server first:
nslookup -port=1053 anything.test localhost
```

The above DNS test should return something like the following:

```
Server:		localhost
Address:	::1#1053

Name:	anything.test
Address: 127.0.0.1
Name:	anything.test
Address: 127.0.0.1
```

#### Add a resolver to `systemd-resolved` (Linux)

Add an extra `[Resolve]` block to `/etc/systemd/resolved.conf`:

```
[Resolve]
DNS=127.0.0.153:1053
Domains=~test ~internal
```

Restart `systemd-resolved`:

```
sudo systemctl restart systemd-resolved
```
