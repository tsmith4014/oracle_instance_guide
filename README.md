# OCI Instance Setup, Nginx Configuration, and Automation Guide

## Table of Contents

1. [Instance Creation and Initial Setup](#instance-creation-and-initial-setup)
2. [Installing and Configuring Nginx](#installing-and-configuring-nginx)
3. [Firewall Configuration on the Instance](#firewall-configuration-on-the-instance)
4. [OCI Network Security Settings](#oci-network-security-settings)
5. [Automating Instance Configuration with Terraform](#automating-instance-configuration-with-terraform)
6. [Troubleshooting](#troubleshooting)
7. [Useful Commands](#useful-commands)

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

## Troubleshooting

- Verify local and OCI network configurations.
- Use `curl http://localhost` on the instance to test Nginx locally.
- Review Nginx and firewall logs for errors.

## Useful Commands

- **Nginx Status**: `sudo systemctl status nginx`
- **Restart Nginx**: `sudo systemctl restart nginx`
- **Firewall Status** (`firewalld`): `sudo systemctl status firewalld`
- **Open Port 80 on `firewalld`**:
  ```bash
  sudo firewall-cmd --zone=public --add-service=http --permanent
  sudo firewall-cmd --reload
  ```

---

This README provides an overview of setting up Nginx on an OCI instance, including steps for firewall configuration and a basic introduction to automating the setup using Terraform. For complex Terraform setups or more advanced configurations, refer to the official [Terraform documentation](https://www.terraform.io/docs) and [OCI Terraform provider documentation](https://registry.terraform.io/providers/hashicorp/oci/latest/docs).
