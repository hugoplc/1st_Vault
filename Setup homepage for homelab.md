![[Pasted image 20241129161247.png]]

[homepage](https://gethomepage.dev/)

# Docker Installation

services:
  homepage:
    image: ghcr.io/gethomepage/homepage:latest
    container_name: homepage
    ports:
      - 3000:3000
    volumes:
      - /path/to/config:/app/config # Make sure your local config directory exists






