# System Prompt

Hey You are a dev ops expert with years of experience in your field. Your profession is also your hobby and you host various open source software on your homelab server. Which is more secure than ford knox and resilient against failures. A friends wants to get started with the topic and needs your assistance. Are you ready for the challenge?

# Prompt
For testing out possible services for the final setup I first want to test them out on my normal computer with docker. So i want you to create a docker compose for me with the following services: * Seafile

* Vaultwarden

* Paperless-ngx

* Immich

Please secure them behind a firewall like opnsense and make it reachable via vpn :). Please explain the final docker compose and how the services interact with each other especially the networking parts i always struggle with.
# Response

Okay, this is a fantastic way to get your feet wet! Testing services locally with Docker Compose before deploying them "for real" is a smart move. It lets you understand how they work, configure them, and break things without affecting a live environment.

Regarding the firewall (OPNsense) and VPN part: That's crucial for a _production_ homelab setup, but it's generally **not** something you'd run _inside_ the same Docker Compose file on your local test machine. OPNsense is a full firewall OS, typically run on dedicated hardware or a VM acting as your network's gateway. A VPN server would usually run on that firewall or another dedicated machine/container accessible from the outside world.

**For local testing on your computer, we'll simulate the "secure access" part differently:**

1. **No Direct Exposure:** We won't expose the individual services' ports directly to your host machine (except for the tool managing access).
2. **Reverse Proxy:** We'll use a **reverse proxy** service _within_ Docker Compose (like Nginx Proxy Manager, Traefik, or Caddy). This proxy _will_ expose the standard web ports (80 and 443) to your host. All traffic to your services will go _through_ this proxy. This mimics how you'd access services behind a firewall/VPN in production, allowing you to manage access, use custom domain names (even just locally), and handle SSL (though local SSL is a bit more involved). We'll use Nginx Proxy Manager (NPM) here as it's quite user-friendly.
3. **Custom Docker Network:** All services will reside on a private Docker network, allowing them to communicate with each other using their service names, but isolated from your main network.

This setup lets you test the services and their interactions effectively in an isolated environment on your PC. The _real_ OPNsense/VPN layer comes later when you build your actual homelab server.

---

Let's build the `docker-compose.yml` file.

**1. Create Project Structure:**

Create a folder for your project, e.g., `homelab-test`. Inside this folder, create:

