# my-docker-stack

My personal Docker stack running on two indepedent RaspberryPis 4B 4 GB. Devices are running in two locations 
in diffrent network subnets: `rpis1` in `192.168.10.0/24` and `rpiw1` in `192.168.20.0/24`. Thanks to Wireguard 
both networks are pernamently connected with Site-to-Site VPN tunnel, 
so `rpis1` can reach devices in `rpiw1`'s network and vice versa.

## RPiS1

Networks:
- LAN - `192.168.10.0/24`
- Docker internal - `172.10.0.0/16`
- Docker VPN - `10.10.0.0/24`

### Connection
- Traefik
- Authelia
- Wireguard StS
- Wireguard Easy
- Certdumper
- Duckdns

### Databases
- Redis
- MariaDB
- InfluxDB
- PhpMyAdmin
- OpenLDAP
- Bitwarden (Vaultwarden)

### DNS servers
- Pi-Hole
- Unbound

### Container management
- Portainer
- Watchtower

### Smart home
- Home Assistant
- ZWaveJS2MQTT
- Mosquitto
- Tasmobackup
- Tasmoadmin
- NTP server

### Telemetry and monitoring
- Telegraf
- Grafana
- Unifi

### Web servers
- Homer
- Nginx
- Whoami

## RPiW1

Networks:
- LAN - `192.168.20.0/24`
- Docker internal - `172.20.0.0/16`
- Docker VPN - `10.20.0.0/24`

### Connection
- DuckDNS
- Traefik
- Traefik Certdumper
- Authelia
- Wireguard StS
- Wireguard Easy

### Smart home
- Home Assistant
- Mosquitto

### DNS servers
- Adguard
- Unbound
- DHCPHelper

### Databases
- Redis
- MariaDB
- PhpMyAdmin

### Container management
- Portainer
- Watchtower

### Web servers
- Whoami

## Installation

Follow installation steps described in READMEs for rpis1 and rpiw1 respectively.
