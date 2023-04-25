# Homelab DNS

> This is a work in progress. Documentation is lacking and features are unfinished.

I am setting up my homelab and need a few more capabilities than what Pi-hole offers out of the box - time to plug a few containers together and throw them on an old Raspberry Pi.

## DNS Resolution Chain

* incoming request on :53
* forward to bind9 for local resolution of my private DNS zone
* forward to Pi-hole for ad blocking
* forward to Cloudflare for public lookups and malware blocking DNS over HTTPS
  * using [dnscrypt-proxy](https://hub.docker.com/r/klutchell/dnscrypt-proxy) and [Cloudflare malware blocking DoH](https://developers.cloudflare.com/1.1.1.1/setup/#dns-over-https-doh)
