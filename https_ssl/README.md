# HTTPS SSL

Domain: `ismaelnd.tech`. Load balancer: `13.135.253.118`.
Web servers: `13.40.131.171` and `16.60.159.142`.

`0-world_wide_web` reports DNS records for `www`, `lb-01`, `web-01`, and
`web-02` in that order, or for one named subdomain. It requires `dig`.

DNS A records to create in Namify:

| Host | Address |
| --- | --- |
| www | 13.135.253.118 |
| lb-01 | 13.135.253.118 |
| web-01 | 13.40.131.171 |
| web-02 | 16.60.159.142 |

`1-haproxy_ssl_termination` is the HAProxy configuration for HTTP and HTTPS.
`2-redirect_http_to_https` adds the permanent redirect on port 80.
Both configurations expect the combined PEM certificate and private key at
`/etc/haproxy/certs/www.ismaelnd.tech.pem` on lb-01.

Certbot issued a certificate for `www.ismaelnd.tech` on lb-01. HAProxy serves
HTTPS on port 443 and returns a 301 redirect from HTTP on port 80. The combined
certificate and private key are stored at `/etc/haproxy/certs/www.ismaelnd.tech.pem`
with root-only permissions. Certbot renewal hooks stop HAProxy for the standalone
HTTP challenge, rebuild the PEM after a successful renewal, and start HAProxy.

Verify DNS with `./0-world_wide_web ismaelnd.tech`. Check the redirect with
`curl -I http://www.ismaelnd.tech/` and HTTPS with
`curl https://www.ismaelnd.tech/`.

The scripts and configuration target Ubuntu 16.04 and HAProxy 1.5 or newer.
