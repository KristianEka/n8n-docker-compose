# N8N Docker Compose Setup

N8N is a powerful workflow automation tool. This setup provides an N8N installation using Docker Compose with customizable configuration.

## Requirements

- Docker
- Docker Compose
- Domain or subdomain to be used for external access

## Installation

### 1. Clone or copy the files

Copy the `docker-compose.yml` and `.env` files to your project directory.

### 2. Configure the .env file

The `.env` file contains various configurable settings. Below is an explanation of each variable:

#### Timezone

- `GENERIC_TIMEZONE`: Default system timezone (example: `Asia/Jakarta`)
- `TZ`: Timezone for Docker containers (should be the same as GENERIC_TIMEZONE)

#### n8n Configuration

- `WEBHOOK_URL`: Public URL for n8n (example: `https://n8n.yourdomain.com`)
- `N8N_HOST`: Hostname used by n8n (should be the same as your domain)
- `N8N_PROTOCOL`: Protocol used by n8n (`http` or `https`)
- `N8N_PORT`: Port used by n8n (default: `5678`)

#### Security & Features

- `N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS`: Enforce configuration file permissions (true/false)
- `N8N_RUNNERS_ENABLED`: Enable runner features (true/false)
- `N8N_PROXY_HOPS`: Number of proxies to trust (number)
- `N8N_DIAGNOSTICS_ENABLED`: Enable diagnostic reporting (true/false)
- `N8N_VERSION_NOTIFICATIONS_ENABLED`: Show new version notifications (true/false)
- `N8N_TEMPLATES_ENABLED`: Enable workflow templates (true/false)

#### Database Configuration

- `DB_SQLITE_POOL_SIZE`: Database connection pool size for SQLite (number)
- `N8N_BLOCK_ENV_ACCESS_IN_NODE`: Block environment variable access in nodes (true/false)
- `N8N_GIT_NODE_DISABLE_BARE_REPOS`: Disable bare Git repositories (true/false)

#### Data Folders

- `DATA_FOLDER`: Location of persistent data folder for n8n (example: `/home/user/n8n_data`)

### 3. Running N8N

After configuring the `.env` file, run the following command:

```bash
docker-compose up -d
```

This command will start N8N in detached mode (background).

### 4. Accessing the Application

After the containers are running, you can access n8n through the URL you configured in `WEBHOOK_URL`.

## Additional Configuration

### Reverse Proxy (Optional)

If you're using a reverse proxy like Nginx, make sure to configure the correct headers so that n8n functions properly:

```
proxy_set_header Connection "";
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header Host $host;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
```

### Data Backup

To backup n8n data, backup the folder you specified in `DATA_FOLDER`.

## Troubleshooting

### Webhook Access

Ensure that the webhook URL is accessible from the internet if you plan to use webhook triggers.

### Port Usage

Make sure the port you specify in `N8N_PORT` is not being used by other applications.

### File Permissions

If you experience file permission issues, ensure that the Docker user has access to the data folder specified in `DATA_FOLDER`.

## Updating N8N

To update your N8N installation to the latest version, follow these steps:

1. Pull the latest image:
   ```bash
   docker-compose pull
   ```

2. Stop the running containers:
   ```bash
   docker-compose down
   ```

3. Start the containers with the updated image:
   ```bash
   docker-compose up -d
   ```

## License

See the official n8n documentation for more information about licensing and usage.
