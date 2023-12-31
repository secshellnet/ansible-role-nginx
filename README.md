# ansible-role-nginx

This role configures your nginx, it creates vhosts, aquires certificates using acme-dns-01 challenge and cloudflare dns api. If a unique ipv6 address is configured for a vhost, it will be added to the dns zone.

## Known issues
- `acme.sh` installation requires an ipv4 address, to download packages from [`github.com`](https://github.com/acmesh-official/acme.sh)

## Variables
### `group_vars/all/vars.yaml`
```yaml
---
nginx_cert_email: "acme@example.com"
```

### `group_vars/all/vault`
```yaml
---
nginx_cf_token:
  example.com: ""
  example.net: ""
```

### `host_vars/server1/vars.yaml`
```yaml
nginx_vhosts:
  - domain: service-one.example.com
    proxy_pass: 'http://[::1]:8080/'

  - domain: service-two.example.com
    proxy_pass: 'http://[::1]:8081/'
    proxy_pass_location: "~* ^(\\/_matrix|\\/_synapse\\/client)"  # e.g. for synapse matrix server
    ipv6_addr: 2001:4b80:7545:d1ca::2  # defaults to ::
    ipv6_prefix: 64  # defaults to 128
    interface: eth0  # defaults to first ethernet interface
    client_max_body_size: 200M
    additional_locations:
      - path: '/static/'
        raw_lines:
          - "alias /opt/netbox/netbox/static/;"
    acls:
      - action: allow
        value: 0.0.0.0/0
      - action: deny
        value: ::/0
    auth_basic:
      title: "SECURITY"
      credentials:
        - username: admin
          password: nimda
```
