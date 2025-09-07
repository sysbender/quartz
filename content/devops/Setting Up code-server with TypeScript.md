

### Setting Up code-server with TypeScript Tutorial in Docker on WSL

This guide outlines how to run `code-server` (a browser-based VS Code) in Docker on WSL, with the [beginners-typescript-tutorial](https://github.com/total-typescript/beginners-typescript-tutorial) repo auto-cloned and ready for `npm run exercise`. It uses the official `coder/code-server` image, installs Node.js 20 LTS, TypeScript, and git, and ensures WSL-compatible permissions.

---

## 📁 File Structure
```
code-server-ts-dev/
├── docker-compose.yml
├── Dockerfile
└── workspace/  # Created on run; holds repo
```

---

## 📄 `docker-compose.yml`
Orchestrates the container with persistent volumes and WSL UID/GID.

<xaiArtifact artifact_id="cb17a2d8-8e23-47d6-aa1b-a0a41dc0d934" artifact_version_id="10b27c54-4e2a-4ab8-9f1d-6c58a52583dd" title="docker-compose.yml" contentType="text/yaml">
```yaml
services:
  code-server:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: code-server-ts
    user: "1000:1000"
    ports:
      - "8080:8080"
    volumes:
      - ./workspace:/home/coder/project
      - ./code-server-config:/home/coder/.config/code-server
      - ./code-server-extensions:/home/coder/.local/share/code-server
    environment:
      - PUID=1000
      - PGID=1000
      - PASSWORD=your_secure_password_here
      - DEFAULT_WORKSPACE=/home/coder/project/beginners-typescript-tutorial
      - TZ=Etc/UTC
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/healthz"]
      interval: 30s
      timeout: 10s
      retries: 3
```
</xaiArtifact>

---

## 📄 `Dockerfile`
Builds on `coder/code-server`, adds Node.js 20, TypeScript, git, and clones the repo.

 ```yaml
 FROM codercom/code-server:ubuntu

# Switch to root for system installs
USER root

# Install dependencies: curl (for NodeSource), git, build tools
RUN apt-get update && apt-get install -y \
    curl \
    git \
    build-essential \
    python3 \
    && rm -rf /var/lib/apt/lists/*

# Install Node.js 20 LTS via NodeSource (replaces bundled Node ~18)
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get install -y nodejs && \
    npm install -g npm@latest typescript

# Clone the repo (verbose for logs)
WORKDIR /home/coder/project
RUN git clone --verbose https://github.com/total-typescript/beginners-typescript-tutorial.git beginners-typescript-tutorial && \
    cd beginners-typescript-tutorial && \
    ls -la  # Verify clone: Should list package.json, src/, etc.

# Install dependencies (separate layer for isolation; verbose)
RUN cd /home/coder/project/beginners-typescript-tutorial && \
    npm install --verbose

# Fix ownership to coder user (UID 1000) for WSL mounts
RUN chown -R coder:coder /home/coder/project

# Switch to non-root coder user
USER coder

# Expose code-server port
EXPOSE 8080

# Start code-server in the repo directory
WORKDIR /home/coder/project/beginners-typescript-tutorial
CMD ["code-server", "--bind-addr", "0.0.0.0:8080", "--auth", "password", "."]
 ```

---

## 🚀 How to Run
1. **Create directory**: `mkdir -p ~/code-server-ts-dev && cd ~/code-server-ts-dev`
2. **Save files**: Add `docker-compose.yml` and `Dockerfile`.
3. **Set permissions**: `mkdir -p workspace code-server-config code-server-extensions && chown -R $(id -u):$(id -g) workspace code-server-config code-server-extensions`
4. **Start**: `docker compose up -d`
5. **Access**: Open `http://localhost:8080`, log in with your password.
6. **Test**: In code-server terminal, run `npm run exercise` to start the tutorial.
7. **Obsidian link**: `[Open](file:///home/jason/code-server-ts-dev/workspace/beginners-typescript-tutorial/src/01/01-number.problem.ts)`

---

## 🔒 Notes
- **Security**: Change `PASSWORD` to a strong value or use `HASHED_PASSWORD` (generate via `docker exec code-server-ts code-server --hash-password your_password`).
- **WSL**: If mounts fail, restart WSL: `wsl --shutdown` (Windows PowerShell).
- **Update repo**: `cd /home/coder/project/beginners-typescript-tutorial && git pull && npm install`.
- **Troubleshooting**: Check `docker compose logs -f code-server-ts` for errors.

This setup delivers a fully functional TypeScript dev environment for the tutorial, ready in minutes!