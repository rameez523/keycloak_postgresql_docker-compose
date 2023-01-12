# keycloak_postgresql_docker-compose
setup keycloak and postgresql using docker-compose, also setup nginx reverse proxy on that server.

# Keycloak and Postgres Docker Compose Configuration
This configuration file sets up a Keycloak server and a Postgres database using Docker Compose.

Services
Postgres
The Postgres service uses the official postgres image from Docker Hub. It creates a volume for the data directory, which is mapped to the host machine's ./postgres_data directory. Environment variables are used to set the database name, username, and password.

Keycloak
The Keycloak service uses the jboss/keycloak image from Docker Hub. It maps port 8080 on the host machine to port 8180 on the container. The command option is used to set the port offset to 100, so that Keycloak runs on port 8180 within the container.

Environment variables are used to configure the database connection, including the vendor (Postgres), address (postgres), schema (public), username, and password. Additional environment variables are used to set the admin user and password for the Keycloak server, as well as the SSL requirements and the proxy address forwarding.

The service also creates a volume for the realms directory, which is mapped to the host machine's ./realms directory, it could be useful for backup and persistence of Keycloak's configuration.

Volumes
postgres_data
This volume is used to persist the data for the Postgres service. It uses the local driver.

realms
This volume is used to persist the realms directory for the Keycloak service. It uses the local driver.

The depends_on option ensure that the postgres service is started before keycloak service.

You should run docker-compose up command to start the services, and docker-compose down to stop them.

You can access the keycloak admin console by browsing to http://localhost:8080/auth .

Please keep in mind that the passwords used in this example are for demonstration purposes only, and you should change them to something more secure in a production environment.


# Nginx Reverse Proxy and SSL Configuration
This configuration file sets up a reverse proxy using Nginx to forward requests to a backend server at IP address 10.0.0.90 on port 8080 and also configures SSL using Let's Encrypt.

Server Blocks
Server Block for 443 (HTTPS)
The first server block listens on port 443 for HTTPS requests with the server_name "example.com". It includes access and error log files for tracking requests and errors.

It has a location block which forwards the incoming requests to the backend server at IP address 10.0.0.90 on port 8080. It also sets the various headers like X-Forwarded-For, X-Forwarded-Host and X-Forwarded-Proto for the backend server to use.

It also sets the ssl_certificate and ssl_certificate_key path to the Let's Encrypt certificate and private key files.

Server Block for 80 (HTTP)
This block redirects all HTTP requests to HTTPS by listening on port 80 and using if statement to check the host, if it's example.com, it returns 301 redirect to https version of the same request.

Let's Encrypt
This configuration uses Let's Encrypt to generate and manage SSL certificates. The certificate and private key files are located in the /etc/letsencrypt/live/example.com directory. The include and ssl_dhparam lines are commented out but it's recommended to include the options-ssl-nginx.conf and ssl-dhparams.pem for better security.

You can run sudo certbot --nginx to obtain SSL certificate from Let's Encrypt and configure the nginx automatically.

Please note that the IP address, domain name and file paths used in this example are for demonstration purposes only, and should be updated to match your specific use case.
