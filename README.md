# Create an cloudflare argo tunnel on ubiquity edgerouter X (erx)

## Setup

* on local machine install golang
* get cloudflared sources

`mkdir cloudflared`

`go get -d github.com/cloudflare/cloudflared/cmd/cloudflared`

* ERX architecture is MIPS little-endian therefore build with GOARCH 'mipsle'

`GOOS=linux GOARCH=mipsle go build -ldflags="-s -w" -v -x github.com/cloudflare/cloudflared/cmd/cloudflared`

* copy binary with scp to erx router
* on the router:

```
sudo su
chmod +x cloudflared
mv cloudflared /usr/local/bin/
mkdir /etc/cloudflared

cloudflared login
# copy link printed on console and paste it in browser
# autorize domain to be tunneld on webinterface (you get a list with domains, if not open link from console again)
# if everythin works you get:
# You have successfully logged in.
# If you wish to copy your credentials to a server, they have been saved to:
# /root/.cloudflared/cert.pem

cloudflared tunnel create your-tunnel-name
# Created tunnel your-tunnel-name with UUID

vi /etc/cloudflared/config.yml
```

* /etc/cloudflared/config.yml:

```
#UUID is from output before
tunnel: UUID
credentials-file: /root/.cloudflared/UUID.json

ingress:
  - hostname: HOSTNAME
    service: https://INTERNAL-SERVICE-IP-OR-DNS-NAME
  - service: http_status:404
```

* on the router:

```
cloudflared service install
systemctl enable cloudflared.service
# disable auto-update since there is no mipsle build from cloudflare
systemctl disable cloudflared-update.service
systemctl disable cloudflared-update.timer
# start service (or restart if already started)
systemctl start cloudflared.service
# check if daemon is running
ps afux | grep cloudflared
```
