# Chapter 2 — Install Docker + Your First Container

> **Course:** Docker (Zero to Hero) · **Chapter:** 2 of 8
> Read on the website: [learn.bazardev.com/docker/02-install-and-first-container](https://learn.bazardev.com/docker/02-install-and-first-container)
>
> 🇧🇩 **Prefer Bangla?** [Read this chapter in Bangla →](https://learn.bazardev.com/docker/02-install-and-first-container?lang=bn)

Time to get hands-on. By the end of this chapter you will have Docker installed, you'll have run two containers, and you'll understand exactly what each command did.

## 1. Installing Docker

### Linux (Ubuntu/Debian — the official convenience script)

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Then let your user run `docker` without typing `sudo` every time:

```bash
sudo usermod -aG docker $USER
```

**Log out and log back in** (or reboot) for the group change to take effect.

### macOS / Windows

Install **Docker Desktop** — it bundles the Docker Engine, CLI, and a GUI, inside a lightweight VM:

- macOS: [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)
- Windows: same link, requires WSL2 enabled.

### Verify the install

```bash
docker --version
docker info
```

If `docker info` prints a big block of server details without errors, the Docker daemon is running correctly.

## 2. Your first container: `hello-world`

```bash
docker run hello-world
```

You'll see output like:

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
...
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

Here's exactly what happened, step by step — this is the Chapter 1 architecture diagram, now actually running:

1. The CLI sent `docker run hello-world` to the Daemon.
2. The Daemon looked for a `hello-world` image **locally** — didn't find one.
3. It **pulled** the image from Docker Hub.
4. It started a **container** from that image.
5. The container printed its message and exited (its one job was done).

Run `docker run hello-world` again — notice it's instant this time, because the image is now cached locally. No download needed.

## 3. A real container: nginx (a web server)

```bash
docker run -d -p 8080:80 --name my-nginx nginx
```

Now open `http://localhost:8080` (or `http://<your-server-ip>:8080`) in a browser — you'll see the nginx welcome page, served from inside a container.

Breaking down the flags:

| Flag | Meaning |
|---|---|
| `-d` | **Detached** — run in the background, give you back your terminal |
| `-p 8080:80` | **Port mapping** — `host_port:container_port`. Requests to port 8080 on your machine get forwarded to port 80 inside the container (where nginx listens) |
| `--name my-nginx` | Give the container a friendly name, instead of a random one Docker would generate |
| `nginx` | The image to run (pulled from Docker Hub automatically, since you don't have it yet) |

## 4. Looking at running containers

```bash
docker ps
```

Shows all **running** containers — you should see `my-nginx` with status `Up`.

```bash
docker ps -a
```

Shows **all** containers, including stopped ones (like the `hello-world` container, which exited right after printing its message).

```bash
docker logs my-nginx
```

Shows the output/logs of that container — useful for debugging when something's not working as expected.

## 5. Stopping and removing a container

```bash
docker stop my-nginx
docker rm my-nginx
```

- `stop` gracefully shuts the container down (it still exists, just not running).
- `rm` removes it completely.

You can also do both in one shot:

```bash
docker rm -f my-nginx
```

## 6. A quick look at images

```bash
docker images
```

Lists every image you've pulled or built, locally. You'll see `hello-world` and `nginx` in that list now. We'll go deep on images vs. containers — and the commands around them — in **Chapter 3**.

## 7. Key takeaways

- `docker run <image>` pulls the image (if needed) and starts a container from it.
- `-d` runs in the background; `-p host:container` maps ports so you can reach the container from outside.
- `docker ps` shows running containers; `docker ps -a` shows all of them, running or stopped.
- `docker stop` pauses a container; `docker rm` deletes it; `docker rm -f` does both.
- `docker images` lists what you have stored locally.

## 8. Test yourself (exam prep)

<details>
<summary><strong>Q1. What's the difference between `docker ps` and `docker ps -a`?</strong></summary>

`docker ps` shows only currently running containers. `docker ps -a` shows every container, including ones that have stopped or exited.
</details>

<details>
<summary><strong>Q2. In `docker run -d -p 8080:80 nginx`, what does `8080:80` mean?</strong></summary>

It maps port 8080 on your host machine to port 80 inside the container. Traffic hitting `localhost:8080` gets forwarded to port 80, where nginx is listening inside the container.
</details>

<details>
<summary><strong>Q3. Why was the second `docker run hello-world` instant, while the first one took a few seconds?</strong></summary>

The first run had to pull the `hello-world` image from Docker Hub over the network. The second run found the image already cached locally, so no download was needed — it just started a new container from the cached image immediately.
</details>

<details>
<summary><strong>Q4. What's the difference between `docker stop` and `docker rm`?</strong></summary>

`docker stop` shuts the container down but keeps it around (you could `docker start` it again later). `docker rm` deletes the container entirely. `docker rm -f` stops and removes it in one step.
</details>

## 9. Free resources

- Official docs: [Install Docker Engine](https://docs.docker.com/engine/install/)
- Official docs: [Docker run reference](https://docs.docker.com/reference/cli/docker/container/run/)
- YouTube: **"Docker Tutorial for Beginners [FULL COURSE in 3 Hours]"** — channel: *TechWorld with Nana* (covers this exact install + first-run flow)

---

**Next:** [Chapter 3 — Images vs Containers →](../01-docker/03-images-vs-containers.md) *(coming soon)*
