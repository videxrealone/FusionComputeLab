# Setting up FusionCompute as a Learning & Testing Platform
This repository describes how to install FusionCompute on a common PC for testing or learning purposes.

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

