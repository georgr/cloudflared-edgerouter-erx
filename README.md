# cloudflared-edgerouter-erx

## Setup

* on local machine install golang
* get cloudflared sources
`mkdir cloudflared`
`go get -d github.com/cloudflare/cloudflared/cmd/cloudflared`

* ERX architecture is MIPS little-endian therefore build with GOARCH 'mipsle'
`GOOS=linux GOARCH=mipsle go build -ldflags="-s -w" -v -x github.com/cloudflare/cloudflared/cmd/cloudflared`

* copy binary with scp to erx router
* 