- `docker-compose.yml` (the file we'll create below)
- `.env` (a file to store secrets and configuration)
- Optionally, subfolders if you prefer bind mounts for some configs, but we'll primarily use named Docker volumes for data persistence.

**2. Create the `.env` file:**

This file stores environment variables used by the `docker-compose.yml`. **Never commit this file to Git if it contains real secrets!** Fill in appropriate values.

Code-Snippet

```
# General Settings
PUID=1000                     # Your user ID (run `id -u` in Linux/macOS terminal)
PGID=1000                     # Your group ID (run `id -g` in Linux/macOS terminal)
TZ=Europe/Berlin              # Your timezone (e.g., America/New_York, Europe/London)

# Nginx Proxy Manager - Will be accessed via http://localhost:81 initially
# Default login: Email: admin@example.com, Password: changeme

# Seafile Settings (uses internal MariaDB)
SEAFILE_ADMIN_EMAIL=admin@example.com
SEAFILE_ADMIN_PASSWORD=a-very-secure-password # Change this!
SEAFILE_SERVER_HOSTNAME=seafile.localtest.me  # We'll map this later
DB_ROOT_PASSWORD=a-very-secure-db-root-password # Change this! MariaDB root password

# Vaultwarden Settings
VAULTWARDEN_ADMIN_TOKEN=some-random-secure-token # Optional: Set for admin page access, change this!
VAULTWARDEN_SIGNUPS_ALLOWED=true # Set to false after creating your user
VAULTWARDEN_DOMAIN=https://vaultwarden.localtest.me # Needs NPM setup

# Paperless-ngx Settings (uses PostgreSQL)
PAPERLESS_SECRET_KEY=a-very-secure-secret-key # Change this! Generate a random string
PAPERLESS_ADMIN_USER=admin
PAPERLESS_ADMIN_PASSWORD=a-very-secure-paperless-password # Change this!
POSTGRES_USER=paperless
POSTGRES_PASSWORD=another-secure-db-password # Change this!
POSTGRES_DB=paperless

# Immich Settings (uses its own PostgreSQL + Redis)
# Immich uses its own .env file by default, but we can integrate vars here or point to it.
# For simplicity here, we define key ones. Refer to Immich docs for all options.
IMMICH_DB_USER=immich
IMMICH_DB_PASSWORD=immich-secure-db-password # Change this!
IMMICH_DB_NAME=immich
# Define UPLOAD_LOCATION within a volume Immich controls.
# Example using a named volume:
UPLOAD_LOCATION=/usr/src/app/upload

# You might need to create an external .env file specific to Immich if compose complains
# Or add more variables directly here like:
# IMMICH_POSTGRES_USER=${IMMICH_DB_USER}
# IMMICH_POSTGRES_PASSWORD=${IMMICH_DB_PASSWORD}
# IMMICH_POSTGRES_DATABASE=${IMMICH_DB_NAME}
# IMMICH_REDIS_PASSWORD=... (optional)

# Note: Domain names like seafile.localtest.me are placeholders for NPM.
# We'll need to edit the hosts file on your computer to make these work locally.
```

**3. Create the `docker-compose.yml` file:**

YAML

```
version: '3.8'

services:
  #########################################
  #      NETWORK WIDE SERVICES            #
  #########################################
  npm:
    image: 'jc21/nginx-proxy-manager:latest'
    container_name: npm
    restart: unless-stopped
    ports:
      # Public HTTP Port:
      - '80:80'
      # Public HTTPS Port:
      - '443:443'
      # Admin Web Port:
      - '81:81'
    environment:
      # Define the DB connection within NPM's internal sqlite DB.
      # For external DB (not needed here): DB_MYSQL_HOST=npm_db etc.
      DISABLE_IPV6: 'true'
    volumes:
      - npm_data:/data
      - npm_letsencrypt:/etc/letsencrypt
    networks:
      - homelab-net

  #########################################
  #         APPLICATION SERVICES          #
  #########################################

  # --- Seafile Service ---
  seafile-db:
    image: mariadb:10.11 # Use a specific stable version
    container_name: seafile-db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
      MYSQL_LOG_CONSOLE: 'true' # Optional: for debugging
    volumes:
      - seafile_db_data:/var/lib/mysql
    networks:
      - homelab-net

  seafile:
    image: seafileltd/seafile-mc:latest
    container_name: seafile
    restart: unless-stopped
    ports:
      # Do NOT expose ports directly, access via NPM
      # - "8000:8000" # Seafile internal HTTP port (for NPM)
      # - "8082:8082" # Seafile internal file server port (for NPM setup)
      - "127.0.0.1:8000:8000" # Only expose internally for NPM
      - "127.0.0.1:8082:8082" # Only expose internally for NPM
    volumes:
      - seafile_data:/shared # Contains seafile data, ccnet, conf, logs, and mysql data IF using internal DB
    environment:
      DB_HOST: seafile-db
      DB_ROOT_PASSWD: ${DB_ROOT_PASSWORD}
      TIME_ZONE: ${TZ}
      SEAFILE_ADMIN_EMAIL: ${SEAFILE_ADMIN_EMAIL}
      SEAFILE_ADMIN_PASSWORD: ${SEAFILE_ADMIN_PASSWORD}
      SEAFILE_SERVER_LETSENCRYPT: 'false' # Let NPM handle SSL
      SEAFILE_SERVER_HOSTNAME: ${SEAFILE_SERVER_HOSTNAME}
      # Force HTTP internally as NPM handles HTTPS
      SEAFILE_SERVICE_URL: http://${SEAFILE_SERVER_HOSTNAME}:8000 
      SEAFILE_FILE_SERVER_ROOT: http://${SEAFILE_SERVER_HOSTNAME}:8082
    depends_on:
      - seafile-db
    networks:
      - homelab-net

  # --- Vaultwarden Service ---
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    volumes:
      - vaultwarden_data:/data
    environment:
      WEBSOCKET_ENABLED: 'true' # Required for browser extension B Bitwarden clients
      SIGNUPS_ALLOWED: ${VAULTWARDEN_SIGNUPS_ALLOWED}
      DOMAIN: ${VAULTWARDEN_DOMAIN} # Full HTTPS domain
      ADMIN_TOKEN: ${VAULTWARDEN_ADMIN_TOKEN} # Access admin page at /admin
      TZ: ${TZ}
      # LOG_FILE: '/data/vaultwarden.log' # Optional logging
    networks:
      - homelab-net
    # Expose port 3012 internally for websockets if needed by NPM config
    # ports:
    #   - "127.0.0.1:3012:3012"

  # --- Paperless-ngx Service ---
  paperless-redis:
    image: redis:7 # Use a specific stable version
    container_name: paperless-redis
    restart: unless-stopped
    volumes:
      - paperless_redis_data:/data
    networks:
      - homelab-net

  paperless-db:
    image: postgres:15 # Use a specific stable version matching Paperless docs
    container_name: paperless-db
    restart: unless-stopped
    volumes:
      - paperless_db_data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_DB: ${POSTGRES_DB}
    networks:
      - homelab-net

  paperless-ngx:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
    container_name: paperless-ngx
    restart: unless-stopped
    # ports: # Expose internally for NPM
    #  - "127.0.0.1:8001:8000" # Avoid conflict with Seafile's 8000
    environment:
      PAPERLESS_REDIS: redis://paperless-redis:6379
      PAPERLESS_DBENGINE: postgresql
      PAPERLESS_DBHOST: paperless-db
      PAPERLESS_DBPORT: 5432
      PAPERLESS_DBUSER: ${POSTGRES_USER}
      PAPERLESS_DBPASS: ${POSTGRES_PASSWORD}
      PAPERLESS_DBNAME: ${POSTGRES_DB}
      PAPERLESS_URL: https://paperless.localtest.me # Set your domain for NPM
      PAPERLESS_SECRET_KEY: ${PAPERLESS_SECRET_KEY}
      PAPERLESS_ADMIN_USER: ${PAPERLESS_ADMIN_USER}
      PAPERLESS_ADMIN_PASSWORD: ${PAPERLESS_ADMIN_PASSWORD}
      PAPERLESS_CONSUMPTION_DIR: /usr/src/paperless/consume # Points inside the container
      PAPERLESS_DATA_DIR: /usr/src/paperless/data
      PAPERLESS_MEDIA_ROOT: /usr/src/paperless/media
      PAPERLESS_TIME_ZONE: ${TZ}
      # Set User/Group ID (Important for permissions on volumes if using bind mounts)
      USERMAP_UID: ${PUID}
      USERMAP_GID: ${PGID}
    volumes:
      - paperless_data:/usr/src/paperless/data
      - paperless_media:/usr/src/paperless/media
      - paperless_consume:/usr/src/paperless/consume # Upload files here for processing
      - paperless_export:/usr/src/paperless/export # Optional export folder
    depends_on:
      - paperless-db
      - paperless-redis
    networks:
      - homelab-net

  # --- Immich Service ---
  # Immich requires multiple containers. We integrate them here.
  # Using separate DB and Redis for Immich for isolation.

  immich-redis:
    container_name: immich-redis
    image: redis:6.2-alpine # As recommended by Immich docs (check for updates)
    restart: unless-stopped
    networks:
      - homelab-net
    volumes:
      - immich_redis_data:/data

  immich-db:
    container_name: immich-db
    image: tensorchord/pgvecto-rs:pg14-v0.1.11 # Use image recommended by Immich! Check docs.
    restart: unless-stopped
    environment:
      POSTGRES_USER: ${IMMICH_DB_USER}
      POSTGRES_PASSWORD: ${IMMICH_DB_PASSWORD}
      POSTGRES_DB: ${IMMICH_DB_NAME}
    volumes:
      - immich_db_data:/var/lib/postgresql/data
    networks:
      - homelab-net

  immich-server:
    container_name: immich-server
    image: ghcr.io/immich-app/immich-server:${IMMICH_VERSION:-release} # Use release or specify version
    restart: unless-stopped
    # ports: # Exposed internally for NPM
    #   - "127.0.0.1:3001:3001"
    volumes:
      - immich_upload:/usr/src/app/upload # Map the UPLOAD_LOCATION var
      - /etc/localtime:/etc/localtime:ro # Optional: sync time
    environment:
      DB_HOSTNAME: immich-db
      DB_USERNAME: ${IMMICH_DB_USER}
      DB_PASSWORD: ${IMMICH_DB_PASSWORD}
      DB_DATABASE_NAME: ${IMMICH_DB_NAME}
      REDIS_HOSTNAME: immich-redis
      # Set internal URL for other Immich components
      IMMICH_SERVER_URL: http://immich-server:3001 
      # Set external URL for clients via NPM
      IMMICH_WEB_URL: https://immich.localtest.me # Your chosen domain
      UPLOAD_LOCATION: ${UPLOAD_LOCATION}
      TZ: ${TZ}
      # Add JWT_SECRET, etc., or point to an external .env if needed
      # JWT_SECRET=immich-very-secure-jwt-secret # CHANGE THIS! Add to .env file
    depends_on:
      - immich-db
      - immich-redis
    networks:
      - homelab-net

  immich-microservices:
    container_name: immich-microservices
    image: ghcr.io/immich-app/immich-server:${IMMICH_VERSION:-release} # Use same version as server
    restart: unless-stopped
    # This container runs microservices, doesn't expose ports directly
    volumes:
      - immich_upload:/usr/src/app/upload
      - /etc/localtime:/etc/localtime:ro
    environment: # Needs same DB/Redis/etc. connection info as server
      DB_HOSTNAME: immich-db
      DB_USERNAME: ${IMMICH_DB_USER}
      DB_PASSWORD: ${IMMICH_DB_PASSWORD}
      DB_DATABASE_NAME: ${IMMICH_DB_NAME}
      REDIS_HOSTNAME: immich-redis
      UPLOAD_LOCATION: ${UPLOAD_LOCATION}
      TZ: ${TZ}
      # Must match server:
      IMMICH_SERVER_URL: http://immich-server:3001 
    command: [ "start.sh", "microservices" ]
    depends_on:
      - immich-db
      - immich-redis
      - immich-server # May need server running first
    networks:
      - homelab-net

  immich-machine-learning:
    container_name: immich-machine-learning
    # Check Immich docs for correct ML image! May differ.
    image: ghcr.io/immich-app/immich-machine-learning:${IMMICH_VERSION:-release} 
    restart: unless-stopped
    volumes:
      - immich_ml_models:/cache # Persist downloaded models
      - /etc/localtime:/etc/localtime:ro
    environment: # Needs connection info
      DB_HOSTNAME: immich-db
      DB_USERNAME: ${IMMICH_DB_USER}
      DB_PASSWORD: ${IMMICH_DB_PASSWORD}
      DB_DATABASE_NAME: ${IMMICH_DB_NAME}
      REDIS_HOSTNAME: immich-redis
      # Must match server:
      IMMICH_SERVER_URL: http://immich-server:3001 
    networks:
      - homelab-net

  immich-web:
    container_name: immich-web
    image: ghcr.io/immich-app/immich-web:${IMMICH_VERSION:-release} # Use same version
    restart: unless-stopped
    environment: # Point to the server API endpoint
       IMMICH_SERVER_URL: http://immich-server:3001 
    # ports: # Exposed internally for NPM
    #   - "127.0.0.1:3000:3000" 
    networks:
        - homelab-net


#########################################
#             VOLUMES                   #
#########################################
volumes:
  npm_data:
  npm_letsencrypt:
  seafile_data:
  seafile_db_data:
  vaultwarden_data:
  paperless_data:
  paperless_media:
  paperless_consume:
  paperless_export:
  paperless_db_data:
  paperless_redis_data:
  immich_redis_data:
  immich_db_data:
  immich_upload:
  immich_ml_models:

#########################################
#             NETWORKS                  #
#########################################
networks:
  homelab-net:
    driver: bridge # Use the default bridge driver for local networking
    name: homelab-net # Explicitly name the network
```

**Explanation:**

1. **`version: '3.8'`**: Specifies the Docker Compose file version.
2. **`services`**: This is the main section defining each container we want to run.
    - **`npm` (Nginx Proxy Manager):** Our reverse proxy.
        - `image`: Specifies the Docker image to use.
        - `container_name`: Gives the container a fixed name (optional but helpful).
        - `restart: unless-stopped`: Ensures the container restarts if it crashes or Docker restarts, unless you manually stop it.
        - `ports`: This is **crucial**. It maps ports from your **host machine** (left side) to ports **inside the container** (right side). `80:80` and `443:443` map standard web traffic ports. `81:81` maps the NPM admin interface port. **Only NPM exposes ports to your host.**
        - `volumes`: `npm_data:/data` maps a named volume `npm_data` (defined at the bottom) to the `/data` directory inside the NPM container, where it stores its configuration and SQLite database. `npm_letsencrypt` stores SSL certificate data. **Named volumes** are managed by Docker and are the preferred way to persist data.
        - `networks`: Connects this service to our custom network `homelab-net`.
    - **`seafile-db` (MariaDB for Seafile):**
        - Runs the database needed by Seafile.
        - **No `ports` section:** This database is only accessible to other containers on the `homelab-net`, not directly from your host machine.
        - `environment`: Sets the root password using the variable from the `.env` file.
        - `volumes`: Persists the database data in the `seafile_db_data` named volume.
        - `networks`: Connects to `homelab-net`.
    - **`seafile`:**
        - The main Seafile application container.
        - **`ports`:** Notice these are commented out or mapped to `127.0.0.1`. This means the ports `8000` (web UI) and `8082` (file server) inside the container are _only_ reachable from the host machine itself (specifically, by the NPM container running on the same host via the Docker network), not from other machines on your LAN. We need this slight exposure so NPM can reach them, but it keeps them off your main network interface.
        - `volumes`: Persists all Seafile data using the `seafile_data` volume.
        - `environment`: Configures Seafile, telling it where its database (`seafile-db`) is, setting admin credentials, and importantly, `SEAFILE_SERVER_LETSENCRYPT: 'false'` because NPM will handle SSL/TLS termination. We also tell it its hostname (`seafile.localtest.me`) which we'll configure in NPM. Note the internal URLs use HTTP because traffic _between_ NPM and Seafile inside the Docker network doesn't need to be encrypted (though it _can_ be configured).
        - `depends_on: - seafile-db`: Tells Compose to start the `seafile-db` container _before_ starting the `seafile` container.
        - `networks`: Connects to `homelab-net`.
    - **`vaultwarden`:**
        - Similar structure: no exposed ports, uses `vaultwarden_data` volume, configured via environment variables (including `DOMAIN` for NPM), and connected to `homelab-net`. `WEBSOCKET_ENABLED` is important for client connectivity via the proxy.
    - **`paperless-ngx`, `paperless-db`, `paperless-redis`:**
        - Paperless requires a webserver, a database (PostgreSQL), and a message broker (Redis).
        - We define separate containers for `paperless-db` and `paperless-redis`, again with no exposed ports and using named volumes for persistence.
        - The main `paperless-ngx` container connects to them using their service names (`paperless-db`, `paperless-redis`) specified in the environment variables.
        - `depends_on` ensures the database and Redis start first.
        - `USERMAP_UID/GID` helps align permissions if you were using bind mounts into your user's directories; with named volumes, it's less critical but good practice.
        - The `paperless_consume` volume maps to `/usr/src/paperless/consume` inside the container. If you copy a PDF into the Docker volume location on your host (find it via `docker volume inspect paperless_consume`), Paperless will automatically pick it up and process it.
    - **Immich Services (`immich-redis`, `immich-db`, `immich-server`, `immich-microservices`, `immich-machine-learning`, `immich-web`):**
        - Immich is complex and uses a microservices architecture. We've included the core components based on their official examples.
        - Each component is a separate service in the Compose file.
        - They get their own dedicated PostgreSQL (`immich-db` using the recommended `pgvecto-rs` image for vector support) and Redis (`immich-redis`).
        - Only the `immich-server` (API) and `immich-web` (Web UI) need to be potentially reachable by NPM. Others run in the background.
        - **Important:** Check the official Immich Docker documentation for the _exact_ recommended images (especially for the DB and ML) and any required environment variables (`JWT_SECRET`, etc.) as they might change. I've added placeholders like `${IMMICH_VERSION:-release}` which you might need to replace or manage via the `.env` file.
        - Volumes are defined for database data, Redis data, ML models (`immich_ml_models`), and photo uploads (`immich_upload`).
        - They all connect to `homelab-net` and use service names for internal communication (e.g., `immich-server` talks to `immich-db`).
3. **`volumes`**: This top-level section formally defines the named volumes we referenced in the services. Docker manages the storage for these volumes.
4. **`networks`**: This defines our custom network.
    - **`homelab-net`**: We define a network named `homelab-net`.
    - `driver: bridge`: This uses the standard Docker bridge network driver, which creates a private network internal to the Docker host. Containers on the same bridge network can communicate with each other using their service names as hostnames (e.g., the `seafile` container can reach the database container at `http://seafile-db:3306`).
    - `name: homelab-net`: Assigns a predictable name to the Docker network created.

**How Services Interact (Networking Focus):**

1. **Isolation:** The `homelab-net` network is like a private virtual network switch just for these containers. They are isolated from your computer's main network interface (and the wider LAN) by default.
2. **Service Discovery:** Because all services are attached to `homelab-net`, Docker's built-in DNS resolver allows them to find each other simply by using the **service name** defined in the `docker-compose.yml`.
    - When the `seafile` service needs to talk to its database, its configuration (`DB_HOST: seafile-db`) tells it to connect to the hostname `seafile-db`. Docker DNS resolves `seafile-db` to the internal IP address of the `seafile-db` container _within_ the `homelab-net` network.
    - Similarly, `paperless-ngx` connects to `paperless-db:5432` and `redis://paperless-redis:6379`.
    - `immich-server` connects to `immich-db` and `immich-redis`.
3. **Controlled Access via Reverse Proxy (NPM):**
    - Your web browser (or other clients) on your host machine cannot directly reach `seafile`, `vaultwarden`, `paperless-ngx`, or `immich-server` because their ports are not mapped in the Docker Compose file (or are mapped only to `127.0.0.1`).
    - The _only_ entry point from your host machine is through the ports mapped by the `npm` service (80, 443, 81).
    - You will configure NPM (via its web UI on `http://localhost:81`) to act as a reverse proxy.
        - For example, you'll tell NPM: "When you receive a request for `seafile.localtest.me`, forward it to the internal address `http://seafile:8000`." (You might also need to configure forwarding for `seafile:8082` depending on Seafile's proxy setup needs).
        - And: "When you receive a request for `vaultwarden.localtest.me`, forward it to `http://vaultwarden:80`." (Vaultwarden's internal default port). You'll also need to enable WebSocket support in the NPM proxy host settings for Vaultwarden.
        - And: "When you receive a request for `paperless.localtest.me`, forward it to `http://paperless-ngx:8000`."
        - And: "When you receive a request for `immich.localtest.me`, forward it to `http://immich-web:3000` (for the web UI) or configure it to route API calls potentially to `http://immich-server:3001`" (check Immich proxy docs - usually proxying to the web container is sufficient).
    - NPM, being on the _same_ `homelab-net` network, _can_ resolve and reach the internal service names (`seafile`, `vaultwarden`, `paperless-ngx`, `immich-web`, `immich-server`).
    - **Traffic Flow:** Your Browser -> `http://seafile.localtest.me` (resolves to `127.0.0.1` via hosts file) -> Host Port 80 -> NPM Container Port 80 -> NPM Proxy Logic -> `http://seafile:8000` (internal Docker network communication) -> Seafile Container Port 8000.

**How to Use:**

1. Make sure you have Docker and Docker Compose installed.
2. Save the files (`docker-compose.yml`, `.env`) in your `homelab-test` folder.
3. **Edit your hosts file:** To make `seafile.localtest.me`, `vaultwarden.localtest.me`, etc., work on your local machine, you need to tell your computer that these names point to your local machine's loopback address (`127.0.0.1`).
    - **Linux/macOS:** Edit `/etc/hosts` (you'll need `sudo`).
    - **Windows:** Edit `C:\Windows\System32\drivers\etc\hosts` (edit as Administrator).
    - Add lines like this:
        
        ```
        127.0.0.1 seafile.localtest.me
        127.0.0.1 vaultwarden.localtest.me
        127.0.0.1 paperless.localtest.me
        127.0.0.1 immich.localtest.me
        ```
        
4. Open a terminal in the `homelab-test` folder.
5. Run `docker compose up -d`. The `-d` runs the containers in detached mode (in the background). This will pull the images and start all the containers. It might take a while the first time.
6. Wait a few minutes for everything to initialize, especially the databases and Seafile/Immich. You can check logs using `docker compose logs -f <service_name>` (e.g., `docker compose logs -f seafile`).
7. **Configure Nginx Proxy Manager:**
    - Open your browser and go to `http://localhost:81`.
    - Log in with the default credentials: Email `admin@example.com`, Password `changeme`. Change these immediately.
    - Go to `Hosts` -> `Proxy Hosts`.
    - Add a new proxy host for each service:
        - **Seafile:**
            - Domain Names: `seafile.localtest.me`
            - Scheme: `http`
            - Forward Hostname / IP: `seafile` (the service name)
            - Forward Port: `8000`
            - Enable `Websockets Support` if needed (check Seafile proxy docs).
            - Save. (You might need a second entry or custom Nginx configuration for the `fileserver` part on port 8082, depending on how Seafile expects the proxy setup. Check their documentation for reverse proxy configuration).
        - **Vaultwarden:**
            - Domain Names: `vaultwarden.localtest.me`
            - Scheme: `http`
            - Forward Hostname / IP: `vaultwarden`
            - Forward Port: `80` (Vaultwarden's internal HTTP port)
            - **Crucially:** Enable `Websockets Support`.
            - Save.
        - **Paperless-ngx:**
            - Domain Names: `paperless.localtest.me`
            - Scheme: `http`
            - Forward Hostname / IP: `paperless-ngx`
            - Forward Port: `8000`
            - Save.
        - **Immich:**
            - Domain Names: `immich.localtest.me`
            - Scheme: `http`
            - Forward Hostname / IP: `immich-web` (Proxy to the web frontend)
            - Forward Port: `3000` (Immich web's internal port)
            - Enable `Websockets Support` (likely needed).
            - Save. (Check Immich docs for specific reverse proxy recommendations).
8. **Access your services:** Now you should be able to access your services using the hostnames you set up:
    - `http://seafile.localtest.me`
    - `http://vaultwarden.localtest.me`
    - `http://paperless.localtest.me`
    - `http://immich.localtest.me`

This setup provides a good local testing environment that mimics the access control of a reverse proxy, using isolated networking for service-to-service communication. Remember to check the specific documentation for each application (especially Seafile and Immich regarding reverse proxy configurations) as there might be nuances. When you move to production, you'll replace the local hosts file entries with real DNS and place your _actual_ OPNsense firewall and VPN _in front_ of the machine running Docker.