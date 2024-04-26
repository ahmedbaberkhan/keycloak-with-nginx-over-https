# keycloak-with-nginx-over-https
Streamline Keycloak Setup: Context Path, Nginx Proxy, HTTPS, and SSL/TLS Certificates

# Simplified Keycloak Setup

This repository is designed to simplify the setup process for beginners encountering challenges in configuring Keycloak with the following features:

- Setting the context-path to '/auth'.
- Utilizing Nginx reverse proxy.
- Enabling Keycloak to operate over HTTPS.
- Implementing Nginx access with certificates.

The goal is to provide a straightforward solution for novices to effectively deploy Keycloak with these essential functionalities.


# Docker Compose Configuration

This Docker Compose configuration sets up services for Nginx, Keycloak, and PostgreSQL.

## Services

### Nginx

Nginx is configured to serve as a reverse proxy and handle SSL termination.

- **Image:** nginx:latest
- **Ports:** 80 (HTTP), 443 (HTTPS)
- **Volumes:**
  - `./conf/nginx.conf:/etc/nginx/nginx.conf`: Custom Nginx configuration file
  - `./certs/dummy.crt:/etc/nginx/dummy.crt`: SSL certificate
  - `./certs/dummy.key:/etc/nginx/dummy.key`: SSL private key
  - `./certs/ca.crt:/etc/nginx/ca.crt`: Certificate authority file

### Keycloak

Keycloak is an open-source identity and access management solution.

- **Image:** keycloak/keycloak:latest
- **Environment Variables:**
  - `KEYCLOAK_ADMIN`: Admin username
  - `KEYCLOAK_ADMIN_PASSWORD`: Admin password
  - `DB_VENDOR`: Database vendor (POSTGRES in this case)
  - `DB_ADDR`: Database address (linked to the `postgres` service)
  - `DB_DATABASE`: Database name
  - `DB_USER`: Database username
  - `DB_PASSWORD`: Database password
  - `KC_HTTP_RELATIVE_PATH`: Relative path for Keycloak HTTP
  - `KC_HTTP_ENABLED`: Enable/disable HTTP (true/false)
  - `KC_HTTPS_CERTIFICATE_FILE`: Path to SSL certificate file
  - `KC_HTTPS_CERTIFICATE_KEY_FILE`: Path to SSL private key file
- **Volumes:**
  - `./certs:/etc/x509/https`: SSL certificate directory
- **Command:** `start-dev` (for development environment)

### PostgreSQL

PostgreSQL is used as the database backend for Keycloak.

- **Image:** postgres:latest
- **Environment Variables:**
  - `POSTGRES_DB`: Database name
  - `POSTGRES_USER`: Database username
  - `POSTGRES_PASSWORD`: Database password
- **Volumes:**
  - `./postgres-data:/var/lib/postgresql/data`: Persistent data storage

## Volumes

- **keycloak-volume:** Local volume for Keycloak (used for persistence)

## Networks

- **Name:** Keycloak network
- **Description:** Default network for communication between services.

# nginx.conf

This configuration file sets up Nginx to act as a reverse proxy for Keycloak, redirecting HTTP traffic to HTTPS and handling SSL/TLS encryption. Below is a breakdown of the configuration:

- **HTTP Server Block (port 80):**
  - Listens on port 80 and redirects all HTTP traffic to HTTPS.
  - The `location /` block issues a 301 redirect to the same request URI on HTTPS.

- **HTTPS Server Block (port 443):**
  - Listens on port 443 (HTTPS) with SSL/TLS encryption.
  - Configures SSL/TLS certificates (`dummy.crt` and `dummy.key`) for encryption.
  - Specifies SSL protocols and ciphers to enhance security.
  - Utilizes client-side SSL verification with the `ca.crt` certificate and sets `ssl_verify_client` to `on`.

- **Proxying Requests to Keycloak:**
  - The `location /auth` block proxies requests to Keycloak running at `https://keycloak:8443/auth`.
  - Sets appropriate headers for forwarding client information (`Host`, `X-Real-IP`, `X-Forwarded-For`, `X-Forwarded-Proto`).

- **SSL Termination (Optional):**
  - Allows adding SSL termination configuration if required.

Ensure to replace placeholder certificate and key files (`dummy.crt`, `dummy.key`, `ca.crt`) with your actual SSL/TLS certificates and certificate authority file.

# Running Keycloak Setup

1. **Docker:**
   - docker compose up -d.
     
2. **Import Certificate into Browser:** [To run Keycloak in your browser, follow these steps]
   - Obtain the `dummy.p12` file provided in certs folder.
   - Import this certificate into your browser's trusted certificate store.
   - When prompted for a password, use 'dummy'.

   *Note: Importing the certificate ensures secure communication with Keycloak over HTTPS.*

# Note on Certificates

The dummy certificates provided in this repository are intended solely for facilitation purposes. We strongly recommend that you generate and use your own SSL/TLS certificates for secure communication with Keycloak.



