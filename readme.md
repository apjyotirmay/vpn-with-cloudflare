# This guide will walk you through setting up Wireguard VPN, PiHole, and Cloudflared with Argo Tunnel on a server, ensuring no ports are exposed to the public internet.
This guide will walk you through setting up Wireguard VPN, PiHole, and Cloudflared with Argo Tunnel on a server, ensuring no ports are exposed to the public internet.

1. Provisioning a Server
Choose a Server: You can use a VPS provider or a home server with a static private IP address.
Operating System: Preferably Ubuntu 20.04 LTS or later.
2. Install Docker and Docker Compose
Install Docker CE:

'''bash
Copy code
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
'''
Install Docker Compose:

'''bash
Copy code
sudo apt install docker-compose
'''
3. Install Wireguard
Install Wireguard on Ubuntu 20.04:

'''bash
Copy code
sudo apt update
sudo apt install wireguard
'''
4. Setup PiHole and Cloudflared with Argo Tunnel using Docker Compose
Download Docker Compose Configuration:

'''bash
Copy code
wget https://raw.githubusercontent.com/jbencina/vpn/master/docker-compose.yaml
'''
Create Cloudflared Configuration:
Create a config.yml file inside the cloudflared directory with your Cloudflare credentials.

yaml
Copy code
tunnel: [TUNNEL_ID] # Replace [TUNNEL_ID] with your tunnel ID
credentials-file: /etc/cloudflared/[CREDENTIALS_FILE] # Replace [CREDENTIALS_FILE] with your Cloudflare credentials file path
Pull Docker Images:

'''bash
Copy code
sudo docker-compose pull
'''
Start Docker Containers:

'''bash
Copy code
sudo docker-compose up -d
'''
5. Configure Argo Tunnel
Install Cloudflared:

'''bash
Copy code
wget https://bin.equinox.io/c/VdrWdbjqyF/cloudflared-stable-linux-amd64.deb
sudo dpkg -i cloudflared-stable-linux-amd64.deb
'''
Authenticate Cloudflared:

'''bash
Copy code
sudo cloudflared tunnel login
'''
Create Argo Tunnel:

'''bash
Copy code
cloudflared tunnel create [TUNNEL_NAME]
'''
Start the Tunnel:

'''bash
Copy code
cloudflared tunnel run [TUNNEL_NAME]
'''
6. DNS Configuration
Domain Settings: Point your domain’s DNS records to Cloudflare's nameservers.
Cloudflare Dashboard: Configure DNS settings to route traffic through the Argo Tunnel.
7. Configure Wireguard Client
Generate Configuration: Create a Wireguard configuration file for your client device.
Connect to Server: Use the private IP address of your server within your LAN.
Conclusion
With this setup, your traffic will be securely tunneled through Cloudflare's network using Argo Tunnel, with Pi-hole handling DNS requests and blocking malicious content. This configuration ensures a secure and private environment without exposing ports to the public internet.

Tips:

Regularly update your Docker images and system packages.
Review Cloudflare and Wireguard documentation for advanced configurations and security enhancements.
By following this guide, you'll establish a robust and secure network solution leveraging the power of Docker, Pi-hole, Wireguard, and Cloudflare's Argo Tunnel.


