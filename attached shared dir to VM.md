# Mounting VirtualBox shared folders on Ubuntu Server 18.04 LTS (Bionic Beaver)
This guide will walk you through the steps on how to setup a VirtualBox shared folder inside your Ubuntu Server guest. 

## Initial Steps
- Open VirtualBox
  
- Right-click your VM, then click **Settings**
  
- Go to **Shared Folders** section
  
- Add a new shared folder

  
- On **Add Share** prompt, select the **Folder Path** in your host that you want to be accessible inside your VM. Type `shared` for the **Folder Name**. Make sure that **Read-only** and **Auto-mount** are unchecked and **Mount point** is blank. Then click **OK**.

  
- Start your VM

  
- Once your VM is up and running, go to **Devices** -> **Insert Guest Additions CD image**

  
- Use the following command to mount the CD
  ```
  sudo mkdir /media/cdrom
  sudo mount -t iso9660 /dev/cdrom /media/cdrom
  ```
- Install dependencies for VirtualBox guest additions
  ```
  sudo apt-get update
  sudo apt-get install -y build-essential linux-headers-`uname -r`
  ```
- Run the installation script for the guest additions. Wait until the installation completes.
  ```
  sudo /media/cdrom/./VBoxLinuxAdditions.run
  ```
- Reboot VM
  ```
  sudo shutdown -r now
  ```
- Create `shared` directory in your home
  ```
  mkdir ~/shared
  ```
- Mount the shared folder from the host to your `~/shared` directory
  ```
  sudo mount -t vboxsf shared ~/shared
  ```
- The host folder should now be accessible inside the VM.
  ```
  cd ~/shared
  ```
## Make the mount folder persistent
This directory mount we just made is temporary and it will disappear on next reboot. To make this permanent, we'll set it so that it will mount our `~/shared` directory on system startup

- Edit `fstab` file in `/etc` directory
  ```
  sudo nano /etc/fstab
  ```
- Add the following line to `fstab` (separated by tabs). Make sure to replace `<username>` with your username. Save the file.
  ```
  shared	/home/<username>/shared	vboxsf	defaults	0	0
  ```
- Edit `modules`
  ```
  sudo nano /etc/modules
  ```
- Add the following line to `/etc/modules` and save
  ```
  vboxsf
  ```
- Reboot the VM and log-in again
  ```
  sudo shutdown -r now
  ```
- Go to your home directory and check to see if the directory is highlighted in green. 

  ![screenshot](https://i.imgur.com/hiXgIFP.png)

If it is then congratulations! You successfully linked the directory within your VM with your host folder.

## Bonus: Using shared folders as Apache root directory
How to point apache's web directory to our folder in the host.
- Remove apache's old `html` directory (WARNING! Backup your data if necessary)
  ```
  sudo rm -rf /var/www/html	
  ```
- Add a symbolic link in its place
  ```
  sudo ln -s ~/shared /var/www/html
  ```
