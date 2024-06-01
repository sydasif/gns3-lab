# Setting Up a Network Lab with GNS3

This blog provides a comprehensive guide to setting up a virtual network lab using GNS3 on a Windows PC. It covers the installation of GNS3 and its virtual machine (GNS3 VM) within VMware and the integration of an Ubuntu 22 Server. The guide is aimed at network enthusiasts, students, and professionals seeking hands-on networking experience.

**Key Components**:

1. **GNS3**: A powerful network simulation tool.
2. **VMware**: Used for virtualization.
3. **Ubuntu Server**: For practical application testing.

The blog details the process of creating a cohesive environment that connects a Windows host, GNS3, and Ubuntu Server, enabling seamless interaction and experimentation. The goal is to provide readers with the knowledge to construct a robust and functional network testing environment on their Windows PC.

### Prerequisites for Setting Up a GNS3 Network Lab

Before setting up your virtual network lab with GNS3 on a Windows PC, ensure you have the following prerequisites:

1. **Windows PC Requirements**: Confirm your PC meets the necessary RAM, CPU, and disk space requirements for GNS3 and VMware. [Details here](https://docs.gns3.com/docs/getting-started/installation/windows/#minimum-requirements).
2. **GNS3 and GNS3 VM**: Install GNS3 and its VM in VMware. Download from the [GNS3 website](https://www.gns3.com/) and follow the [installation instructions](https://docs.gns3.com/docs/getting-started/installation/windows/#introduction). Helpful videos: [Video 1](https://www.youtube.com/watch?v=x9pGYyEqLYs), [Video 2](https://www.youtube.com/watch?v=lFEDmM_lsxI).
3. **Ubuntu 22 Server**: Install Ubuntu 22 Server in VMware, ensuring it has sufficient resources for optimal performance. Helpful [Video](https://youtu.be/Lj5qHBjSfMo?si=I5YZmZqnNqUKGdoP)

With these components in place, you're ready to proceed.

## Starting the Lab Environment

With the prerequisites in place, it's time to kick off the virtual network lab environment. This section outlines the steps to start GNS3, launch the GNS3 VM in VMware, and initiate the Ubuntu 22 Server.

Open GNS3 on your Windows PC. Navigate through any initial setup wizards or configurations if prompted. Once the GNS3 interface is accessible, you're ready to proceed.

Ensure that VMware is on, locate the GNS3 VM and check GNS3 VM to establish the connection between GNS3 GUI and the virtual machine.

![fig-1](/images/gns3-vm.PNG)

### Configuring Ubuntu Server

In this section, we'll configure the Ubuntu Server within your virtual lab environment. Follow these steps to set up a second network adapter for the Ubuntu Server in VMware:

#### Add Network Adapter

1. Open VMware and locate the Ubuntu Server virtual machine.
2. Ensure the VM is powered off.
3. Right-click on the VM and select "Settings."
4. In the VM Settings window, navigate to the "Network Adapter" tab and add a second adapter.
5. Change the network connection mode to `Vmnet2`.
6. Click OK to save the changes.

![fig-2](/images/network-adopter.PNG)

#### Start Ubuntu Server

1. Power on the Ubuntu Server virtual machine in VMware.
2. Allow the VM to boot up and log in using the credentials you configured during the Ubuntu setup.

#### Verify Network Configuration

1. Open a terminal in Ubuntu.
2. Configure static IP addresses on both network adapters by editing the netplan configuration:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

Example netplan configuration:

```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:  # NAT Adapter
      addresses:
        - 192.168.71.100/24
      gateway4: 192.168.71.2
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
    ens37:  # Vmnet2 Adapter
      addresses:
        - 172.16.10.10/24
  version: 2
```

3. Apply the netplan configuration:

```bash
sudo netplan apply
```

4. Use the following command to check the network interfaces:

```bash
ip addr show
```

Example output:

```bash
zolo@u22s:~/gns3-lab$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:01:09:c2 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.71.100/24 brd 192.168.71.255 scope global ens33
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe01:9c2/64 scope link 
       valid_lft forever preferred_lft forever
3: ens37: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:01:09:cc brd ff:ff:ff:ff:ff:ff
    altname enp2s5
    inet 172.16.10.10/24 brd 172.16.10.255 scope global ens37
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe01:9cc/64 scope link 
       valid_lft forever preferred_lft forever
```

Ensure that the network interfaces are associated with IP addresses and are in the "UP" state.

By adding a network adapter, you enable communication between the Ubuntu Server and your GNS3 setup, facilitating connectivity with other devices in the virtual lab. With the network settings in place, proceed to Section 4, where we'll guide you through adding a cloud node to GNS3 and selecting your network.
