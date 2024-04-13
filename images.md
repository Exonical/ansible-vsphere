# Setting Up a VNFS with Warewulf 4

This guide provides detailed instructions on how to prepare, import, and manage a VNFS container in Warewulf 4.

## Prerequisites

Before beginning, ensure you have:

- A machine running Rocky Linux 8
- Warewulf 4 installed and properly configured on the master node.
- Sudo privileges for the user performing these actions.

## Step 1: Prepare the VNFS Directory

Create a directory that will serve as the root filesystem for your VNFS:

```bash
sudo mkdir -p /var/warewulf/chroots/rocky8
```

## Step 2: Bootstrap the Directory

Install a minimal base system into the directory using dnf:

```bash
sudo dnf --installroot=/var/warewulf/chroots/rocky8 --releasever=8 groupinstall "Minimal Install" -y
sudo dnf --installroot=/var/warewulf/chroots/rocky8 clean all
```

## Step 3: Register the VNFS Container with Warewulf

Import the prepared directory into Warewulf as a VNFS container:

```bash
sudo wwctl container import /var/warewulf/chroots/rocky8 rocky8
```

This command registers the directory /var/warewulf/chroots/rocky8 with the identifier rocky8.

## Step 4: Chroot and Configure the Environment

To further configure the environment, you will need to chroot into the directory:

```bash
sudo chroot /var/warewulf/chroots/rocky8
```

Once inside, you can install additional software and make other configurations.
### Install Mellanox OFED for InfiniBand Support

1. Download the Mellanox OFED Repository Setup Package:

    You can find the latest version of the repository setup package on the Mellanox website or use a specific command to download it directly. Replace the link with the appropriate version for your system:

    ```bash
    wget https://linux.mellanox.com/public/repo/mlnx_ofed/latest-24.01/rhel8.9/mellanox_mlnx_ofed.repo -P /etc/yum.repos.d/

    dnf install rdma-core libibverbs-utils infiniband-diags perftest -y
    ```

2. Exit the Chroot:

    After completing the installations and configurations, exit the chroot environment:

    ```bash
    exit
    ```

## Step 5: Build the VNFS

Build the VNFS container to prepare it for deployment:

```bash
sudo wwctl container build rocky8
```
## Step 6: Assign the VNFS to Nodes

Assign the newly built VNFS to the nodes in your cluster:

```bash
sudo wwctl node set --container=rocky8 <node-name>
sudo wwctl provision update <node-name>
```

Replace <node-name> with the actual names of the nodes that you want to provision.

## Step 7: Verify the Import and Build

Confirm that the VNFS container has been successfully imported and is available for use:

```bash
sudo wwctl container list
```

This command should list rocky8 among the containers, indicating it is ready for use.
