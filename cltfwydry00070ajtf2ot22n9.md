---
title: "All you need to know about Storage Management in Linux"
datePublished: Wed Mar 06 2024 14:47:58 GMT+0000 (Coordinated Universal Time)
cuid: cltfwydry00070ajtf2ot22n9
slug: all-you-need-to-know-about-storage-management-in-linux
tags: linux, devops, storage, learn-in-public

---

Have you ever wondered why it's important to manage storage on your computer? Well, let me explain. Think of your computer as a big closet where you store all your stuff. As time goes on, you accumulate more and more things, and before you know it, the closet is overflowing! That's where storage management comes in - it's like tidying up that closet to make sure everything fits well, is easy to find, and nothing goes to waste. Plus, it can help prevent data loss and even recover lost or damaged files.

In the Linux World, several tools help with storage management such as:

* NFS (Network File System)
    
* autofs (Automounter File System)
    
* LVM(Logical Volume Manager)
    

## **NFS - NETWORK FILE SYSTEM**

Imagine having a bunch of closets in your house, and you want to get clothes from any of them without having to physically move them around. That's where NFS comes in - it's like a magic tunnel that connects all these closets, letting you reach into any of them from wherever you are.

In this case, the closets represent computers or servers, the clothes represent files and data, and NFS is the magic tunnel that lets you share and access these files smoothly. It's a distributed file system protocol that makes it possible for a user on a client computer to access files over a network as if those files were right there on the client.

Think of NFS as a helpful storage manager for your computers, setting up a central storage space where you can share files with others. This makes it easier to organize and use files without having to make extra copies. With NFS, computers can grab files only when they need them, saving space.

NFS is compatible with different types of computers and allows you to add more storage space with ease. So you could say that NFS is like a smart organizer that makes file sharing and storage more straightforward and efficient for everyone.

### **Basic steps and commands for setting up NFS on a Linux System -**

1. ***Install NFS packages :***
    
    ```bash
    # Install NFS server (on the machine with files to share)
    sudo apt-get install nfs-kernel-server  # For Debian/Ubuntu
    sudo yum install nfs-utils              # For Red Hat/CentOS
    
    # Install NFS client (on the machine that needs access)
    sudo apt-get install nfs-common         # For Debian/Ubuntu
    sudo yum install nfs-utils              # For Red Hat/CentOS
    ```
    
2. ***Edit the***`/etc/exports`***file on the server to define the directories you want to share and the permissions for the client(s).***
    
    ```bash
    sudo vi /etc/exports
    #example --- 
    #/path_to_shared_directory client_ip(rw,sync)
    #replace client_ip with the IP address of your NFS client
    #rw allows the client to read and write to the shared directory
    #sync : Ensures changes are immediately written to the disk
    ```
    

Save the file and restart the NFS server using command `sudo systemctl restart nfs`

3. ***Mount NFS share on Client*** :
    
    Create a directory on the client to mount the NFS share.
    
    ```bash
    sudo mkdir /mnt/nfs_share
    ```
    
    Mount the NFS share on the client.
    
    ```bash
    sudo mount -t nfs server_ip:/path/to/shared/directory /mnt/nfs_share
    ```
    
    Replace `server_ip` with the IP address of your NFS server.
    
4. ***Automate Mounting***
    
    * To automatically mount the NFS share on the client at boot, you can add an entry to the `/etc/fstab` file.
        
    
    ```bash
    sudo nano /etc/fstab
    ```
    
    Add the following line:
    
    ```bash
    server_ip:/path/to/shared/directory /mnt/nfs_share nfs defaults 0 0
    ```
    
    Save the file and run:
    
    ```bash
    sudo mount -a
    ```
    

## **autofs - AUTOMOUNTER FILE SYSTEM**

Let me explain to you how autofs works using an analogy of a closet of clothes. Just like your closet, you have various clothing items that are neatly organized and accessible when needed. You don't keep every piece of clothing you own out in the open all the time because it could be overwhelming. Instead, you have a system to access the clothes you need just when you need them.

Autofs is a system typically used in Unix-like operating systems that automatically mounts file systems on demand. In this analogy, clothing items represent file systems or storage devices in a computing environment. Each type of clothing item in your closet can be compared to different file systems. For instance, you might keep your summer clothes separate from your winter clothes and work clothes. Similarly, autofs can be configured to mount different file systems when they are needed.

