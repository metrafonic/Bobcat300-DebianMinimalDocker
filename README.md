# Bobcat300-DebianMinimalDocker
A minimal docker-compose config for running a helium lora gateway on a bobcat 300.

## Warning
Currently having issues with the pf crashing.  
Do not use in prod yet!  
Packetforwarder crashes during **downlinks** - beacons etc

## Details
Currently only tested on the `G285`

Uses the following docker images:
- `rakwireless/udp-packet-forwarder:latest` - Stable packet forwarder. I had issues with other images including the one provided by sicXnull. The CPU usage of this one is also half as much - good for solar deployments.
- `quay.io/team-helium/miner:gateway-latest` - Official gateway image

### Credits:
- [sicXnull](https://github.com/sicXnull) has done some amazing work on reverse engineering the bobcat 300. Check out their repos:
  - https://github.com/sicXnull/Bobcat300-Debian
  - https://github.com/sicXnull/BobcatDashboard
 
## Preparations
1. Install the debian image provided by https://github.com/sicXnull/Bobcat300-Debian
2. Remove the dashboard and additional packages like strongswan, nginx-common, php etc (to your liking)
```
sh /var/dashboard/uninstall.sh
apt remove php* nginx* strongswan* openvpn*
reboot
```
3. Ensure latest update: `apt update && apt upgrade -y`
4. Reboot and install docker compose `apt install docker-compose`
5. Set root user password `passwd`
6. Consider adding ssh-keys and disabling password based auth by adding `PasswordAuthentication no` to `/etc/ssh/ssh_config` 

## Install
```
mkdir gw && cd gw
curl -fsSL https://raw.githubusercontent.com/metrafonic/Bobcat300-DebianMinimalDocker/refs/heads/main/docker-compose.yml-o docker-compose.yml
```
Modify the compose file to match your region. Current config uses `GW_REGION=EU868` and `BAND=eu_863_870`. Make sure to edit them both if this does not match.

The `BAND` can be one of these values: `as_915_921(as_923_3)`, `as_915_928(as_915_1)`, `as_917_920(as_923_4)`, `as_920_923(as_923_2)`, `au_915_928`, `cn_470_510`, `eu_433`, `eu_863_870`, `in_865_867`, `kr_920_923`, `ru_864_870`, and `us_902_928`.

The `REGION` can be one of the following values `US915 | EU868 | EU433 | CN470 | CN779 | AU915 | AS923 | KR920 | IN865`

Start the services
```
docker-compose up -d
```
Check the logs
```
docker compose logs -f --tail=1000
```
Check miner animal name:
```
docker exec gw_helium-miner_1 helium_gateway key info
```
