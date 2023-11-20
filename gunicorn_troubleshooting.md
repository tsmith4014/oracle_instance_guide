# Monty Hall Project Deployment Guide

This document provides a comprehensive guide for deploying and managing the Monty Hall Django app and its associated services. It covers setting up Gunicorn, Nginx, and managing processes.

## Initial Setup

### 1. Directory Structure

Ensure your project directory is structured properly. For this project, the relevant directories and files are:

- Backend Django app: `/home/opc/monty_hall_project/backend/django_monty_hall`
- Gunicorn configuration file: `/home/opc/monty_hall_project/backend/gunicorn_config.py`
- Nginx configuration file: `/etc/nginx/nginx.conf`

### 2. Nginx Configuration

The Nginx server is set up to serve the React frontend and proxy requests to the Django backend. Ensure the following settings are in your Nginx config file:

- React app served from `/home/opc/monty_hall_project/frontend/front/dist`
- API requests proxied to `http://127.0.0.1:8000`

## Running Gunicorn Manually

To start the Gunicorn server manually from the specific project directory:

1. Navigate to the Django project directory:

   ```bash
   cd /home/opc/monty_hall_project/backend/django_monty_hall
   ```

2. Start Gunicorn with the specified number of workers and bind it to port 8000:
   ```bash
   /home/opc/monty_hall_project/backend/venv/bin/gunicorn --workers 3 --bind 0.0.0.0:8000 django_monty_hall.wsgi:application
   ```

## Managing Gunicorn with Systemd

For a more robust and production-ready setup, manage Gunicorn with systemd. This allows for automatic restarting of the service and better process management.

1. Stop manually started Gunicorn processes if any. Find the PIDs using `sudo lsof -i :8000` and kill them:

   ```bash
   sudo kill 269337 277214 277215 277216
   ```

2. Edit the Gunicorn service file to specify the working directory and command to start Gunicorn. The service file path is `/etc/systemd/system/montyhall-webapp.service`. Ensure the `WorkingDirectory` and `ExecStart` are set as follows:

   ```
   [Service]
   Type=simple
   WorkingDirectory=/home/opc/monty_hall_project/backend/django_monty_hall
   Environment="PATH=/home/opc/monty_hall_project/backend/venv/bin"
   ExecStart=/home/opc/monty_hall_project/backend/venv/bin/gunicorn django_monty_hall.wsgi:application -c /home/opc/monty_hall_project/backend/gunicorn_config.py
   ```

3. After editing, reload the systemd daemon and restart the service:

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart montyhall-webapp
   ```

4. Check the status of the service to ensure it's running without errors:
   ```bash
   sudo systemctl status montyhall-webapp
   ```

## Troubleshooting Tips

- If Gunicorn fails to start, check the journal logs for detailed error messages:

  ```bash
  sudo journalctl -u montyhall-webapp
  ```

- Ensure the Gunicorn configuration file has the correct settings for binding to the desired IP and port.

- Verify that the Nginx configuration is correctly set up to proxy requests to the Django app.

- If changes are made to the Gunicorn or Nginx configuration files, remember to restart the respective services.

By following these steps, you should have a fully functioning setup for your Monty Hall project, with both manual and systemd-managed options for running Gunicorn.