Just like your closet, the file system hierarchy in a computer organizes data into directories and subdirectories. When you need a specific piece of clothing, you go to the relevant section of your closet to access it. Similarly, when a program or user needs access to a particular file system, autofs automatically mounts that file system on demand.

You don't keep all your clothes out in the open all the time, and you only access them when you need them. Likewise, autofs mounts file systems only when they are accessed, reducing the need for constant, manual mounting. Mounting just means making the content of a storage device, such as a hard drive or network share, accessible at a certain point in the file system hierarchy. It's simply a process of integrating the file system into the existing directory structure.

Lastly, just as keeping all your clothes out in the open would be inefficient and cluttered, mounting all file systems at once can be resource-intensive. autofs helps optimize resources by mounting file systems only when they are necessary.

### **Basic steps and commands for setting up autofs on a Linux System -**

1. ***Install autofs:***
    
    ```bash
    sudo dnf install autofs -y
    ```
    
2. ***Edit*** `auto.master` ***Configuration File:***
    
    ```bash
    sudo vi /etc/auto.master
    ```
    
    * Open the `auto.master` configuration file for editing.
        
        The `/etc/auto.master` file specifies the location of the master map file that contains information about automount points.Each line in the `/etc/auto.master` file typically represents an automount point. The format is:
        
        ```plaintext
        mount-point   map-file   options
        ```
        
        * `mount-point`: The directory where the automounted filesystem will appear.
            
        * `map-file`: The path to the map file that contains the details of the filesystem to be mounted.
            
        * `options`: Additional options for configuring automount behavior.
            
              
            Add a line specifying the master map file and the mount points.
            
3. ***Restart autofs:***
    
    ```bash
    sudo systemctl restart autofs
    ```
    
    * Restart the `autofs` service to apply the changes.
        
4. ***Access the Mount Point:***
    
    ```bash
    cd /path/to/mount/point
    ```
    
    * Access the specified mount point. `autofs` will automatically mount the associated file system when you access this mount point for the first time.
        
5. ***Verify Mounted File Systems*:**
    
    ```bash
    mount | grep autofs
    ```
    
    * Check if the file systems are mounted.
        
6. ***View autofs Logs (if needed):***
    
    ```bash
    cat /var/log/syslog
    ```
    
    * View system logs for any potential issues.
        

## LVM (Logical Volume Management) -

Managing your computer's storage can seem complicated, but with Logical Volume Management (LVM), it's like managing a pizza! Think of each slice of pizza as a "Physical Volume" (PV) that represents an actual segment of your storage devices. You can combine several slices to create a "Volume Group" (VG) that forms a unified storage space with diverse physical slices (PVs). Inside this VG, you can create customizable sections called "Logical Volumes" (LVs), which are like individual pizza slices allocated for specific purposes, such as your operating system or personal files.

One of the best things about LVM is that you can dynamically extend or shrink these LVs, similar to adjusting the size of pizza slices to accommodate changing needs. LVM also offers "snapshots" which allow you to capture the state of your LVs before making alterations, much like taking a quick picture of your pizza before modifying it. This flexibility enables easy management and allocation of storage - just like resizing and rearranging pizza slices without causing a mess!

In the pizza analogy of Logical Volume Management (LVM):

1. **Pizza:**
    
    * Represents your computer's overall storage.
        
2. **Pizza Slices:**
    
    * Correspond to "Physical Volumes" (PVs), representing actual segments of storage devices like hard drives.
        
3. **Group of Pizza Slices:**
    
    * Forms a "Volume Group" (VG), creating a unified storage space composed of different physical slices (PVs).
        
4. **Individual Pizza Slice:**
    
    * Represents a "Logical Volume" (LV), a customizable storage section that can be allocated for specific purposes, such as operating systems or personal files.
        
5. **Resizing Pizza Slices:**
    
    * Describes the ability to dynamically extend or shrink "Logical Volumes" (LVs), adjusting their size to meet changing storage needs.
        
6. **Pizza Slice Snapshots:**
    
    * Analogous to LVM snapshots, which allow you to capture the state of Logical Volumes (LVs) before making alterations, providing a way to revert to a specific state if needed.
        

