# N8N Docker Compose Setup with Cloudflare Tunnel

N8N is a powerful workflow automation tool. This setup provides an N8N installation using Docker Compose with Cloudflare Tunnel for secure external access, without requiring port forwarding or reverse proxy configuration.

## Requirements

- Docker
- Docker Compose
- Cloudflare account (free tier is sufficient)
- Domain registered with Cloudflare

## Installation

### 1. Clone or copy the files

Copy the `docker-compose.yml` and `.env` files to your project directory.

### 2. Set up Cloudflare Tunnel

#### Create a Cloudflare Tunnel

1. Log in to your [Cloudflare Dashboard](https://dash.cloudflare.com/)
2. Navigate to **Zero Trust** > **Access** > **Tunnels**
3. Click **Create a tunnel**
4. Choose **Cloudflared** as the connector type
5. Give your tunnel a name (e.g., `n8n-tunnel`)
6. Click **Save tunnel**

#### Get the Tunnel Token

After creating the tunnel, Cloudflare will provide you with a token. Copy this token for use in your `.env` file.

Alternatively, if you have `cloudflared` CLI installed, you can get the token with:
```bash
cloudflared tunnel token <tunnel-name>
```

#### Configure Public Hostname

1. In the tunnel configuration, add a **Public Hostname**:
   - **Subdomain**: `n8n` (or your preferred subdomain)
   - **Domain**: Select your domain from the dropdown
   - **Service**: `http://n8n:5678`
2. Click **Save tunnel**

### 3. Configure the .env file

The `.env` file contains various configurable settings. Below is an explanation of each variable:

#### Timezone

- `GENERIC_TIMEZONE`: Default system timezone (example: `Asia/Jakarta`)
- `TZ`: Timezone for Docker containers (should be the same as GENERIC_TIMEZONE)

#### N8N Configuration

- `WEBHOOK_URL`: Public URL for n8n (example: `https://n8n.yourdomain.com`)
- `N8N_HOST`: Hostname used by n8n (should match your Cloudflare tunnel subdomain)
- `N8N_PROTOCOL`: Protocol used by n8n (`http` or `https` - use `https` for Cloudflare Tunnel)
- `N8N_PORT`: Port used by n8n (default: `5678`)

#### Security & Features

- `N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS`: Enforce configuration file permissions (true/false)
- `N8N_RUNNERS_ENABLED`: Enable runner features (true/false)
- `N8N_PROXY_HOPS`: Number of proxies to trust (set to `1` for Cloudflare Tunnel)
- `N8N_DIAGNOSTICS_ENABLED`: Enable diagnostic reporting (true/false)
- `N8N_VERSION_NOTIFICATIONS_ENABLED`: Show new version notifications (true/false)
- `N8N_TEMPLATES_ENABLED`: Enable workflow templates (true/false)

#### Database Configuration

- `DB_SQLITE_POOL_SIZE`: Database connection pool size for SQLite (number)
- `N8N_BLOCK_ENV_ACCESS_IN_NODE`: Block environment variable access in nodes (true/false)
- `N8N_GIT_NODE_DISABLE_BARE_REPOS`: Disable bare Git repositories (true/false)

#### Data Folders

- `DATA_FOLDER`: Location of persistent data folder for n8n
  - **Windows**: `C:/Development/n8n_data`
  - **Linux/Mac**: `./n8n_data` or `/path/to/n8n_data`
  - **Docker volume**: `n8n_data`

#### Cloudflare Tunnel Configuration

- `CLOUDFLARED_TOKEN`: Your Cloudflare Tunnel token (obtained from step 2)

### 4. Running N8N

After configuring the `.env` file, run the following command:
```bash
docker compose up -d
```

This command will start N8N and Cloudflare Tunnel in detached mode (background).

### 5. Accessing the Application

After the containers are running, you can access n8n through the URL you configured in Cloudflare (e.g., `https://n8n.yourdomain.com`).

**Note**: It may take a few moments for the Cloudflare Tunnel to establish the connection. Check the tunnel status in your Cloudflare Dashboard.

## Architecture

This setup includes two main services:

1. **N8N**: The workflow automation platform
2. **Cloudflare Tunnel**: Secure tunnel that exposes N8N to the internet without port forwarding
```
Internet → Cloudflare Network → Cloudflare Tunnel → N8N Container
```

### Benefits of Cloudflare Tunnel

- **No port forwarding required**: Your firewall stays closed
- **Automatic HTTPS**: SSL/TLS handled by Cloudflare
- **DDoS protection**: Built-in Cloudflare security
- **No reverse proxy needed**: Cloudflare handles all routing
- **Works behind NAT**: Perfect for home servers

## Additional Configuration

### Data Backup

To backup n8n data, backup the folder you specified in `DATA_FOLDER`:
```bash
# Example backup command
tar -czf n8n-backup-$(date +%Y%m%d).tar.gz /path/to/DATA_FOLDER
```

### Custom Domain Configuration

If you want to use a different domain or subdomain:

1. Update the Public Hostname in your Cloudflare Tunnel dashboard
2. Update `WEBHOOK_URL` and `N8N_HOST` in your `.env` file
3. Restart the containers: `docker compose restart`

## Troubleshooting

### Tunnel Connection Issues

Check the Cloudflare Tunnel logs:
```bash
docker compose logs cloudflared
```

Verify the tunnel status in your Cloudflare Dashboard under **Zero Trust** > **Access** > **Tunnels**.

### Webhook Access

Ensure that:
- Your `WEBHOOK_URL` matches your Cloudflare public hostname
- The tunnel is showing as "Healthy" in the Cloudflare Dashboard
- `N8N_PROTOCOL` is set to `https`

### Port Usage

The internal port `5678` is only used within the Docker network. There's no need to expose it externally since Cloudflare Tunnel handles all external access.

### File Permissions

If you experience file permission issues, ensure that the Docker user has access to the data folder specified in `DATA_FOLDER`:
```bash
# Linux/Mac
chmod -R 755 /path/to/n8n_data
chown -R $USER:$USER /path/to/n8n_data
```

### Windows Path Issues

If you're on Windows and experience path issues, ensure you're using forward slashes in `DATA_FOLDER`:
- ✅ Correct: `C:/Development/n8n_data`
- ❌ Incorrect: `C:\Development\n8n_data`

## Monitoring

### Check Container Status
```bash
docker compose ps
```

### View Logs
```bash
# All services
docker compose logs -f

# N8N only
docker compose logs -f n8n

# Cloudflare Tunnel only
docker compose logs -f cloudflared
```

## Updating N8N

To update your N8N installation to the latest version:

1. Pull the latest images:
```bash
docker compose pull
```

2. Stop the running containers:
```bash
docker compose down
```

3. Start the containers with the updated images:
```bash
docker compose up -d
```

## Security Recommendations

1. **Enable authentication**: Set up user authentication in N8N (first login)
2. **Cloudflare Access**: Consider adding Cloudflare Access policies for additional security
3. **Regular backups**: Implement automated backups of your `DATA_FOLDER`
4. **Keep updated**: Regularly update N8N and Cloudflare Tunnel to the latest versions
5. **Monitor logs**: Regularly check logs for unusual activity

## Useful Links

- [N8N Documentation](https://docs.n8n.io/)
- [Cloudflare Tunnel Documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

## License

See the official n8n documentation for more information about licensing and usage.

## Support

For issues related to:
- **N8N**: Check the [N8N community forum](https://community.n8n.io/)
- **Cloudflare Tunnel**: Check [Cloudflare Community](https://community.cloudflare.com/)
- **This setup**: Open an issue in this repository
