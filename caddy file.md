```Caddyfile
example.com {
    # Reverse proxy all requests to the app container on port 8080
    reverse_proxy localhost:8080

    # Automatically handle HTTPS (Caddy will obtain and manage certificates)
    # This setting is implicit but included for clarity
    tls internal

    # Log requests to stdout
    log {
        output stdout
        format console
    }

```