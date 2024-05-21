# Installing Wireguard, PiHole, Cloudflared VPN
Certainly! Here's a comprehensive guide on setting up Wireguard VPN, PiHole, and Cloudflared with Argo Tunnel, without exposing ports to the public internet:

### 1. Provisioning a Server:

Choose a server provider or set up a server at home with a static private IP address.

### 2. Docker Install:

Install Docker CE and Docker Compose on your server.

#### Install Docker CE:
```bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
```

#### Install Docker Compose:
```bash
sudo apt install docker-compose
```

### 3. Wireguard Install:

Install Wireguard on your server.

#### Install Wireguard:
Follow the instructions for your specific operating system. For Ubuntu 20.04:
```bash
sudo apt update
sudo apt install wireguard
```

### 4. PiHole / Cloudflared with Argo Tunnel:

Set up PiHole and Cloudflared with Argo Tunnel using Docker Compose.

#### Download Docker Compose Configuration:
```bash
wget https://raw.githubusercontent.com/jbencina/vpn/master/docker-compose.yaml
```

#### Modify Docker Compose Configuration:
Edit the `docker-compose.yaml` file to include Cloudflared with Argo Tunnel configuration. Replace `[ARGO_TUNNEL_NAME]` with your desired tunnel name.

```yaml
version: '3'

services:
  pihole:
    image: pihole/pihole:latest
    container_name: pihole
    restart: unless-stopped
    environment:
      TZ: 'America/New_York' # Modify time zone if needed
      WEBPASSWORD: 'your_password' # Set your desired web password
    ports:
      - "80:80"
      - "443:443"
    dns:
      - 127.0.0.1
      - 1.1.1.1
    volumes:
      - './pihole/pihole:/etc/pihole/'
      - './pihole/dnsmasq.d:/etc/dnsmasq.d/'
    cap_add:
      - NET_ADMIN
    networks:
      - vpn

  cloudflared:
    image: cloudflare/cloudflared:latest
    container_name: cloudflared
    restart: unless-stopped
    command: tunnel --config /etc/cloudflared/config.yml run [ARGO_TUNNEL_NAME] # Replace [ARGO_TUNNEL_NAME] with your tunnel name
    networks:
      - vpn
    volumes:
      - './cloudflared:/etc/cloudflared'

networks:
  vpn:
    driver: bridge
```

#### Create Cloudflared Configuration:
Create a configuration file named `config.yml` inside the `cloudflared` directory with your Cloudflare credentials:
```yaml
tunnel: [TUNNEL_ID] # Replace [TUNNEL_ID] with your tunnel ID
credentials-file: /etc/cloudflared/[CREDENTIALS_FILE] # Replace [CREDENTIALS_FILE] with your Cloudflare credentials file path
```

#### Pull Docker Images:
```bash
sudo docker-compose pull
```

#### Start Docker Containers:
```bash
sudo docker-compose up -d
```

### 5. Configure Argo Tunnel:

Install Cloudflared on your server and authenticate it with your Cloudflare account.

#### Install Cloudflared:
```bash
wget https://bin.equinox.io/c/VdrWdbjqyF/cloudflared-stable-linux-amd64.deb
sudo dpkg -i cloudflared-stable-linux-amd64.deb
```

#### Authenticate Cloudflared:
```bash
sudo cloudflared tunnel login
```

#### Create Argo Tunnel:
```bash
cloudflared tunnel create [TUNNEL_NAME] # Replace [TUNNEL_NAME] with your desired tunnel name
```

#### Start the Tunnel:
```bash
cloudflared tunnel run [TUNNEL_NAME] # Replace [TUNNEL_NAME] with your tunnel name
```

### 6. DNS Configuration:

Point your domain's DNS records to Cloudflare's nameservers and configure DNS settings.

### 7. Client Configuration:

Configure Wireguard on your client device to connect to the private IP address of your server.

### Conclusion:

With this setup, all traffic between your client device and the server will be securely tunneled through Cloudflare's network using Argo Tunnel. PiHole will intercept DNS requests and block malicious content, while Cloudflared with Argo Tunnel will handle DNS resolution securely.

Remember to follow Cloudflare's documentation for configuring Argo Tunnel and DNS settings to ensure proper functionality and security.

This approach provides a secure and private setup without exposing any ports to the public internet.