### **Basic steps and commands for setting up LVM on a Linux System -**

1. ***Install LVM tools:***
    
    * Use your package manager to install the LVM tools. On a system using `apt` (Debian/Ubuntu), the command is:
        
        ```bash
        sudo apt-get install lvm2
        ```
        
2. ***Initialize Physical Volumes (PVs)*:**
    
    * Identify the storage devices you want to include in the LVM setup and initialize them as Physical Volumes. Replace `/dev/sdX` with your actual device.
        
        ```bash
        sudo pvcreate /dev/sdX
        ```
        
3. **Create a Volume Group (VG):**
    
    * Combine the initialized Physical Volumes into a Volume Group. Replace `my_vg` with your desired Volume Group name.
        
        ```bash
        sudo vgcreate my_vg /dev/sdX
        ```
        
4. ***Create Logical Volumes (LVs):***
    
    * Within the Volume Group, create Logical Volumes specifying their size and purpose. Replace `my_lv` with your desired Logical Volume name and adjust the size accordingly.
        
        ```bash
        sudo lvcreate -L 20G -n my_lv my_vg
        ```
        
5. ***Format the Logical Volume:***
    
    * Format the newly created Logical Volume with a filesystem of your choice. Replace `/dev/my_vg/my_lv` and `ext4` with your LV path and preferred filesystem.
        
        ```bash
        sudo mkfs.ext4 /dev/my_vg/my_lv
        ```
        
6. ***Mount the Logical Volume:***
    
    * Create a mount point and mount the Logical Volume to integrate it into the file system. Replace `/mnt/my_mount` with your desired mount point.
        
        ```bash
        sudo mkdir /mnt/my_mount
        sudo mount /dev/my_vg/my_lv /mnt/my_mount
        ```
        
7. ***Update /etc/fstab for Persistent Mounting (Optional):***
    
    * Add an entry to `/etc/fstab` to ensure the Logical Volume is mounted persistently at boot. Edit the file using a text editor.
        
        ```bash
        sudo nano /etc/fstab
        ```
        
        Add a line like:
        
        ```bash
        /dev/my_vg/my_lv  /mnt/my_mount  ext4  defaults  0  0
        ```
        
        The line `/dev/my_vg/my_lv /mnt/my_mount ext4 defaults 0 0` is an entry in the `/etc/fstab` file, which is a system file in Linux used to control how disks and partitions are mounted and how they should be treated by the system at boot time.
        
    * Let's break down each part of this line:
        
        1. `/dev/my_vg/my_lv`**:**
            
            * This specifies the block device (in this case, a Logical Volume) that you want to mount. `/dev/my_vg/my_lv` points to the Logical Volume named `my_lv` in the Volume Group named `my_vg`.
                
        2. `/mnt/my_mount`**:**
            
            * This is the directory where you want to mount the specified block device. In this case, it's the directory `/mnt/my_mount`.
                
        3. `ext4`**:**
            
            * This specifies the filesystem type of the block device. In this case, it's using the ext4 filesystem. It's important to match this with the filesystem type you used when creating the Logical Volume.
                
        4. `defaults`**:**
            
            * This field includes mount options. The term "defaults" is a shorthand for a standard set of mount options. It typically includes options like read-write access and other default behaviors.
                
        5. `0`**:**
            
            * This field is used by the `dump` command to determine whether the filesystem should be backed up. A value of `0` means no backup.
                
        6. `0`**:**
            
            * This field is used by the `fsck` command to determine the order in which filesystem checks are performed at boot time. A value of `0` means that the filesystem is not checked.
                
8. ***Extend or Resize (Optional):***
    
    * If needed, you can dynamically extend the Logical Volume. Replace `+10G` with your desired size increment.
        
        ```bash
        sudo lvextend -L +10G /dev/my_vg/my_lv
        ```
        
9. ***Resize the Filesystem (Optional):***
    
    * Resize the filesystem to utilize the newly allocated space.
        
        ```bash
        sudo resize2fs /dev/my_vg/my_lv
        ```
        

Managing storage on a computer is crucial for maintaining efficiency, accessibility, and data integrity. With tools like NFS, autofs, and LVM in the Linux world, we users can effectively organize and utilize their storage resources to meet our needs.

I hope you found this blog helpful. Please feel free to share any feedback or suggestions.