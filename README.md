
# Home Lab Lite Setup

This project provides an automated way to deploy a lightweight home lab environment using Ansible and Docker. It features modular deployment of various services without the complexity of a reverse proxy - all services are accessible directly via IP:port.

## Features

### Core Infrastructure
- **Base Network**: Isolated Docker networks for service segregation
- **Watchtower**: Automated container updates with email notifications
- **Portainer**: Docker container management UI (https://<ip>:9443)

### Remote Access
- **WireGuard**: VPN server with web UI (http://<ip>:51821)
- **No-IP**: Dynamic DNS client for remote access
- **PuTTY**: Web-based SSH client (http://<ip>:5800)

### Dashboards
- **Homepage**: Customizable service dashboard (http://<ip>:3000)
- **Glances**: System monitoring (http://<ip>:61208)

## Prerequisites

1. Ubuntu/Debian-based system with:
   - Ansible installed and configured
   - Docker installed and configured for your user
2. SSH access with sudo privileges
3. Git installed

## Quick Start

1. **Configure Deployment**:
   ```bash
   # Setup secrets (required)
   cp group_vars/vault.yml.template group_vars/vault.yml
   # Edit vault.yml with your credentials
   ansible-vault encrypt group_vars/vault.yml
   
   # Configure deployment (optional)
   nano group_vars/main.yml  # Enable/disable services and configure paths
   nano inventory/hosts      # Configure target hosts

   # If you haven't already, ensure Docker is running and your user has permissions
   sudo systemctl start docker
   sudo usermod -aG docker $USER
   ```

2. **Deploy**:
   ```bash
   ansible-playbook deploy_home_lab_lite_playbook.yml --ask-vault-pass --ask-become-pass
   ```

## Configuration Files

- `group_vars/main.yml`: Main configuration file
  - Service enable/disable flags
  - Network settings
  - Data paths and domains
  - Resource limits

- `group_vars/vault.yml`: Encrypted secrets (generated from template)
  - Credentials and API keys
  - Email settings
  - Device names and tokens

## Service Management

### Email Notifications
Watchtower sends email notifications for container updates. Configure in `vault.yml`:
- SMTP server settings
- From/To addresses
- Update schedule (default: 5 AM daily)

## Troubleshooting

1. **Vault Access**:
   ```bash
   ansible-vault edit group_vars/vault.yml    # Edit encrypted file
   ansible-vault decrypt group_vars/vault.yml  # Decrypt (careful!)
   ansible-vault encrypt group_vars/vault.yml  # Re-encrypt
   ```

2. **Service Status**: Use Portainer (https://<ip>:9443) or Glances (http://<ip>:61208)

## Adding New Services

1. Create new role in `roles/`:
   ```
   roles/my_service/
   ├── tasks/
   │   └── main.yml
   └── templates/
       └── my_service.yml.j2
   ```

2. Add role to `deploy_home_lab_lite_playbook.yml`
3. Add configuration to `group_vars/main.yml`
4. Add secrets to `group_vars/vault.yml.template`

## Contributing

1. Fork the repository
2. Create feature branch
3. Follow existing patterns for:
   - Role structure
   - Resource limits
   - Network isolation
   - Secret management
4. Submit pull request

## License

This project is licensed under the MIT License.