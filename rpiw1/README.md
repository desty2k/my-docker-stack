# RaspberryPi W1

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
    cp -r my-docker-stack/rpiw1/* /docker/
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
5. Restart Authelia
    ```shell
    docker container restart authelia
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

### Home Assistant

#### Traefik proxy
Follow these steps if you changed subnet for Docker network or Traefik internal IP address.
1. Open `/docker/appdata/homeassistant/configuration.yaml` with text editor
2. Add your Traefik instance internal IP address to trusted proxies. For example:
    ```yaml
    http:
      use_x_forwarded_for: true
      trusted_proxies:
        - 172.20.0.200
    ```
