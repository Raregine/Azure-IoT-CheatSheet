# Azure IoT Edge Cheat Sheet

References:

 https://docs.microsoft.com/en-us/azure/iot-edge/how-to-install-iot-edge-linux
 
 https://docs.microsoft.com/en-us/azure/iot-edge/troubleshoot

## Contents
1. Register Microsoft key and software repository feed	
2. Install the container runtime	
3. Install the Azure IoT Edge Security Daemon	
4. Configure the security daemon	
5. Verify successful installation	
6. Uninstall IoT Edge	
7. Check container logs for issues	
8. View the messages going through the IoT Edge hub	
9. Check the status of the IoT Edge security manager and its logs	


## 1. Register Microsoft key and software repository feed
Install the repository configuration

Raspbian Stretch:

    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list

 Ubuntu Server 16.04:
 
    curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list

Ubuntu Server 18.04:

    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list

Copy the generated list.

    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

Install Microsoft GPG public key

    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
 
## 2.  Install the container runtime
Update package lists on your device.

    sudo apt-get update
    
 Install the Moby engine
 
    sudo apt-get install moby-engine

 Install the Moby command-line interface (CLI). The CLI is useful for development but optional for production deployments.
 
    sudo apt-get install moby-cli

## 3. Install the Azure IoT Edge Security Daemon

Update package lists on your device.

    sudo apt-get update

 Install the security daemon. The package is installed at /etc/iotedge/.
 
    sudo apt-get install iotedge

## 4. Configure the security daemon

Open the configuration file

    sudo nano /etc/iotedge/config.yaml

 Save and close the file.
CTRL + X, Y, Enter

After entering the provisioning information in the configuration file, restart the daemon

    sudo systemctl restart iotedge

## 5. Verify successful installation

You can check the status of the IoT Edge Daemon:

    systemctl status iotedge 

 Examine daemon logs:
 
    journalctl -u iotedge --no-pager --no-full

 Run the troubleshooting tool to check for the most common configuration and networking errors:
 
    sudo iotedge check

 list running modules:
 
    sudo iotedge list

## 6. Uninstall IoT Edge

Remove the IoT Edge runtime.

    sudo apt-get remove --purge iotedge

 View all containers to see which ones remain
 
    sudo docker ps -a

 Delete the containers from your device, including the two runtime containers.
 
    sudo docker rm -f <container name>

 Finally, remove the container runtime from your device.
 
    sudo apt-get remove --purge moby-cli
    sudo apt-get remove --purge moby-engine

## 7. Check container logs for issues

    iotedge logs <container name>

## 8. View the messages going through the IoT Edge hub

You can view the messages going through the IoT Edge hub, and gather insights from verbose logs from the runtime containers. To turn on verbose logs on these containers, set RuntimeLogLevel in your yaml configuration file. To open the file:
On Linux:

    sudo nano /etc/iotedge/config.yaml

 On Windows:
 
    notepad C:\ProgramData\iotedge\config.yaml

By default, the agent element will look like the following example:

    agent:
      name: edgeAgent
      type: docker
      env: {}
      config:
        image: mcr.microsoft.com/azureiotedge-agent:1.0
        auth: {} 

 Replace env: {} with:
 
    env:
      RuntimeLogLevel: debug
      
 Save the file and restart the IoT Edge security manager.

## 9. Check the status of the IoT Edge security manager and its logs

The IoT Edge security manager is responsible for operations like initializing the IoT Edge system at startup and provisioning devices. If IoT Edge isn't starting, the security manager logs may provide useful information.
On Linux:

View the status of the IoT Edge security manager:

    sudo systemctl status iotedge

 View the logs of the IoT Edge security manager:
 
    sudo journalctl -u iotedge -f

View more detailed logs of the IoT Edge security manager:

Edit the IoT Edge daemon settings: 

    sudo systemctl edit iotedge.service

 Update the following lines:
 
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug

Restart the IoT Edge Security Daemon:

    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge 
 
