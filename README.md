# Multi-EC2 EFS Setup for Project1 (Dev) & Project2 (staging)

This project demonstrates how to mount a **single AWS EFS (Elastic File System)** across **multiple EC2 instances** for two different environments: Project1 (Development) and Project2 (staging). Each project uses a separate directory in the same EFS file system to ensure isolation and proper permissions.

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

---
## Step 2: Create directories in EFS

Mount EFS temporarily on any EC2 (or use the EFS console):
```bash
sudo mkdir -p /mnt/efs-temp
sudo mount -t nfs4 -o nfsvers=4.1 fs-0b9bb01540dc6fc3a.efs.ap-south-1.amazonaws.com:/ /mnt/efs-temp

sudo mkdir -p /mnt/efs-temp/project-dev
sudo mkdir -p /mnt/efs-temp/project-staging

sudo umount /mnt/efs-temp
```
---
## Now your EFS has:
```bash
/project-dev
/project-staging
```


## Step 3: Create Mount Points
## Create local directories on EC2 to mount the EFS directories:

``` bash
sudo mkdir -p /mnt/project-dev
sudo mkdir -p /mnt/project-staging
```

-----
## Step 4: Mount EFS Directories
## Mount the EFS directories to the local mount points:
```bash
sudo mount -t nfs4 -o nfsvers=4.1 fs-0b9bb01540dc6fc3a.efs.ap-south-1.amazonaws.com:/project-dev /mnt/project-dev
sudo mount -t nfs4 -o nfsvers=4.1 fs-0b9bb01540dc6fc3a.efs.ap-south-1.amazonaws.com:/project-staging /mnt/project-staging
```
---
## Verify that the directories are mounted:
```bash
df -h | grep efs
```
## Step 5: Set Permissions
## Set ownership and permissions so users can read/write to both directories:
```bash
sudo chown -R ubuntu:ubuntu /mnt/project-dev
sudo chmod -R 775 /mnt/project-dev
sudo chown -R ubuntu:ubuntu /mnt/project-staging
sudo chmod -R 775 /mnt/project-staging

```
## Step 6: Configure Auto-Mount on Reboot
## Add the following lines to /etc/fstab to mount EFS automatically after reboot:

``` bash
fs-0b9bb01540dc6fc3a.efs.ap-south-1.amazonaws.com:/project-dev /mnt/project-dev nfs4 defaults,_netdev 0 0
fs-0b9bb01540dc6fc3a.efs.ap-south-1.amazonaws.com:/project-prod /mnt/project-stgaing nfs4 defaults,_netdev 0 0
```

## Test fstab:
``` bash
sudo mount -a
```
## Step 7: Verify Access Across EC2 Instances

## Test that files written from one EC2 are visible on another EC2:
```bash
# Write test file for Dev environment
echo "Hello from Dev EC2" > /mnt/project-dev/test.txt

# Read test file from another Dev EC2
cat /mnt/project-dev/test.txt

# Write test file for staging environment
echo "Hello from staging EC2" > /mnt/project-staging/test.txt

# Read test file from another staging EC2
cat /mnt/project-staging/test.txt
```

Step 8: Notes & Best Practices

Use directory-level isolation for Dev and stging environments.

Ensure security groups allow NFS traffic (TCP 2049).

Test file sharing across multiple EC2 instances.

For multiple users, create a shared group with write permissions.

Consider a startup script to automatically mount EFS on new EC2 instances.


## you guys use also access point ** Using EFS Access Points (better permissions)
