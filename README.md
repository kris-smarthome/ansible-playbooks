# ansible-playbooks

Small, focused Ansible playbooks and roles for common host tasks on Debian/Ubuntu:
- Update packages and reboot if required
- Install Docker CE, create a `traefik` Docker network, and deploy Traefik
- Mount NFS shares for home/apps and media

## Requirements
- Ansible installed locally and SSH access to targets
- Privileged access on targets (become/sudo)
- Python on targets (standard on Debian/Ubuntu)
- Collections: `community.docker`, `ansible.posix`
  - Install: `ansible-galaxy collection install community.docker ansible.posix`

## Inventory
Define hosts and groups in `inventory.yml`.

## Usage
- Update and reboot if needed: `ansible-playbook -i inventory.yml playbooks/update_hosts.yml`
- Install Docker and deploy Traefik: `ansible-playbook -i inventory.yml playbooks/docker_install/docker_install.yml`
  - Configure Traefik variables in `playbooks/docker_install/roles/traefik/vars/main.yml`
    - `acme_email`, `traefik_acme_domain`, `cloudflare_token`
  - Uses Let's Encrypt DNS challenge via Cloudflare and exposes ports 80/443 (and 8080 for dashboard if enabled)
- Mount shares: configure variables under
  `playbooks/mount_shares/roles/mount_home/vars/main.yml` and
  `playbooks/mount_shares/roles/mount_media/vars/main.yml`, then run:
  `ansible-playbook -i inventory.yml playbooks/mount_shares/mount_shares.yml`

### Traefik Vars Example
`playbooks/docker_install/roles/traefik/vars/main.yml`:

```yaml
acme_email: you@example.com
traefik_acme_domain: traefik.example.com
cloudflare_token: <cloudflare_api_token>
```

### Traefik Dashboard and Ports
- HTTP/HTTPS: `:80` and `:443`
- Dashboard: `:8080` (enabled by `--api.dashboard=true` and port mapping)
- To disable the dashboard, comment out the `--api.dashboard=true` flag and the `8080:8080` port in `playbooks/docker_install/roles/traefik/tasks/main.yml`.

## Structure
- `inventory.yml` — example inventory
- `playbooks/update_hosts.yml` — apt update/upgrade + conditional reboot
- `playbooks/docker_install/` — Docker install and Traefik (roles: `install`, `traefik_network`, `traefik`)
- `playbooks/mount_shares/` — roles to mount NFS shares (home, media)

Tested on Debian-based systems; adapt as needed for your environment.
