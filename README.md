# Full-Stack FastAPI and React Template

Welcome to the Full-Stack FastAPI and React template repository. This repository serves as a demo application for interns, showcasing how to set up and run a full-stack application with a FastAPI backend and a ReactJS frontend using ChakraUI.

## Project Structure

The repository is organized into two main directories:

- **frontend**: Contains the ReactJS application.
- **backend**: Contains the FastAPI application and PostgreSQL database integration.

Each directory has its own README file with detailed instructions specific to that part of the application.

## Getting Started

To get started with this template, please follow the instructions in the respective directories:

- [Frontend README](./frontend/README.md)
- [Backend README](./backend/README.md)

## Detailed Setup Instructions

### Prerequisites

- Docker and Docker Compose installed on your system.
- A domain name for your application (optional for local development).
- Access to Azure or another cloud provider (optional for deployment).

### Setting Up Docker and Docker Compose

1. **Docker Compose Configuration**: Ensure your `docker-compose.yml` is set up correctly. Here is an example configuration:

    ```yaml
    version: '3.7'

    services:
      backend:
        build:
          context: ./backend
        ports:
          - "8000:8000"
        networks:
          - app-network
        environment:
          - DOMAIN=tosinghdevs.pakasak.com
          - ENVIRONMENT=production

      frontend:
        build:
          context: ./frontend
        ports:
          - "5173:5173"
        networks:
          - app-network
        environment:
          - VITE_API_URL=http://tosinghdevs.pakasak.com:8000

      db:
        image: postgres:latest
        environment:
          POSTGRES_USER: tosingh54
          POSTGRES_PASSWORD: peranofo_54
          POSTGRES_DB: HngDevopsDb
        networks:
          - app-network
        volumes:
          - postgres_data:/var/lib/postgresql/data

      proxy-manager:
        image: jc21/nginx-proxy-manager:latest
        ports:
          - "8081:80"  # HTTP
          - "81:81"    # Admin interface
          - "443:443"  # HTTPS
          - "8090:8090" # Custom port for access
        networks:
          - app-network
        volumes:
          - data:/data
          - letsencrypt:/etc/letsencrypt
        environment:
          DEFAULT_HOST: tosinghdevs.pakasak.com

    networks:
      app-network:

    volumes:
      postgres_data:
      data:
      letsencrypt:
    ```

2. **Backend Configuration**: Ensure your backend settings are correctly set up, particularly the CORS settings to allow requests from your frontend domain:

    ```python
    from fastapi import FastAPI
    from starlette.middleware.cors import CORSMiddleware
    from app.core.config import settings

    app = FastAPI()

    if settings.BACKEND_CORS_ORIGINS:
        app.add_middleware(
            CORSMiddleware,
            allow_origins=[
                "http://tosinghdevs.pakasak.com",
                "http://tosinghdevs.pakasak.com:5173",
                "https://localhost",
                "https://localhost:5173"
            ],
            allow_credentials=True,
            allow_methods=["*"],
            allow_headers=["*"],
        )
    ```

3. **Frontend Configuration**: Ensure your Vite configuration is set up to expose the frontend server properly:

    ```javascript
    import { TanStackRouterVite } from "@tanstack/router-vite-plugin"
    import react from "@vitejs/plugin-react-swc"
    import { defineConfig } from "vite"

    // https://vitejs.dev/config/
    export default defineConfig({
      plugins: [react(), TanStackRouterVite()],
      server: {
        host: true
      }
    })
    ```

4. **NGINX Proxy Manager Setup**: Configure NGINX Proxy Manager to serve your frontend and backend services. Here are the key steps:

    - **Access NGINX Proxy Manager**: Go to `http://<your-server-ip>:81` to access the NGINX Proxy Manager admin interface.
    - **Add Proxy Hosts**: Add new proxy hosts for your frontend and backend.
        - **Frontend**:
            - **Domain Names**: `tosinghdevs.pakasak.com`
            - **Scheme**: `http`
            - **Forward Hostname / IP**: `frontend`
            - **Forward Port**: `5173`
            - **Enable SSL**: Check the SSL option and follow the instructions to generate or import SSL certificates.
        - **Backend**:
            - **Domain Names**: `tosinghdevs.pakasak.com`
            - **Scheme**: `http`
            - **Forward Hostname / IP**: `backend`
            - **Forward Port**: `8000/api`
            - **Enable SSL**: Check the SSL option and follow the instructions to generate or import SSL certificates.

5. **Ensure HTTPS Redirection**: Ensure that the NGINX Proxy Manager is set up to redirect HTTP traffic to HTTPS. This can usually be done in the SSL settings for each proxy host.

6. **DNS Configuration**: Make sure your domain name points to your server's IP address. This can be done in your DNS provider's settings.

## Additional Notes

- If you are deploying to Azure, ensure that necessary ports are open and properly configured in the Azure portal.
- For local development, you can map your domain to your local IP address by editing the `/etc/hosts` file on your development machine.

By following these steps, you should have a fully functioning full-stack application with FastAPI and React, served via NGINX Proxy Manager with SSL support.
