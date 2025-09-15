# ansible-playbooks

Small, focused Ansible playbooks and roles for common host tasks on Debian/Ubuntu:
- Update packages and reboot if required
- Install Docker CE and create a `traefik` Docker network
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
- Install Docker and create network: `ansible-playbook -i inventory.yml playbooks/docker_install/docker_install.yml`
- Mount shares: configure variables under
  `playbooks/mount_shares/roles/mount_home/vars/main.yml` and
  `playbooks/mount_shares/roles/mount_media/vars/main.yml`, then run:
  `ansible-playbook -i inventory.yml playbooks/mount_shares/mount_shares.yml`

## Structure
- `inventory.yml` — example inventory
- `playbooks/update_hosts.yml` — apt update/upgrade + conditional reboot
- `playbooks/docker_install/` — Docker install role and Traefik network
- `playbooks/mount_shares/` — roles to mount NFS shares (home, media)

Tested on Debian-based systems; adapt as needed for your environment.
