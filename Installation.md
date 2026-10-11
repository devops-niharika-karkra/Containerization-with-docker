# Docker Installation on Ubuntu 🐳
## Objective
Install Docker Engine on Ubuntu using Docker's official APT repository.
## Step 1: Update Packages
```bash
sudo apt update
sudo apt upgrade -y
```
## Step 2: Install Prerequisites
```bash
sudo apt install ca-certificates curl -y
```
## Step 3: Add Docker's GPG Key
```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
## Step 4: Add the Official Docker Repository
```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```
Update package information:
```bash
sudo apt update
```
## Step 5: Install Docker Engine
```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```
## Step 6: Start Docker
```bash
sudo systemctl enable --now docker
sudo systemctl status docker
```
Look for `active (running)`. Press `q` to exit the status screen.
## Step 7: Verify Installation
```bash
docker --version
sudo docker run hello-world
```
## Step 8: Test Basic Commands
```bash
sudo docker images
sudo docker ps
sudo docker ps -a
sudo docker info
```
## Optional: Run Docker Without sudo
```bash
sudo usermod -aG docker "$USER"
```
Log out and log back in, then test `docker run hello-world`. **Warning:** Membership in the `docker` group grants root-level privileges, so only add trusted users.
## Troubleshooting
- **Docker service not running:** Check `sudo systemctl status docker`.
- **Permission denied:** Use `sudo` or review Docker group membership.
- **Package not found:** Check the repository configuration and run `sudo apt update
