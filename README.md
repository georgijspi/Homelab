# Homelab
My setup and docker-compose files for my homelab.

## Proxmox Ubuntu VM:
| Component           | Details                                                                                              |
|---------------------|------------------------------------------------------------------------------------------------------|
| Memory              | 32.00 GiB                                                                                            |
| Processors          | 8 (1 sockets, 8 cores) [x86-64-v2-AES]                                                              |
| BIOS                | OVMF (UEFI)                                                                                          |
| Display             | Default                                                                                              |
| Machine             | q35                                                                                                  |
| SCSI Controller     | VirtIO SCSI single                                                                                   |
| CD/DVD Drive (ide2)| local:iso/ubuntu-22.04.4-live-server-amd64.iso,media=cdrom,size=2055086K                               |
| Hard Disk (scsi0)  | local-lvm:vm-101-disk-1,iothread=1,size=100G                                                         |
| Network Device (net0)| virtio=BC:24:11:83:3E:0E, bridge=vmbr0, firewall=1                                                    |
| EFI Disk            | local-lvm:vm-101-disk-0,efitype=4m,pre-enrolled-keys=1,size=4M                                        |

| Configuration           | Details                                                                                              |
|-------------------------|------------------------------------------------------------------------------------------------------|
| Name                    | BattleBlimp                                                                                          |
| Start at boot           | Yes                                                                                                  |
| Start/Shutdown order    | order=any                                                                                            |
| OS Type                 | Linux 6.x - 2.6 Kernel                                                                               |
| Boot Order              | scsio, ide2, neto                                                                                    |
| Use tablet for pointer  | Yes                                                                                                  |
| Hotplug                 | Disk, Network, USB                                                                                   |
| ACPI support            | Yes                                                                                                  |
| KVM hardware virtualization | Yes                                                                                               |
| Freeze CPU at startup   | No                                                                                                   |
| Use local time for RTC  | Default (Enabled for Windows)                                                                        |
| RTC start date          | now                                                                                                  |
| SMBIOS settings (type1) | uuid=c688948c-8b1b-44f4-8467-ffc959d024e1                                                           |
| QEMU Guest Agent        | Enabled                                                                                              |
| Protection              | No                                                                                                   |
| Spice Enhancements      | none                                                                                                 |
| VM State storage        | Automatic                                                                                            |


---

## Docker Applications Overview

This repository contains Docker configurations for several applications. Each application is organized within its own directory along with its corresponding `docker-compose.yml` file.

### Applications:

#### [Ghost](./docker/ghost)
- **Description:** Ghost is a popular open-source headless Node.js CMS for professional publishing.
- **Documentation:** [Ghost Documentation](https://ghost.org/docs/)

#### [Minecraft Server](./docker/minecraftserver)
- **Description:** Minecraft Server is a sandbox video game where players can build and explore virtual worlds.
- **Documentation:** [Minecraft Server Documentation](https://minecraft.gamepedia.com/Server)

#### [Pi-hole](./docker/pihole)
- **Description:** Pi-hole is a network-wide ad blocker that improves your internet experience.
- **Documentation:** [Pi-hole Documentation](https://docs.pi-hole.net/)

#### [Portainer](./docker/portainer)
- **Description:** Portainer is an open-source tool for managing Docker containers, images, volumes, networks, and more.
- **Documentation:** [Portainer Documentation](https://documentation.portainer.io/)

#### [Traefik](./docker/traefik)
- **Description:** Traefik is a modern reverse proxy and load balancer for HTTP and TCP-based services.
- **Documentation:** [Traefik Documentation](https://doc.traefik.io/traefik/)

### Additional Configuration Files:

#### [Traefik](./docker/traefik)
- **acme.json:** Traefik's ACME certificate storage file.
- **traefik.yml:** Traefik configuration file.

---

### Traefik Setup Overview:

Traefik is configured as a reverse proxy and load balancer to manage traffic for multiple services running on your server. It handles requests and routes them to the appropriate containers based on defined rules.

#### Components:
- **Traefik Docker Container:** Traefik runs as a Docker container, exposing ports for HTTP and HTTPS traffic.
- **traefik.yml Configuration:** Configuration file defining Traefik's settings, including entry points, routers, and middleware.
- **acme.json:** File storing SSL certificates obtained from Let's Encrypt for HTTPS encryption.

#### Configuration Steps:
1. **Docker Compose Configuration:**
    - The `docker-compose.yml` file defines the Traefik service, specifying the image, ports, and volumes.
    - It also mounts the `traefik.yml` configuration file and `acme.json` SSL certificate storage file.

2. **Traefik Configuration (`traefik.yml`):**
    - Defines entry points for HTTP and HTTPS traffic.
    - Configures routers and middlewares to route requests to appropriate services.
    - Sets up Let's Encrypt ACME provider for automatic SSL certificate provisioning.

3. **DNS Configuration:**
    - DNS records are configured to point to the Traefik server's IP address (`home.georgijs.dev`).

4. **Cloudflare Setup:**
    - Configure Cloudflare to manage DNS records for your domain.
    - Set up Cloudflare nameservers to point to Cloudflare's DNS servers.

---

### DNS Records Configuration:

#### Cloudflare DNS Records:

| Type              | Status    | Name                  | Value               | 
|-------------------|-----------|-----------------------|---------------------|
| A (Proxied)       | auto TTL  | home.georgijs.dev     | 188.141.56.199      |
| A (DNS only)      | auto TTL  | local.georgijs.dev    | 192.168.0.59        |
| CNAME (DNS only)  | auto TTL  | blog.georgijs.dev     | georgijs.dev        |
| CNAME (DNS only)  | auto TTL  | chatsql.georgijs.dev  | georgijs.dev        |
| CNAME (Proxied)   | auto TTL  | georgijs.dev          | home.georgijs.dev   |
| CNAME (DNS only)  | auto TTL  | *.local.georgijs.dev  | local.georgijs.dev  |
| CNAME (Proxied)   | auto TTL  | www.georgijs.dev      | pixie.porkbun.com   |

#### Porkbun URL Purchase:
- Purchase the domain `georgijs.dev` from Porkbun.
- Set up CNAME records for `www.georgijs.dev` to point to `pixie.porkbun.com`.

---

### Setting Up Nameservers on Cloudflare:
1. **Sign Up/Login to Cloudflare:**
    - Go to the Cloudflare website and sign up for an account or log in if you already have one.

2. **Add a Site to Cloudflare:**
    - Click on "Add a Site" and enter your domain (`georgijs.dev`).
    - Follow the prompts to scan your DNS records.

3. **Change Nameservers:**
    - Once the scan is complete, Cloudflare will provide you with nameservers.
    - Go to your domain registrar (Porkbun) and update the nameservers to the ones provided by Cloudflare.

4. **Verify Nameserver Update:**
    - After updating the nameservers, it may take some time (up to 48 hours) for the changes to propagate globally.
    - You can verify the nameserver update using online DNS lookup tools.

5. **DNS Configuration in Cloudflare:**
    - Once the nameservers are updated and propagated, manage your DNS records within the Cloudflare dashboard as needed.

---

This setup ensures that traffic to your domain is routed through Traefik for efficient handling and management of your services. DNS records are configured to point to your server's IP address and Cloudflare is utilized for DNS management and nameserver configuration.