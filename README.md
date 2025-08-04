# Bobcat300-DebianMinimalDocker
A minimal docker-compose config for running a helium lora gateway on a bobcat 300.


## Details
Currently only tested on the `G285`

Uses the following docker images:
- `heliumdiy/sx1302_hal` - Fast and common pf used by most diy miners.
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
git clone https://github.com/metrafonic/Bobcat300-DebianMinimalDocker && cd Bobcat300-DebianMinimalDocker
```
Modify the `REGION` value in the `docker-compose.yml`. The `REGION` can be one of the following values `US915 | EU868 | EU433 | CN470 | CN779 | AU915 | AS923 | KR920 | IN865`

Start the pf once, so that it creates the region file. We must modify it.
```
docker-compose up pktfwd -d && sleep 1 && docker compose down
```
Edit `packet_forwarder/configs/global_conf.json` and replace and replace the `spidev_path:` value with:
```
"spidev_path": "/dev/spidev1.0",
```
Start again:
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
