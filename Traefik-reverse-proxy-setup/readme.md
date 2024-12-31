# Setting up Traefik Reverse Proxy using Dockers

### Some useful links

- [ChatGPT Conversation](https://chatgpt.com/share/6773c2fb-b094-8011-aa18-408b079d2e99)

- [Brief Guide of setting up Traefik with Dockers](https://docs.techdox.nz/traefik/)

### Explanation in Simple Terms

This guide helps you set up **Traefik**, a tool that manages traffic for web services, as a reverse proxy using **Docker Compose**. The goal is to make your services accessible with secure HTTPS connections, automatically handled by Let's Encrypt.

---

### What You Need to Do

1. **Prepare Your Router**  
   Make sure ports 80 (HTTP) and 443 (HTTPS) are forwarded to your server. This is needed for Traefik to work properly.

---

### 1. **What Traefik Does**

- **Reverse Proxy**: Routes traffic to your services (like websites or apps) running in Docker containers.
- **Automatic HTTPS**: Traefik automatically gets SSL certificates from **Let's Encrypt** for secure connections.

---

### 2. **Setting Up Traefik with Docker Compose**

- Use the example `docker-compose.yml` to set up Traefik.
- Here’s what happens:
  - **Ports**: Traefik listens on:
    - 80 (HTTP)
    - 443 (HTTPS)
    - 8080 (Dashboard, optional for monitoring).
  - **Volumes**:
    - Traefik needs access to:
      - The Docker socket (`/var/run/docker.sock`) to detect services.
      - Its configuration file (`traefik.yml`).
      - The SSL certificate file (`acme.json`).
  - **Networks**:
    - A **dedicated Docker network** ensures Traefik can communicate with your services.

#### Example

```yaml
version: "3"
services:
  reverse-proxy:
    image: traefik:v2.11
    command: --configFile=/etc/traefik/traefik.yml
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./traefik.yml:/etc/traefik/traefik.yml
      - ./acme.json:/acme.json
    networks:
      - traefik
networks:
  traefik:
    external: true
```

---

### 3. **Create the Traefik Network**

Run the following command to create a shared network for Traefik and your services:

```bash
docker network create traefik
```

---

### 4. **Configure Traefik (traefik.yml)**

This file tells Traefik:

- Where to listen for traffic (ports 80 and 443).
- How to request SSL certificates from Let's Encrypt.

#### Example

```yaml
entryPoints:
  web:
    address: ":80"
  websecure:
    address: ":443"

certificatesResolvers:
  myresolver:
    acme:
      email: your-email@example.com
      storage: acme.json
      httpChallenge:
        entryPoint: web

providers:
  docker:
    exposedByDefault: false
```

- **Important parts**:
  - `certificatesResolvers`: Handles SSL certificates from Let's Encrypt.
  - `httpChallenge`: Verifies your domain for certificate issuance.
  - `providers.docker`: Connects Traefik to your Docker containers.

---

### 5. **Set Up the `acme.json` File**

This file stores SSL certificates securely. Create it with restricted permissions:

```bash
touch acme.json
chmod 600 acme.json
```

---

### 6. **Start Traefik**

Run the following command to start Traefik:

```bash
docker compose up -d
```

---

### 7. **Deploy a Service and Use HTTPS**

You can now deploy services (like Nginx or WordPress) and secure them with HTTPS by adding **Traefik labels** in their `docker-compose.yml` file. Labels tell Traefik how to handle traffic for each service.

#### Example 1: Nginx Service

```yaml
version: "3"
services:
  nginx_test:
    image: nginx
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.nginx.rule=Host(`test.yourdomain.com`)"
      - "traefik.http.routers.nginx.entrypoints=websecure"
      - "traefik.http.routers.nginx.tls=true"
      - "traefik.http.routers.nginx.tls.certresolver=myresolver"
    networks:
      - traefik
networks:
  traefik:
    external: true
```

- **Labels Explained**:
  - `traefik.enable=true`: Enables Traefik for this service.
  - `traefik.http.routers.nginx.rule`: Directs traffic for `test.yourdomain.com` to this service.
  - `websecure`: Uses HTTPS (port 443).
  - `tls.certresolver`: Uses Let's Encrypt for SSL.

#### Example 2: WordPress Service

```yaml
services:
  wordpress:
    image: wordpress:latest
    environment:
      - WORDPRESS_DB_HOST=db
      - WORDPRESS_DB_USER=wordpress
      - WORDPRESS_DB_PASSWORD=wordpress
      - WORDPRESS_DB_NAME=wordpress
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.wordpress.rule=Host(`wordpress.yourdomain.com`)"
      - "traefik.http.routers.wordpress.entrypoints=websecure"
      - "traefik.http.routers.wordpress.tls=true"
      - "traefik.http.routers.wordpress.tls.certresolver=myresolver"
    networks:
      - traefik
```

---

### 8. **Monitor and Verify**

- Access the Traefik Dashboard:  
  `http://your-host:8080`
- Test your services:
  - Visit `https://test.yourdomain.com` or `https://wordpress.yourdomain.com`.

---

### Recap

1. Set up Traefik as a reverse proxy.
2. Use a dedicated network for secure communication.
3. Configure SSL certificates with Let's Encrypt.
4. Deploy services with Traefik labels for HTTPS routing.

This approach simplifies hosting secure web services!

## Why use Docker Compose instead of Dockerfile

Using a `docker-compose.yml` file instead of a `Dockerfile` to set up Traefik offers several benefits, particularly for scenarios like reverse proxy configuration. Here's why it's preferred:

### 1. **Purpose of `docker-compose.yml` vs. `Dockerfile`**

- **`Dockerfile`**:  
  A `Dockerfile` is used to **build a custom image** by defining instructions to assemble a container. It's ideal when you need to modify the base image or add application-specific dependencies.
- **`docker-compose.yml`**:  
  This file is used to **define and manage multi-container applications** and their configurations, including networking, volumes, and dependencies. It's better suited for orchestrating how containers interact.

**Traefik doesn't need a custom image**, so using `docker-compose.yml` is simpler and more appropriate for defining its setup.

---

### 2. **Traefik Uses an Existing Image**

- Traefik provides a prebuilt Docker image (`traefik:v2.11`) that contains everything it needs to run.
- There's no need to create a custom `Dockerfile` because:
  - You don't need to add extra files or dependencies to Traefik.
  - The configuration (`traefik.yml`) is mounted via volumes, not baked into the image.

---

### 3. **Multi-Container Coordination**

- **`docker-compose.yml`** allows you to:
  - Define multiple services (e.g., Traefik, Nginx, WordPress) in one file.
  - Establish networks for these services to communicate securely.
- In a single `docker-compose.yml`, you can:
  - Configure Traefik as the reverse proxy.
  - Define other services and their labels to integrate with Traefik.
- Using a `Dockerfile` for each service would make it harder to manage relationships between containers.

---

### 4. **Networking Simplicity**

- The `docker-compose.yml` file makes it easy to define and reuse a **dedicated Docker network** for Traefik and its services:

  ```yaml
  networks:
    traefik:
      external: true
  ```

- This ensures secure and isolated communication. With `Dockerfile`, you would need to manually configure networks for each container.

---

### 5. **Ease of Configuration**

- In `docker-compose.yml`, you can directly specify volumes, ports, and commands:

  ```yaml
  services:
    reverse-proxy:
      image: traefik:v2.11
      command: --configFile=/etc/traefik/traefik.yml
      volumes:
        - /var/run/docker.sock:/var/run/docker.sock
        - ./traefik.yml:/etc/traefik/traefik.yml
        - ./acme.json:/acme.json
      ports:
        - "80:80"
        - "443:443"
        - "8080:8080"
  ```

- This eliminates the need for complex `Dockerfile` instructions or manual container creation with `docker run` commands.

---

### 6. **Dynamic and Flexible Service Deployment**

- Using `docker-compose.yml`, you can quickly scale, update, or manage services:
  - Run `docker compose up` to deploy all services in the file.
  - Add more services with minimal effort.
- With a `Dockerfile`, you would need to write separate scripts or commands to handle multi-container setups.

---

### 7. **Built-in Volume Management**

- In `docker-compose.yml`, you can easily define and share volumes (e.g., `acme.json` for SSL certificates):

  ```yaml
  volumes:
    - ./acme.json:/acme.json
  ```

- This avoids hardcoding volume paths or configurations in a `Dockerfile`.

---

### In Summary

We use `docker-compose.yml` to set up Traefik because it:

- Uses the prebuilt Traefik image without modification.
- Simplifies multi-container orchestration (Traefik + services).
- Makes networking and volume management straightforward.
- Offers a cleaner, more maintainable configuration for defining relationships between services.

If you were modifying Traefik’s base image, you might use a `Dockerfile`, but for most cases, `docker-compose.yml` is the better tool.

## How to use docker compose with Dockerfile

Yes, you can use `docker-compose.yml` to build and run a container for a project that has a `Dockerfile` in its folder. Docker Compose allows you to specify a service that is built using a `Dockerfile` in the project folder, along with any additional configurations like networks, volumes, and environment variables.

---

### How to Use `docker-compose.yml` with a `Dockerfile`

Here’s the typical process:

1. **Project Structure**: Ensure your project directory has the following structure:

   ```
   my-project/
   ├── Dockerfile
   ├── app/
   ├── docker-compose.yml
   ```

2. **Define `docker-compose.yml`**:  
   Write a `docker-compose.yml` file that references the `Dockerfile` in the same directory.

   Example:

   ```yaml
   version: "3"

   services:
     my-app:
       build:
         context: . # The folder containing the Dockerfile
         dockerfile: Dockerfile # Optional if the file is named 'Dockerfile'
       ports:
         - "8080:80" # Map port 80 inside the container to port 8080 on the host
       volumes:
         - ./app:/app # Mount the app folder for live development (optional)
       environment:
         - ENV_VAR=example # Add any required environment variables
       networks:
         - app-network

   networks:
     app-network:
       driver: bridge
   ```

   - **`build`**: Points to the location of the `Dockerfile`. By default, it looks for a `Dockerfile` in the specified `context`.
   - **`ports`**: Exposes container ports to the host.
   - **`volumes`**: Optionally maps folders from your host to the container for live development.
   - **`environment`**: Passes environment variables to the container.
   - **`networks`**: Connects the service to a Docker network (optional but useful for multi-container setups).

3. **Build and Run with Docker Compose**:
   Run the following command in the directory containing the `docker-compose.yml` file:

   ```bash
   docker compose up --build
   ```

   - `--build`: Ensures the image is rebuilt from the `Dockerfile`.
   - Without `--build`, Compose will reuse the existing image unless changes are detected in the `Dockerfile` or context.

4. **Access Your Service**:
   - If you mapped ports (e.g., `8080:80`), you can access the service at `http://localhost:8080`.

---

### Example Use Case

Suppose you have a simple web app:

#### `Dockerfile`

```dockerfile
FROM nginx:alpine
COPY ./app /usr/share/nginx/html
```

#### `docker-compose.yml`

```yaml
version: "3"

services:
  web:
    build:
      context: .
    ports:
      - "8080:80"
    volumes:
      - ./app:/usr/share/nginx/html
```

- Run with:

  ```bash
  docker compose up
  ```

- Visit `http://localhost:8080` to see your app.

---

### Why Use Docker Compose for a `Dockerfile` Project?

1. **Simplifies Multi-Container Apps**:  
   Combine your app with other services (e.g., a database or a reverse proxy) in the same `docker-compose.yml`.

2. **Flexible Configuration**:  
   Easily configure networks, environment variables, and volumes.

3. **Easier Management**:  
   Start, stop, and scale services using simple commands like:

   ```bash
   docker compose up
   docker compose down
   ```

4. **Portability**:  
   Your entire stack configuration is in one file, making it easy to share and deploy.

---

### Conclusion

Yes, you can use Docker Compose to run a container based on a `Dockerfile` in your project folder. It adds flexibility and allows you to manage your app and its dependencies more efficiently.

## Anatomy of Docker Compose YAML file

The anatomy of a typical `docker-compose.yml` file consists of various **commands** (or **directives**) that define how Docker services, networks, volumes, and other resources are set up and interact. Here's a detailed breakdown of the key components in a `docker-compose.yml` file:

---

### 1. **Top-Level Structure**

#### **`version`**

Defines the Compose file format version. It determines which features are available.

```yaml
version: "3.9" # Most recent and commonly used version
```

#### **`services`**

Specifies the containers (services) to be created and their configurations.

```yaml
services:
  web: ...
  db: ...
```

#### **`networks`**

Defines custom Docker networks for service communication.

```yaml
networks:
  frontend:
  backend:
```

#### **`volumes`**

Specifies named volumes for persistent storage.

```yaml
volumes:
  db_data:
```

---

### 2. **Inside `services`**

Each service represents a container. These directives define how a container behaves.

#### **`image`**

Specifies the image to use for the container.

```yaml
image: nginx:alpine
```

#### **`build`**

Used when you need to build an image from a `Dockerfile`.

```yaml
build:
  context: .
  dockerfile: Dockerfile # Optional if named `Dockerfile`
```

#### **`container_name`**

Sets a custom name for the container.

```yaml
container_name: my_custom_container
```

#### **`ports`**

Maps ports from the container to the host.

```yaml
ports:
  - "8080:80" # Host port 8080 -> Container port 80
```

#### **`volumes`**

Mounts volumes into the container. Can be:

- Named volumes
- Host directory paths

```yaml
volumes:
  - ./app:/usr/share/nginx/html # Bind mount host directory
  - db_data:/var/lib/mysql # Named volume
```

#### **`environment`**

Passes environment variables to the container.

```yaml
environment:
  - MYSQL_ROOT_PASSWORD=example
  - MYSQL_DATABASE=mydb
```

#### **`command`**

Overrides the default command specified in the Docker image.

```yaml
command: ["nginx", "-g", "daemon off;"]
```

#### **`entrypoint`**

Overrides the default entrypoint specified in the Docker image.

```yaml
entrypoint: ["sh", "-c"]
```

#### **`depends_on`**

Specifies service dependencies. Ensures a service starts after its dependencies are started (but doesn't wait for them to be "ready").

```yaml
depends_on:
  - db
  - redis
```

#### **`networks`**

Connects the service to specific networks.

```yaml
networks:
  - frontend
  - backend
```

#### **`restart`**

Specifies the restart policy for the container.

```yaml
restart: always
```

Options:

- `no`: Do not restart.
- `always`: Always restart.
- `on-failure`: Restart on non-zero exit codes.
- `unless-stopped`: Restart unless explicitly stopped.

#### **`labels`**

Defines metadata labels, often used with tools like Traefik.

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.app.rule=Host(`example.com`)"
```

---

### 3. **Inside `networks`**

Networks define how services communicate. Types:

- **Bridge** (default): Isolated network.
- **Host**: Shares the host's network stack.
- **None**: No networking.

#### Example

```yaml
networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
```

---

### 4. **Inside `volumes`**

Volumes store data persistently across container restarts.

#### Example

```yaml
volumes:
  db_data: # Named volume
    driver: local
```

---

### Example `docker-compose.yml` Breakdown

```yaml
version: "3.9"

services:
  web:
    image: nginx:alpine # Use the nginx:alpine image
    ports:
      - "8080:80" # Map host port 8080 to container port 80
    volumes:
      - ./html:/usr/share/nginx/html # Bind host directory to container
    networks:
      - frontend # Attach to frontend network

  db:
    image: mysql:8.0 # Use the MySQL 8.0 image
    environment: # Environment variables for MySQL
      - MYSQL_ROOT_PASSWORD=example
      - MYSQL_DATABASE=mydb
    volumes:
      - db_data:/var/lib/mysql # Use named volume for MySQL data
    networks:
      - backend # Attach to backend network

networks:
  frontend: # Define a custom frontend network
    driver: bridge
  backend: # Define a custom backend network
    driver: bridge

volumes:
  db_data: # Define a named volume for MySQL data
```

---

### Key Components of the Example

1. **`web` Service**:

   - Uses the `nginx:alpine` image.
   - Exposes the web server on `http://localhost:8080`.
   - Mounts the `html` directory from the host for web content.

2. **`db` Service**:

   - Uses the `mysql:8.0` image.
   - Stores database data persistently in a named volume (`db_data`).
   - Accepts environment variables to configure MySQL.

3. **Networks**:

   - Separate `frontend` and `backend` networks for isolation.
   - Web and DB services are isolated but can be connected if needed.

4. **Volumes**:
   - Persistent storage for the database.

---

### Conclusion

The `docker-compose.yml` file is a powerful and flexible way to define containerized applications. Its structure enables you to manage multiple services, networks, and volumes while customizing behavior through commands like `ports`, `volumes`, `environment`, and `build`. Understanding these commands helps you deploy, scale, and maintain your applications efficiently.

## Example of Docker Compose Setup

Here's a `docker-compose.yml` file for a full-stack setup with the following components:

1. **Frontend**: Next.js application.
2. **Backend**: Python FastAPI application.
3. **Proxy**: Traefik for reverse proxy and automatic HTTPS.
4. **Database**: PostgreSQL.

---

### Directory Structure

Ensure the following directory structure for the setup:

```
project/
├── traefik/
│   ├── traefik.yml         # Traefik configuration
│   ├── acme.json           # SSL certificate storage
├── frontend/               # Next.js app
│   ├── Dockerfile
├── backend/                # FastAPI app
│   ├── Dockerfile
│   ├── app/                # FastAPI app files
├── docker-compose.yml
```

---

### `docker-compose.yml`

```yaml
version: "3.9"

services:
  traefik:
    image: traefik:v2.11
    container_name: traefik
    command:
      - --providers.docker
      - --entrypoints.web.address=:80
      - --entrypoints.websecure.address=:443
      - --certificatesresolvers.myresolver.acme.tlschallenge=true
      - --certificatesresolvers.myresolver.acme.email=your-email@example.com
      - --certificatesresolvers.myresolver.acme.storage=/acme.json
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./traefik/traefik.yml:/etc/traefik/traefik.yml
      - ./traefik/acme.json:/acme.json
    networks:
      - web

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: frontend
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.frontend.rule=Host(`frontend.example.com`)"
      - "traefik.http.routers.frontend.entrypoints=websecure"
      - "traefik.http.routers.frontend.tls=true"
      - "traefik.http.routers.frontend.tls.certresolver=myresolver"
    networks:
      - web

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: backend
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.backend.rule=Host(`backend.example.com`)"
      - "traefik.http.routers.backend.entrypoints=websecure"
      - "traefik.http.routers.backend.tls=true"
      - "traefik.http.routers.backend.tls.certresolver=myresolver"
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/mydatabase
    networks:
      - web
      - db

  db:
    image: postgres:14
    container_name: postgres
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: mydatabase
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - db

networks:
  web:
    driver: bridge
  db:
    driver: bridge

volumes:
  postgres_data:
```

---

### Explanation of Each Service

#### 1. **Traefik (Proxy)**

- Handles reverse proxying for both frontend and backend.
- Configured for automatic HTTPS using Let's Encrypt.
- Listens on ports 80 and 443 for HTTP and HTTPS.

#### 2. **Frontend (Next.js)**

- A container built using a `Dockerfile` in the `frontend` folder.
- Accessible at `https://frontend.example.com`.

#### 3. **Backend (FastAPI)**

- A Python FastAPI app connected to the PostgreSQL database.
- Accessible at `https://backend.example.com`.

#### 4. **Database (PostgreSQL)**

- Stores application data.
- Exposed only to the `backend` service via the `db` network.

---

### `traefik.yml`

Traefik configuration file (`traefik/traefik.yml`):

```yaml
entryPoints:
  web:
    address: ":80"
  websecure:
    address: ":443"

providers:
  docker:
    exposedByDefault: false

certificatesResolvers:
  myresolver:
    acme:
      email: your-email@example.com
      storage: /acme.json
      tlsChallenge: {}
```

---

### `frontend/Dockerfile`

Dockerfile for the Next.js frontend:

```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package.json yarn.lock ./
RUN yarn install
COPY . .
RUN yarn build
EXPOSE 3000
CMD ["yarn", "start"]
```

---

### `backend/Dockerfile`

Dockerfile for the FastAPI backend:

```dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

### `acme.json`

Create an empty file for SSL certificate storage:

```bash
touch traefik/acme.json
chmod 600 traefik/acme.json
```

---

### How to Deploy

1. Clone or set up the directory structure.
2. Build and run the stack:
   ```bash
   docker compose up -d
   ```
3. Ensure the DNS for `frontend.example.com` and `backend.example.com` points to your server's IP.

4. Access the services:
   - Frontend: `https://frontend.example.com`
   - Backend: `https://backend.example.com`

---

This configuration provides a fully functional stack with frontend, backend, database, and proxy, all integrated and secured with HTTPS.
