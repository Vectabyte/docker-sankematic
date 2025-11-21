# Docker SankeyMATIC

This repository provides a Docker container for [SankeyMATIC](https://github.com/nowthis/sankeymatic).  
It is a fork of [bvmensvoort/docker-sankematic](https://github.com/bvmensvoort/docker-sankematic), which is no longer actively maintained.

The container is built automatically via GitHub workflows and hosted on [Docker Hub](https://hub.docker.com/repository/docker/vectabyte/sankeymatic/general).

---

## ⚠️ Disclaimer / Security Notes

- This container is a **direct packaging** of the original SankeyMATIC repository.  
- There are **no guarantees** for security updates or ongoing maintenance.  
- Use at your own risk in production environments.  

---

## Usage

You can run the container directly via Docker or use Docker Compose for easier configuration.

### Docker Compose Example

Below is a sample `docker-compose.yml` setup:

```yaml
services:
  sankeymatic:
    image: docker.io/vectabyte/sankeymatic
    container_name: sankeymatic
    restart: unless-stopped
    networks:
      - some-network
    ports:
      - "8080:80"
    volumes:
      - ./.apache.conf:/usr/local/apache2/conf/extra/servername.conf
    command: /bin/sh -c '(grep -F "Include conf/extra/servername.conf" /usr/local/apache2/conf/httpd.conf > /dev/null || echo "Include conf/extra/servername.conf" >> /usr/local/apache2/conf/httpd.conf) && exec httpd -D FOREGROUND'
````

* This setup mounts a local `.apache.conf` file into the container so you can pass a custom `ServerName` to Apache.
* Port `8080` on the host maps to `80` in the container.

---

### Apache Configuration

Create a file named `.apache.conf` in the same directory as your `docker-compose.yml`:

```apache
ServerName sankeymatic.localhost
```

* This file is automatically included in `httpd.conf` by the command in Docker Compose.
* The container ensures the line is only added if it does not already exist.

---

## How It Works

* The container runs Apache in the **foreground** using `httpd -D FOREGROUND` so Docker can manage it correctly.
* The included shell command checks for the `Include` directive in `httpd.conf` and appends it if missing.
* Logs can be viewed with:

```bash
docker logs -f sankeymatic
```

---

## Notes

* The container is built automatically via GitHub workflows.
* Updates to the container only happen when the workflows are triggered.
* This setup is intended for **local development and experimentation**, not production.

---

## References

* [SankeyMATIC GitHub](https://github.com/nowthis/sankeymatic)
* [Docker Hub Repository](https://hub.docker.com/repository/docker/vectabyte/sankeymatic/general)
* [Original Docker Repo](https://github.com/bvmensvoort/docker-sankematic)
