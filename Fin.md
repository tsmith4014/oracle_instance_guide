# OCI Instance Setup, Nginx Configuration, and Automation Guide with Monty Hall Project Deployment

## Table of Contents

1. [Instance Creation and Initial Setup](#instance-creation-and-initial-setup)
2. [Installing and Configuring Nginx](#installing-and-configuring-nginx)
3. [Firewall Configuration on the Instance](#firewall-configuration-on-the-instance)
4. [OCI Network Security Settings](#oci-network-security-settings)
5. [Automating Instance Configuration with Terraform](#automating-instance-configuration-with-terraform)
6. [Monty Hall Project Deployment Guide](#monty-hall-project-deployment-guide)
7. [Managing Gunicorn with Systemd](#managing-gunicorn-with-systemd)
8. [Troubleshooting](#troubleshooting)
9. [Useful Commands](#useful-commands)
10. [Connecting to Oracle Autonomous Database](#connecting-to-oracle-autonomous-database)

## Instance Creation and Initial Setup

- Launch an OCI instance with a preferred OS.
- Ensure the instance is associated with a VCN and has a public IP address.

## Installing and Configuring Nginx

1. **Install Nginx**:

   - Ubuntu/Debian: `sudo apt install nginx`
   - CentOS/RHEL: `sudo yum install nginx`

2. **Test, Start, and Enable Nginx**:

   ```bash
   sudo nginx -t
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```

3. **Verify Nginx Status**:

   ```bash
   sudo systemctl status nginx
   ```

4. **Test Nginx Configuration**:
   After making any changes to the Nginx configuration:

   ```bash
   sudo nginx -t
   ```

5. **nginx config file location**:

   ```bash
   sudo find / -type f -name "nginx.conf"
   ```

6. **new niginx config file**:

   ```bash
   server {
      listen 80;
      server_name 150.136.137.6;  # Your server's public IP

      # Location block for the React app (Vite build output)
      location / {
         root /home/opc/monty_hall_project/frontend/front/dist;  # Path to Vite's dist directory
         try_files $uri /index.html;
      }

      # Location block for the Django app
      location /api {
         proxy_pass http://127.0.0.1:8000;  # Assuming Gunicorn on port 8000
         proxy_set_header Host $host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header X-Forwarded-Proto $scheme;
      }
   }
   ```

## Firewall Configuration on the Instance

1. **Managing `firewalld`**:

   - Check Status: `sudo systemctl status firewalld`
   - Open HTTP Port (80):
     ```bash
     sudo firewall-cmd --zone=public --add-service=http --permanent
     sudo firewall-cmd --reload
     ```
   - List Current Rules: `sudo firewall-cmd --list-all`

2. **Checking and Changing Firewall Settings**:
   - Check the status of the firewall: `sudo firewall-cmd --state`
   - List all current firewall rules: `sudo firewall-cmd --list-all`
   - Open port 8000: `sudo firewall-cmd --zone=public --add-port=8000/tcp --permanent` and `sudo firewall-cmd --reload`

## OCI Network Security Settings

- Configure Security Lists or NSGs in OCI to allow inbound traffic on port 80 (HTTP).

## Automating Instance Configuration with Terraform

1. **Terraform Setup**:

   - Install Terraform and set up your OCI provider.

2. **Define Infrastructure as Code**:

   - Create Terraform configuration files defining your instance, VCN, subnet, and security rules.
   - Example to open HTTP port:

     ```hcl
     resource "oci_core_security_list" "example_sl" {
       compartment_id = var.compartment_id
       vcn_id         = oci_core_vcn.example_vcn.id

       egress_security_rules {
         protocol = "all"
         destination = "0.0.0.0/0"
       }

       ingress_security_rules {
         protocol = "6"  # TCP
         source   = "0.0.0.0/0"
         tcp_options {
           "min" = 80
           "max" = 80
         }
       }
     }
     ```

## Monty Hall Project Deployment Guide

### Initial Setup

1. **Directory Structure**:
   - Ensure proper structure, including backend Django app, Gunicorn config, and Ngin

x config files.

2. **Nginx Configuration**:
   - Set up to serve React frontend and proxy Django backend requests.

### Running Gunicorn Manually

1. **Start Gunicorn**:
   - Navigate to Django directory and start Gunicorn with the specified number of workers and bind it to port 8000.

## Managing Gunicorn with Systemd

1. **Stopping Manual Gunicorn Processes**: Find PIDs using `sudo lsof -i :8000` and kill them.
2. **Edit the Gunicorn Service File**: Specify the working directory and command to start Gunicorn.
3. **Reload and Restart the Service**: Reload the systemd daemon and restart the service.
4. **Check Service Status**: Ensure it's running without errors.

## Troubleshooting

- General troubleshooting steps for local and OCI network configurations, Nginx, and firewall.
- SELinux configuration and service failure solutions.
- Dependency installation issues.

## Useful Commands

- **Nginx Status**: `sudo systemctl status nginx`
- **Restart Nginx**: `sudo systemctl restart nginx`
- **Firewall Status** (`firewalld`): `sudo systemctl status firewalld`
- **Open Port 80 on `firewalld`**:
  ```bash
  sudo firewall-cmd --zone=public --add-service=http --permanent
  sudo firewall-cmd --reload
  ```
- **Start the Application with Gunicorn**: `/home/opc/venv/bin/gunicorn -w 4 -b 0.0.0.0:8000 app:app`
- **Start the Application with Systemd**: `sudo systemctl start expense-report-webapp.service`

## Connecting to Oracle Autonomous Database

- **Installation and Setup**: Steps for installing SQL\*Plus, configuring the wallet, and setting environment variables.
- **Connect Using SQL\*Plus**: Command to establish a database connection.

---

This README provides a comprehensive, step-by-step guide for setting up and configuring an OCI instance for the Monty Hall Project, including detailed instructions for Nginx, firewall, and Gunicorn, as well as troubleshooting tips and useful commands. For additional details on advanced configurations and setups, refer to the official documentation of the respective tools and services.
