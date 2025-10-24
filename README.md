# Multi-EC2 EFS Setup for Project1 (Dev) & Project2 (Prod)

This project demonstrates how to mount a **single AWS EFS (Elastic File System)** across **multiple EC2 instances** for two different environments: Project1 (Development) and Project2 (Production). Each project uses a separate directory in the same EFS file system to ensure isolation and proper permissions.

EFS directories and mount points:

| EFS Directory       | Project / Environment          | EC2 Mount Point       |
|--------------------|-------------------------------|---------------------|
| `/project-dev`      | Project1 / Development (Dev)  | `/mnt/project-dev`  |
| `/project-staging`     | Project2 / staging (Stag)  | `/mnt/project-staging` |

---

## Step 1: Install NFS Client on EC2 ubuntu

```bash
sudo apt-get update -y
sudo apt-get install -y nfs-common
```
----
## Step 2: Create Mount Points
## Create local directories on EC2 to mount the EFS directories:

``` bash
sudo mkdir -p /mnt/project-dev
sudo mkdir -p /mnt/project-staging
```

-----
## Step 3: Mount EFS Directories
## Mount the EFS directories to the local mount points:
```bash
sudo mount -t nfs4 -o nfsvers=4.1 fs-0b9bb01540dc6fc3a.efs.ap-south-1.amazonaws.com:/project-dev /mnt/project-dev
sudo mount -t nfs4 -o nfsvers=4.1 fs-0b9bb01540dc6fc3a.efs.ap-south-1.amazonaws.com:/project-staging /mnt/project-staging




