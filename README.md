# my-docker-stack
My personal docker stack running on RaspberryPi 4 model B.

## Services

- Portrainer - Container management
- Pi-hole - DNS server with ad blocking
- Unbound - DNS server with caching and DoT
- Wireguard - VPN server
- Traefik - reverse proxy 
- Nginx - HTTP/S server
- Bitwarden - password manager
- Home Assistant - home automation software
- Mosquitto - MQTT server
- Z-Wave JS - Z-Wave server
- Noisy MQTT - random network traffic generator
- Tasmoadmin - manager for Tasmota devices
- Tasmobackup - backup for Tasmota devices
- Homer - homepage with links for all services
- Remotely - remote control for desktops  
- Watchtower - Docker images updater

## How to use?

1. Clone this repository using `git`

```shell
git clone https://github.com/desty2k/my-docker-stack.git
```

2. Change current working directory

```shell
cd my-docker-stack
```

3. Update __all__ values in `.env` file
4. Create containers with `docker-compose`

```shell
docker-compose up -d
```

