# Dria Node Setup Guide

This guide covers how to set up and run a **Dria Node** on **Ubuntu 22.04** with necessary security configurations.

## 🚀 System Preparation

1. **Update the system**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install dependencies** (recommended for networking and security):
   ```bash
   sudo apt install unzip ufw vim -y
   ```

## 📥 Download and Install Dria Node

1. **Move to the home directory**:
   ```bash
   cd $HOME
   ```

2. **Download the latest release**:
   ```bash
   curl -L -o dkn-compute-node.zip https://github.com/firstbatchxyz/dkn-compute-launcher/releases/latest/download/dkn-compute-launcher-linux-amd64.zip
   ```

3. **Unzip the archive and navigate into the directory**:
   ```bash
   unzip dkn-compute-node.zip -d dkn-compute-node
   cd dkn-compute-node
   ```

4. **Run the node**:
   ```bash
   ./dkn-compute-launcher
   ```
   - You will be prompted to enter **your secret wallet key** and **Gemini API key**.
   - Once the setup is complete, **stop the process** by pressing `CTRL + C`.

## 🛠️ Run Node in the Background (systemd Service)

To keep the node running in the background, create a **systemd service**:

1. **Create the service file**:
   ```bash
   sudo vim /etc/systemd/system/dria.service
   ```
   **Add the following content:**
   ```ini
   [Unit]
   Description=Dria Node
   After=network.target

   [Service]
   User=root
   EnvironmentFile=/root/dkn-compute-node/.env
   ExecStart=/root/dkn-compute-node/dkn-compute-launcher
   WorkingDirectory=/root/dkn-compute-node/
   Restart=on-failure

   [Install]
   WantedBy=multi-user.target
   ```

2. **Reload systemd and start the service**:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable dria
   sudo systemctl start dria
   ```

3. **Check logs to ensure the node is running**:
   ```bash
   journalctl -u dria -f --no-hostname -o cat
   ```

## 🔒 Security Enhancements

### 📌 Configure UFW (Firewall)
1. **Enable UFW and allow required ports**:
   ```bash
   sudo ufw default deny incoming
   sudo ufw default allow outgoing
   sudo ufw allow 4001/tcp
   sudo ufw allow 4001/udp
   sudo ufw allow 22/tcp  # Ensure SSH is accessible
   sudo ufw enable
   ```
2. **Check firewall status**:
   ```bash
   sudo ufw status verbose
   ```

## ✅ Verification
- **Check if the node is running:**
  ```bash
  systemctl status dria
  ```
- **Monitor logs in real-time:**
  ```bash
  journalctl -u dria -f --no-hostname -o cat
  [2025-03-02T12:15:25.064Z INFO  dkn_compute::node::diagnostic] Diagnostics (v0.3.7):
      Peer Count (mesh/all): 8 / 302
      Pending Tasks (single/batch): 0 / 0
      Peer ID: 16Uiu2HAkuXs7Rv7qAuYBWwGwe9m3nY7aM7bQqi7SQ12E9yaeqxgq
      Address: 0x3966d7ee335a7dadeca73f75c7ce4b739b2e184d
      Models: gemini/gemini-1.5-flash
  ```
- **Check firewall settings:**
  ```bash
  sudo ufw status
  ```

## 🎯 Done! Your Dria Node is now running securely 🚀
