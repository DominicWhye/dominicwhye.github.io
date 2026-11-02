---
title: 'Setting up homelab'
description: 'A complete guide to provisioning a Raspberry Pi 5 home server with Docker and migrating container data to an external drive'
pubDate: 'Nov 3 2026'
heroImage: '../../assets/pi5.jpg'
---

Setting up a reliable home server requires moving beyond SD cards to prevent premature wear and improve read/write speeds. This documentation covers how to provision a Raspberry Pi 5 (4GB RAM) equipped with an Active Cooler, install Docker, and route all container data to a 1TB SanDisk E30 Portable SSD formatted in ext4.

### 1. Install Raspberry Pi OS

1. Download and open the **Raspberry Pi Imager** on your main computer.
2. Select your device (Raspberry Pi Model) and choose **Raspberry Pi OS Lite (64-bit)** for a headless server environment.
3. Choose your MicroSD card/storage choice as the storage target.
4. Click **Next** and edit the OS customization settings: enable SSH, set your username and password, and configure your local Wi-Fi if you aren't using Ethernet. 
5. Write the image, insert the SD card into your Pi 5, and power it on. SSH into the Pi to continue.

ssh command should be:
  ```bash
  ssh username@raspberrypi.local
  ```
or
  ```bash
  ssh username@ip_address
  ```

### 2. Prepare and Mount the External SSD

Connect your choice of External Storage to one of the USB ports on the Pi. 

1. Identify your drive's path and UUID:
   ```bash
   lsblk -f
2. Format the drive to ext4:
   ```bash
   sudo mkfs.ext4 /dev/sda1
3. Create a mount point and mount the drive:
   ```bash
   sudo mkdir -p /mnt/external_drive_name
   sudo mount /dev/sda1 /mnt/external_drive_name
   sudo chown -R $USER:$USER /mnt/external_drive_name
4. Configure it to mount automatically on boot by editing /etc/fstab:
   ```bash
   sudo nano /etc/fstab
5. Add the following line to the bottom (replace YOUR-UUID with the UUID from step 1):
   ```bash
   UUID=YOUR-UUID /mnt/external_drive_name ext4 defaults,noatime 0 2

### 3. Install / setup Docker

By this step, you should have a Raspberry with an external drive mounted with the device pointing to the external drive as the main storage 🤝👏🥳💃

But that is not enough, you would want to have docker installed to ensure your applications are isolated and run more cleanly so as to ensure resource efficiency

So to do that, let us install Docker using the official convenience script and add your user to the Docker group so you do not need to run commands with sudo:
  ```bash
  curl -fsSL [https://get.docker.com](https://get.docker.com) -o get-docker.sh
  sudo sh get-docker.sh
  sudo usermod -aG docker $USER
  ```

You would need to reboot here in order for it to take effect:
  ```bash
  sudo reboot
  ```
By default, Docker stores its images, volumes and container data on the SD card at /var/lib/docker. 
So to ensure longevity and performance, we will point this to the external drive.

1. Create a directory for Docker on the External Drive:
  ```bash
  mkdir -p /mnt/external_drive_name/docker_data
  ```
2. Stop the Docker service:
   ```bash
   sudo systemctl stop docker
   ```
3. Edit or create the Docker daemon configuration file:
   ```bash
   sudo nano /etc/docker/daemon.json
   ```
4. Add the data-root directive:
   ```bash
   {
   "data-root": "/mnt/external_drive_name/docker_data"
   }
   ```
5. Rsync any existing Docker data to the new location:
   ```bash
   sudo rsync -aP /var/lib/docker/ /mnt/external_drive_name/docker_data/
   ```
6. Restart the Docker service:
   ```bash
   sudo systemctl start docker
   ```
7. Now just verify the new directory is in use:
   ```bash
   docker info | grep "Docker Root Dir"
   ```
   Expected output: Docker Root Dir: /mnt/external_drive_name/docker_data

AAAANNDDD Voila! Your Raspberry Pi is now up and ready to take on containerized application, next step is to just choose what applications you want to add to your Docker
  

