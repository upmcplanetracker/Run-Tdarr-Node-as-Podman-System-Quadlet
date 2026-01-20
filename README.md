Tdarr Node Podman Quadlet Deployment
====================================

**Disclaimer:** I am not affiliated with, associated, authorized, endorsed by, or in any way officially connected with the Tdarr project.

This guide demonstrates how to deploy a **Tdarr Node** as a System Podman Quadlet on Ubuntu 25.10. This node acts as a remote worker to assist your primary Tdarr Server with transcoding tasks. This needs to be a system quadlet to communicate with the GPU.

System Information
------------------

* **Tested Environment:** Ubuntu 25.10
* **Podman Version:** 5.4.x
* **Role:** Remote Worker Node (Requires a separate Tdarr Server)

1\. The Quadlet File (tdarr-node.container)
-------------------------------------------

Download the `tdarr-node.container` file. This is pre-configured for Intel QuickSync (QSV) hardware acceleration.

2\. Installation & Setup
------------------------

### Step A: GPU Permissions

Ensure your system user has access to the GPU render nodes:

`sudo usermod -aG video,render $USER`

### Step B: Identify Server Address

Locate the IP or Hostname of your Tdarr Server (e.g., `http://192.168.1.50:8266`). You will need to enter this in the `.container` file's `serverURL` field.

### Step C: Move and Edit

Move the file to the systemd directory and update your media paths to match your server exactly:

`sudo mkdir -p /etc/containers/systemd`

`sudo mv tdarr-node.container /etc/containers/systemd/`

`sudo nano /etc/containers/systemd/tdarr-node.container` to update the configuration.

**Note on Paths:** For Tdarr nodes to work, the `/temp` and `/media` paths inside the container must be identical on both the server and all nodes.

3\. Activation
--------------

Trigger the Quadlet generator and start the service:

`sudo systemctl daemon-reload`

`sudo systemctl start tdarr-node`

4\. Post-Install Verification
-----------------------------

### Verify Connection

Check the logs to ensure the node has successfully handshaked with the master server:

`sudo journalctl -u tdarr-node -f`

### Check GPU Access

Verify the node can see the Intel hardware:

`podman exec -it tdarr-node ls -l /dev/dri`

5\. Updating
------------

The container is set to `AutoUpdate=registry`. To check for and apply updates:

`sudo podman auto-update`
