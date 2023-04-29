# Setting up FusionCompute as a Learning & Testing Platform

This repository describes how to install FusionCompute on a common PC for testing or learning purposes.

* Machine Number: 7
* Machine Hostname: Isetcom07
* Host Machine IP: 172.16.16.207/24
* CNA01 IP: 172.16.16.11/24
* VRM IP: 172.16.16.12/24
* Default Gateway: 172.16.16.1

# What is Huawei's FusionCompute Solution?

![image](https://user-images.githubusercontent.com/91763346/235259300-c1224691-639e-4abf-87b1-edb26165f21b.png)

FusionCompute is virtualization software deployed on physical servers to virtualize server resources, including CPUs, memory, and network interface cards (NICs).
In other words, FusionCompute enables a physical server to simultaneously run multiple isolated VM execution environments, improving resource utilization, meeting flexible and dynamic resource allocation requirements for applications.

# Setup Overview

![image](https://user-images.githubusercontent.com/91763346/235259639-416cfd49-c080-4b4b-8884-6684ed7468db.png)

The process is simple, it basically consists of Installing a Linux OS on one or more common PCs, enabling KVM in the kernel and finally running the FusionCompute
CNA and VRM nodes on the common PCs as VMs.

## Requirements

![image](https://user-images.githubusercontent.com/91763346/235259841-f8a07411-3977-4818-b777-d3ccf3607c14.png)

This guide will only have the **second method** of installation and describes how to setup, install and run FusionCompute.

## Installation

![image](https://user-images.githubusercontent.com/91763346/235260064-9d84db0a-ab43-493f-abef-89936f7986ee.png)

## Prerequisites & Must-have tools

![image](https://user-images.githubusercontent.com/91763346/235260157-bbe61ca7-f662-487b-a673-61826412665b.png)

This please refer to huawei's website for more info regarding the iso files.
This is a list of a checklist:
* Enable virtualization both PCs
* Disabling Secure Boot and setting up boot mode to UEFI ( might come in handy when installing the host ubuntu OS )
* Have a USB installer for Ubuntu
* Having a ready to use Ubuntu Machine

# Setting up the host machine 

The first step that needs to be done is **upgrading** the machine and in our case it's a Ubuntu 18 host.

```
$ sudo apt update && sudo apt upgrade -y
```
## Checking if our CPU supports virtualization
FusionCompute basically runs on virtualization to work and to see if it's working, we can simply run the following cmd

```
$ egrep –c '(svm|vmx)'/proc/cpuinfo
```
 If the result is 0, the CPU does not support virtualization. If the result is
greater than or equal to 1, the CPU supports virtualization.

In our case the output is 6.

## Installing KVM software packages

Kernel-based Virtual Machine (KVM) is a software feature that you can install on physical Linux machines to create virtual machines. A virtual machine is a software application that acts as an independent computer within another physical computer.

We just need to run the following command

```
$ sudo apt install qemu qemu-kvm libvirt-bin bridge-utils virt-manager
```
Upon completion, we just need to restart the machine.

```
reboot
```

# Setting up Virtual Machine Manager to Configure Storage and Bridge Networks


## Setting up the NIC
In order to get FusionCompute to work, we need to have the 3 machines on the **same** network

![image](https://user-images.githubusercontent.com/91763346/235262699-401ca98d-9ef3-499e-8b95-15fd25e5643a.png)

Now we need to configure the interface and set it to **bridge** so that it can be used and seen by the CNA01 machine (the one we're creating)

![image](https://user-images.githubusercontent.com/91763346/235263522-04d775c0-049a-4542-a7a0-c558f7484ab0.png)

In my case I've used :
Interface: **enp0s04**
IP: 172.16.16.207 (Lab Machine #7)

![image](https://user-images.githubusercontent.com/91763346/235263697-fbcb7285-4a97-413b-9c7d-7382ee812c0e.png)

## Creating the Storage Pool

This step was straight forward.
![image](https://user-images.githubusercontent.com/91763346/235263791-86a7a7fc-fe97-4d45-ba1c-7e539edb5c74.png)

Now we just have to set the Folder that contains the ISO files for FusionCompute CNA and FusionCompute VRM files.

![image](https://user-images.githubusercontent.com/91763346/235263880-3d5dea1a-b784-44a1-9c90-eff5fb8eb9a0.png)

The result would look something like this:

![image](https://user-images.githubusercontent.com/91763346/235263983-4b0de247-9e8e-4c2c-90da-0bbc8dc7fe77.png)

# Installing FusionCompute CNA01 Machine

In this step we're going to create a VM from the FusionCompute CNA ISO file.

![image](https://user-images.githubusercontent.com/91763346/235264160-02d0ff54-38d3-4173-b9bf-50d8d06824b6.png)

Now we have to load the iso file.

![image](https://user-images.githubusercontent.com/91763346/235264219-c459fb4e-3fd6-406d-91d6-73ecbd9a9982.png)

## Configuring RAM & CPU settings

![image](https://user-images.githubusercontent.com/91763346/235264466-7708cbce-3fb9-453c-9dec-6054f3173d34.png)

We're advised to use **>=4096Mb** of RAM and **3** CPU cores

## Configuring Disk Storage

This disk partition will work as the main Drive for the machine and we're advised to use **>=200.0 GB** 

![image](https://user-images.githubusercontent.com/91763346/235264922-3b58e7c1-8b27-4bb3-88d7-a43a8da54359.png)

Now we just need to specify the Storage Volume Location

![image](https://user-images.githubusercontent.com/91763346/235264936-80652a5e-aa39-4289-a563-416c6e927b34.png)

Now the next step would be to resume the installation of the FusionCompute CNA01 and for that we can just select the **custom** **storage** 

![image](https://user-images.githubusercontent.com/91763346/235265107-e969f3fa-b100-4984-8ff1-1801d796993a.png)

In the next step we need to change the machine name to **CNA01** and tick the **Customize Configuration before install**

![image](https://user-images.githubusercontent.com/91763346/235265379-1000e300-677f-46d2-bd80-0f0d4d804957.png)

## Changing the CPU Model
As seen on Huawei's guide, we need to set the CPU model to **host-passthrough** or else FusionCompute won't work properly

![image](https://user-images.githubusercontent.com/91763346/235265536-eba1e7f3-8f0e-4cd2-b5fe-d49d7e1ef7ca.png)

## Changine the Memory Settings

We need to check that the memory is **>=7168MB** for CNA to work properly

![image](https://user-images.githubusercontent.com/91763346/235265956-e0911b7f-a2f5-4d10-bf67-4608677547a5.png)

## Changing Disk Settings

In a nutshell, VirtIO's main purpose is to send and receive network data to and from the host. In other words, let virtio-net be a liaison for network data between the host and the guest. 

![image](https://user-images.githubusercontent.com/91763346/235266010-b304260e-e03c-464e-afd2-66ac8505d849.png)

So I did set the Disk Bus to **VirtIO** for better performance

## Changing the NIC Bridge Settings

![image](https://user-images.githubusercontent.com/91763346/235266452-539c3195-ce35-4c60-b245-75c9727d273a.png)

To have better performance and no errors, we're advised to use :
* Network Source: Bridge brx (the bridge NIC that we configured earlier) 
* Device mode: e1000

## Starting the CNA01 Installation

![image](https://user-images.githubusercontent.com/91763346/235266598-4d20e706-fbd8-4bd3-9360-9fdf3d27d9d0.png)

After verifying all the settings and requirements we're ready to start the installation.

## Customizing the Configuration during installation

The first thing that needs to be configured is:
* Network:

![image](https://user-images.githubusercontent.com/91763346/235266842-97bb0f05-156d-42c3-826f-a831d3573370.png)

We need to set the IP to a non-leased IP and for that I chose : 172.16.16.11 because the other network machines are use this format : 172.16.16.2xx.
The default gateway also needs to be set: 172.16.16.1

* Hostname:

![image](https://user-images.githubusercontent.com/91763346/235269774-7256e5e1-1b60-42fc-9728-1bd797ed8667.png)

We're going to use CNA01 as hostname for that machine

* Password
![image](https://user-images.githubusercontent.com/91763346/235269852-89c35356-3cd1-44d7-8926-4749e16cfbaf.png)

We're going to setup a machine password to protect it and it's resources.

Now after making sure that all the above is valid and is configured, we can start the installation by clicking **F12**.

![image](https://user-images.githubusercontent.com/91763346/235269938-47391c81-7be0-4d4c-bd16-81c87b2700ce.png)

## Logging in and Checking network Connectivity

![image](https://user-images.githubusercontent.com/91763346/235270242-f634dbea-aad3-4444-bdd4-cfa58d962d56.png)

After the installation ends, we should be able to login and check the configuration.
* USER: root
* Pass: isetcom07! 
Remember to Always use a secure password!  
Because this is a testing lab I used a simple one.

We proceed to ping the default gateway that we configured earlier.
``` 
$ ping 172.16.16.1
64 bytes from 172.16.16.1: icmp_seq=1 ttl=64 time=0.68ms
64 bytes from 172.16.16.1: icmp_seq=2 ttl=64 time=1.26ms
64 bytes from 172.16.16.1: icmp_seq=3 ttl=64 time=1.16ms
^C
```

# Installing the VRM

* VRM IP: 172.16.16.12
* VRM Gateway: 172.16.16.1

![image](https://user-images.githubusercontent.com/91763346/235271555-3c3f5116-3748-44d4-89be-09819f9f3a16.png)

We just need to repeat all the previous steps but using the FusionCompute VRM file.

# (Optional) Installation and Configuration on PC2

This step is optional but the steps are:
* Installing Ubuntu.
* Installing and Configuring KVM.
* Installing CNA02 (same way as before).
* Connecting PC1 and PC2 on the same network.

# Testing FusionCompute

Now after verifying connectivity, we can proceed to install the NFS server on our host PC1.
NFS (Network File System) allows a system to share directories and files with others over a network. By using NFS, users and programs can access files on remote systems almost as if they were local files.

## Installing NFS Server on Host PC1

![image](https://user-images.githubusercontent.com/91763346/235270980-18d5aedc-4ec6-4206-9853-dea29d1416b5.png)

We just need to run: 

```
$ sudo apt install nfs-kernel-server
$ sudo apt install vim
```

Now we need to make a shared directory:

```
~$ mkdir nfs_vm
~$ cd nfs_vm
~/nfs_vm$ pwd
```

Now we need to edit the /etc/exports to configure the NFS configuration and we just need to append the following line:
```
/home/isetcom07/nfs_vm *(rw,sync,no_subtree_check,no_root_squash)
```

## Restarting nfs-kernel-server daemon

For the configuration change to take effect, we need to restart the nfs-kernel-server

```
$ sudo systemctl restart nfs-kernerl-server
$ sudo systemctl enable nfs-kernerl-server
```

# Accessing the Management Interface

![31](https://user-images.githubusercontent.com/91763346/235271694-e00ac788-651c-4849-9360-ea471b0d52c4.PNG)

In our case the IP of the VRM is: 172.16.16.12

If everything runs perfectly, we should be able to access the VRM via **https://172.16.16.12*** .

We can use the following creds:
* Login: admin
* password: IaaS@PORTAL-CLOUD8!

# How to use the NFS Server on FusionCompute

![image](https://user-images.githubusercontent.com/91763346/235271948-253b7153-67cc-4f50-ade1-27e3644ad0c6.png)


We just need to use the same step in which we did configure Storage and Bridge Networks.







