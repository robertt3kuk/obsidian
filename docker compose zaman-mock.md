```yaml 
version: "3.8"

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: zaman-app
    environment:
      PG_USER: ${PG_USERNAME}
      PG_PASSWORD: ${PG_PASSWORD}
      PG_NAME: ${PG_NAME}
      PG_HOST: postgres
      PG_PORT: ${PG_PORT:-5432}
      PG_SSL_MODE: ${PG_SSL_MODE:-disable}
      PG_POOL_MAX: ${PG_POOL_MAX}
      HTTP_PORT: ${HTTP_PORT}
      HTTP_USERNAME: ${HTTP_USERNAME}
      HTTP_PASSWORD: ${HTTP_PASSWORD}
      LOG_LEVEL: ${LOG_LEVEL}
    ports:
      - ${HTTP_PORT:-8080}:8080
    depends_on:
      - postgres
    networks:
      - zaman-network
    restart: unless-stopped

  postgres:
    image: postgres:17-alpine
    container_name: zaman-postgres
    environment:
      POSTGRES_USER: ${PG_USER}
      POSTGRES_PASSWORD: ${PG_PASSWORD}
      POSTGRES_DB: ${PG_DBNAME} # Fixed: POSTGRES_PG to POSTGRES_DB
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "${PG_PORT:-5432}:5432"
    networks:
      - zaman-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${PG_USERNAME} -d ${PG_NAME}"]
      interval: 10s
      timeout: 5s
      retries: 5

  caddy:
    image: caddy:2-alpine
    container_name: zaman-caddy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile # Load Caddyfile directly from root directory
      - caddy_data:/data
      - caddy_config:/config
    networks:
      - zaman-network
    restart: unless-stopped
    depends_on:
      - app

networks:
  zaman-network:
    driver: bridge

volumes:
  postgres_data:
  caddy_data:
  caddy_config:

```