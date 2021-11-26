# my-docker-stack

My personal Docker stack running on two indepedent RaspberryPis 4B 4 GB. Devices are running in two locations 
in diffrent network subnets: `rpis1` in `192.168.10.0/24` and `rpiw1` in `192.168.20.0/24`. Thanks to Wireguard 
both networks are pernamently connected with Site-to-Site VPN tunnel, 
so `rpis1` can reach devices in `rpiw1`'s network and vice versa.

## RPiS1 Services
RaspberryPi 4B 4 GB running in `192.168.10.0/24` network. Internal Docker network address is `172.10.0.0/16`.

### Connection
- Traefik
- Authelia
- Wireguard StS
- Wireguard Easy

### Smart home
- Home Assistant
- ZWaveJS2MQTT
- Mosquitto
- Tasmobackup
- Tasmoadmin
- NoisyMQTT

### DNS servers
- Pi-Hole
- Unbound

### Databases
- Redis
- MariaDB
- InfluxDB
- PhpMyAdmin
- Bitwarden

### Container management
- Portainer
- Watchtower

### Telemetry and monitoring
- Telegraf
- Grafana

### Web servers
- Homer
- Nginx
- Whoami

## RPiW1 Services
RaspberryPi 4B 4 GB running in `192.168.20.0/24` network. Internal Docker network address is `172.20.0.0/16`.

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
