# new

## Usage
1. Install `docker` and `docker compose` v2.0.0 or later.
2. Update all secrets in `secrets` directory.
3. Update environment variables in `.env` file.
4. Pull images and spin up containers using `start` script.
    ```shell
    chmod +x start
    ./start
    ```
5. Configure services.

## TODO

- migrate to Icinga2?
- use unprivileged ports
- add docs
- use higher ports - 10xxx
- integrate all services with LDAP

### Add services
- Jellyfin
- Nextcloud
- Harbor registry?
- Gitlab server and runner / Gitea
- Bitnami OpenLDAP?
- zigbee2mqtt
