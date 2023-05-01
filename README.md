# Homelab DNS

> This is a work in progress. Documentation is lacking and features are unfinished.

I am setting up my homelab and need a few more capabilities than what Pi-hole offers out of the box - time to plug a few containers together and throw them on an old Raspberry Pi.

## DNS Resolution Chain

* incoming request on :53
* forward to bind9 for local resolution of my private DNS zone
* forward to Pi-hole for ad blocking
* forward to Cloudflare for public lookups and malware blocking DNS over HTTPS
  * using [dnscrypt-proxy](https://hub.docker.com/r/klutchell/dnscrypt-proxy) and [Cloudflare malware blocking DoH](https://developers.cloudflare.com/1.1.1.1/setup/#dns-over-https-doh)

## Preparation

Setup your Pi with Raspberry Pi OS Lite (or any other system with any Debian based distribution, I didn't thest this on anything else though), set your hostname to `ns` and set a static IP. You'll also want to activate the SSH server if it is not active yet.

Either use the devcontainer for the setup or [install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html), for example like this:

```bash
# get pip
sudo apt-get install -y python3-pip

# setup Ansible
pip3 install ansible

# install this playbooks requirements (may need a refresh of the session)
ansible-galaxy collection install -r requirements.yml
```

Then create copies of all files containing `example.` and remove the prefix from their name. Adjust the contents according to your environment and run the playbook using `ansible-playbook main.yml`.

## Acknowledgements

Ansible configuration insipired by and adapted from Jeff Geerlings [Internet Pi](https://github.com/geerlingguy/internet-pi).
