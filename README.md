# easy-fulcrum

Easily run bitcoind + Fulcrum via Docker Compose

**CURRENTLY WIP, but should work without issues.**

## Why run this?

- Remove trust in other Bitcoin nodes or Electrum servers
- Drastically improve your transactional privacy in Bitcoin
- Faster/potentially more reliable wallet sync than using centralized servers, especially over clearnet
- Run in one place (home, VPS, etc.), connect many wallets/apps

## Requirements

As of writing, the following requirements apply:

- CPU: The more the merrier, but can run with low end hardware
- RAM: The more the merrier, but can run with low end hardware (if lower than 8GB you will need to remove dbcache and Fulcrum memory variables!)
- Disk: Fast disk is *very* important, you should only run this on an SSD minimum, NVMe drive recommended
  - Current disk usage as of October 2022:
    - bitcoind: `500GB`
    - Fulcrum: `120GB`
  - Recommended free space: `750GB+`

## Setup

- `git clone https://github.com/sethforprivacy/easy-fulcrum.git && cd easy-fulcrum`
- Configure RPC auth/user/password in the `.env` file
  - Be sure to change the default options, as they are insecure!
- Increase Fulcrum memory variable if you can afford to give it more than 2GB of RAM during initial sync
  - The more you give it here the faster initial sync will be, and this RAM is *only* allocated during initial sync
- Increase bitcoin dbcache variable in `bitcoin.conf` if you can afford to give it more than 2GB of RAM

## Running

- `docker compose up -d`

## Monitoring and maintenance

- To tail logs, i.e. follow sync status:
  - `docker compose logs --follow --tail=50`
- To update bitcoind/fulcrum:
  - `docker compose pull && docker compose up -d`
  
## Exposing ports

By default, only the p2p port for bitcoind and the SSL port for Fulcrum are exposed on the host. If you'd like to reach either of these externally forward the necessary ports from your router.

Note: if you need bitcoind RPC for other apps or tools, you can expose it by removing the `127.0.0.1:` before `8332:8332` in the `docker-compose.yml` file

## Connecting wallets

- If internal to your network, point your wallet (i.e. [Envoy](https://foundationdevices.com/envoy/) or [Sparrow](https://www.sparrowwallet.com/) to your hosts internal IP address (i.e. 192.168.x.xxx) and use port `50002`
  - i.e. for Envoy, use `ssl://192.168.1.2:50002` and for Sparrow use `192.168.1.2`, set port to `50002`, and check "Use SSL"
- If you're outside of the network, first forward port `50002` (if needed) to Fulcrum and then connect using the hosts external (internet-facing) IP address plus the port you exposed (normally also `50002`)
  - i.e. for Envoy, use `ssl://my.home.dns:50002` and for Sparrow use `my.home.dns`, set port to `50002`, and check "Use SSL"

## Credit

- @kylemanna for the excellent bitcoin Docker image: https://github.com/kylemanna/docker-bitcoind
- @cculianu for the incredible Fulcrum: https://github.com/cculianu/Fulcrum
- @jlopp for the awesome Bitcoin config and rpcauth generators
