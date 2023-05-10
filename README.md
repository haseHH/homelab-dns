# Homelab DNS

[![MIT license](https://img.shields.io/badge/License-MIT-blue?style=flat&labelColor=dimgrey)](./LICENSE)
[![Made with Visual Studio Code](https://img.shields.io/badge/Made%20with%20VSCode-dimgrey?style=flat&logo=visual%20studio%20code&logoColor=blue)](https://code.visualstudio.com/)
[![Deploys with Ansible](https://img.shields.io/badge/Deploys%20with%20Ansible-dimgrey?style=flat&logo=ansible&logoColor=black)](https://www.ansible.com/)
[![Includes Pi-hole](https://img.shields.io/badge/Includes%20Pi%20Hole-dimgrey?style=flat&logo=pihole&logoColor=firebrick)](https://pi-hole.net/)
[![Deploys on Docker](https://img.shields.io/badge/Deploys%20on%20Docker-dimgrey?style=flat&logo=docker&logoColor=blue)](https://www.docker.com/)
[![Runs on Raspberry Pi](https://img.shields.io/badge/Runs%20on%20Raspberry%20Pi-dimgrey?style=flat&logo=raspberrypi&logoColor=firebrick)](https://www.raspberrypi.org/)

> This is a work in progress. Documentation is lacking and features are unfinished.

I am setting up my homelab and need a few more capabilities than what Pi-hole offers out of the box - time to plug a few containers together and throw them on an old Raspberry Pi.

## DNS Resolution Chain

* incoming request on :53
* forward to bind9 for local resolution of a private DNS zone
* forward to Pi-hole for ad blocking
* forward to Cloudflare for public lookups and malware blocking DNS over HTTPS
  * using [dnscrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy) and [Cloudflare malware blocking DoH](https://developers.cloudflare.com/1.1.1.1/setup/#dns-over-https-doh)

## Preparation

Setup your Pi with Raspberry Pi OS Lite (or any other system with any Debian based distribution, I didn't test this on anything else though), set your hostname to `ns` and set a static IP. You'll also want to activate the SSH server if it is not active yet.

Either use the devcontainer for the setup or [install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html), for example like this:

```bash
# get pip
sudo apt-get install -y python3-pip

# setup Ansible
pip3 install ansible
```

Then create copies of all files containing `example.` and remove the prefix from their name. Adjust the contents according to your environment and run the playbook using `ansible-playbook main.yml`.

## Components

### BIND 9 DNS Server

* Image: [cytopia/docker-bind](https://github.com/cytopia/docker-bind)
* Entrypoint for DNS requests for our chain
* DNS server for the zone used by the homelab, e.g. `home.example.com`

### Pi-hole

* Image: [pihole/pihole](https://github.com/pi-hole/docker-pi-hole)
* Adblocker for the network on the DNS level

### dnscrypt-proxy

* Image: [klutchell/dnscrypt-proxy](https://github.com/klutchell/dnscrypt-proxy-docker)
* Forwards DNS requests over HTTPS, hiding them from your ISP
* Configured to use [Cloudflares malware-blocking DoH endpoint](https://developers.cloudflare.com/1.1.1.1/setup/#dns-over-https-doh)
  * Cloudflare puts emphasize on speed, availability and privacy
  * Free malware blocking is always a nice bonus, aight?

## Webinterfaces

Some applications offer webinterfaces and APIs, these are made available via [traefik](https://traefik.io/) and can be de-/activated in the `Configure webinterfaces` section of your [config.yml](./example.config.yml). Most services are informational endpoints, but Pi-hole offers some configuration via the GUI, which will be persisted in the file system as well.

### Traefik Dashboard and API

Informational dashboard and API, can be made available on the `traefik`-subdomain, e.g. `traefik.ns.home.example.com`.

### DNS Record Service

A small webserver with a list of all DNS entries that were configured on the BIND9 server, can be served at the host FQDN, e.g. `ns.home.example.com`. Available formats are:

* `HTML` aka human readable tables
* `JSON` for automated parsing, available at `/api.json`
* `YAML` for automated parsing, available at `/api.yaml`

### Pi-hole GUI

Administrative interface, can be made available on the `pihole`-subdomain, e.g. `pihole.ns.home.example.com`.

## Acknowledgements

Ansible configuration insipired by and adapted from Jeff Geerlings [Internet Pi](https://github.com/geerlingguy/internet-pi).
