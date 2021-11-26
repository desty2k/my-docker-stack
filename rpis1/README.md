# RaspberryPi S1

## Installation steps

1. Clone `my-docker-stack` repo
    ```shell
    git clone https://github.com/desty2k/my-docker-stack.git
    ```
2. Create `docker` directory
    ```shell
    mkdir /docker
    ```
3. Copy all files from `my-docker-stack/rpis1/` directory to `docker` directory
    ```shell
    cp -r my-docker-stack/rpis1/* /docker/
    ```
4. Change working directory to `docker`
    ```shell
    cd /docker
    ```
5. Set all variables in `.env` file.
6. Fill all secret files in `secrets` directory
7. Download images and start containers
    ```shell
    docker compose up -d --force-recreate
    ```

## Service specific post installation steps

### Notes

$SERVER_IP - static IP of your server set in .env file

### MariaDB
1. Open `$SERVER_IP:8010` in your web browser
2. You should see PhpMyAdmin login page
3. Log in as root, use password you set in `secrets/mysql_root_password` file
4. Create new user `authelia`. Use password you set in `secrets/authelia_storage_mysql_password`. 
   Tick `Create database with same name and grant all privileges` option.
5. Create new user `tasmobackup`. Use password you set for environment variable `MARIADB_TASMOBACKUP_PASSWORD` (in `.env` file). 
   Tick `Create database with same name and grant all privileges` option.   
6. Restart Authelia and Tasmobackup
    ```shell
    docker container restart authelia tasmobackup
    ```

### Authelia
1. Go to `https://duo.com/`, create `Duo` account, create custom application, generate integration key, create users, 
   update `secrets/authelia_duo_api_secret_key` file with obtained secret key
2. Open `/docker/appdata/authelia/configuration.yml` with text editor
3. Replace `example.com` with your domain name / DDNS
4. Update values in `duo_api` section
   ```yaml
   duo_api:
     hostname: ""
     integration_key: ""
   ```
5. Update your networks in `access_control` section
   ```yaml
   access_control:
     networks:
       - name: internal
         networks:
           - 192.168.20.0/24
           - 192.168.10.0/24
       - name: VPN
         networks: 10.6.0.0/16
   ```
6. Save configuration file
7. Open Authelia container shell in terminal
   ```shell
   docker exec -it authelia /bin/sh
   ```
8. For each user generate password using `hash-password`
   ```shell
   authelia hash-password -c /config/configuration.yml
   ```
9. To exit shell use `exit` command
10. Open `/docker/appdata/authelia/users_database.yml` with text editor
11. Create users. Their nicknames __must__ match nicknames in `Duo`.
12. Restart Authelia
   ```shell
   docker container restart authelia
   ```

### InfluxDB
1. Open `$SERVER_IP:8086` in your web browser
2. Follow first setup steps
3. Click your avatar on the level then click `About`
4. Copy and save your `Organization ID`, we will need it later
5. Go to `Data` then `Buckets` with 7 days retention
6. Create `homeassistant` and `rpis1` buckets
7. Go to `API Tokens`
8. Create `Grafana` token with read permission to all containers
9. Create `Home Assistant` token with write permission to `homeassistant` bucket
10. Create `Telegraf` token with write permission to `rpis1` bucket
11. Copy and save tokens, they will be required to set up Telegraf, Grafana and Home Assistant telemetry

### Telegraf
Follow these steps __after__ configuring InfluxDB.
1. Configure InfluxDB
2. Open `/docker/appdata/telegraf/telegraf.yaml` with text editor
3. Update values for `token` and `organization` in `[[outputs.influxdb_v2]]` section, 
   note that `organization` is not an organization ID name but its name.
   ```yaml
   [[outputs.influxdb_v2]]	
     ## The URLs of the InfluxDB cluster nodes.
     ##
     ## Multiple URLs can be specified for a single cluster, only ONE of the
     ## urls will be written to each interval.
     ## urls exp: http://127.0.0.1:8086
     urls = ["http://influxdb:8086"]
   
     ## Token for authentication.
     token = YOUR_TELEGRAF_API_TOKEN_HERE
      
     ## NAME NOT ID!!!
     ## Organization is the name of the organization you wish to write to; must exist.
     organization = YOUR_INFLUXDB_ORGANIZATION_NAME_HERE
   
     ## Destination bucket to write into.
     bucket = "rpis1"
   ```
4. Restart Telegraf
   ```shell
   docker container restart telegraf
   ```
5. Open `$SERVER_IP:8086` in your web browser
6. Login to InfluxDB admin panel
7. Click `Explore`
8. Select `rpis1` bucket
9. If filters list is not empty it means that Telegraf started writing data to InfluxDB


### Home Assistant

#### InfluxDB
Follow these steps __after__ configuring InfluxDB.
1. Configure InfluxDB
2. Open `/docker/appdata/homeassistant/configuration.yaml` with text editor
3. Fill InfluxDB configuration, note that `organization` is not an organization name but its ID.
   ```yaml
   influxdb:
     api_version: 2
     ssl: false
     host: influxdb
     port: 8086
     token: INFLUXDB_TOKEN
     organization: INFLUXDB_ORGANIZATION_ID
     bucket: homeassistant
     tags_attributes:
       - friendly_name
     exclude:
       domains:
         - automation
         - group
   ```

#### Traefik proxy
Follow these steps if you changed subnet for Docker network or Traefik internal IP address.
1. Open `/docker/appdata/homeassistant/configuration.yaml` with text editor
2. Add your Traefik instance internal IP address to trusted proxies. For example:
    ```yaml
    http:
      use_x_forwarded_for: true
      trusted_proxies:
        - 172.10.0.200
    ```
