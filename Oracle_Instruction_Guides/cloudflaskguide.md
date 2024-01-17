# Oracle Cloud Flask Application Deployment Guide

This README documents the steps taken to deploy a Flask application on an Oracle Cloud instance, along with troubleshooting steps for common issues.

## Initial Setup and Deployment

### 1. Create Virtual Cloud Network (VCN) and Subnet

- Create a VCN and subnet using Terraform scripts.

### 2. Launch an Oracle Cloud Instance

- Use Terraform to launch an instance with the required configurations.

### 3. Connect to the Instance

- SSH into the instance using the provided SSH key.

### 4. Install Dependencies and Set Up the Application

- Update the package manager: `sudo yum update -y`
- Install Git and Python 3: `sudo yum install git python38 -y`
- Clone the repository: `git clone <repo_url>`
- Navigate to the application directory: `cd path/to/app`
- Create a Python virtual environment: `python3 -m venv venv`
- Activate the virtual environment: `source venv/bin/activate`
- Install Python dependencies: `pip install -r requirements.txt`

### 5. Configure Gunicorn to Serve the Application

- Create a Gunicorn config file.
- Create a systemd service file for Gunicorn.
- Start and enable the Gunicorn service.

## Firewall and Security List Configuration

- Configure the firewall to allow traffic on port 8000: `sudo firewall-cmd --zone=public --add-port=8000/tcp --permanent`
- Reload the firewall: `sudo firewall-cmd --reload`
- Add rules to the security list for SSH (port 22), HTTP (port 80), and your application port (e.g., 8000).

## Troubleshooting

### Checking Logs

- Check cloud-init logs: `sudo less /var/log/cloud-init.log`
- Check cloud-init output logs: `sudo less /var/log/cloud-init-output.log`
- Check systemd service logs: `sudo journalctl -u your-service-name.service`

### Common Issues and Solutions

- **Service Fails to Start**: Check the ExecStart path in the systemd service file. Ensure the Gunicorn executable path is correct.
- **Firewall Issues**: Ensure that the required ports are open in both the firewall and the Oracle Cloud security list.
- **Dependency Installation Issues**: If certain Python packages fail to install, check for compatibility issues or missing dependencies (like development headers).

## Updating the Application

### Pulling Changes from Git Repository

- Navigate to the application directory: `cd path/to/app`
- Pull the latest changes: `git pull origin main` (replace `main` with your branch name if different)

### Restart the Application Service

- Restart the Gunicorn service to apply the changes: `sudo systemctl restart your-service-name.service`

---

**Note**: Replace placeholder text like `<repo_url>` and `your-service-name.service` with your specific repository URL and service name.
