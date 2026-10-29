# Shared Storage Between Two EC2 Servers using Amazon EFS

## Problem
A website runs on two servers. A file uploaded on server 1 is not visible on server 2, so the site breaks. Both servers need the same shared folder.

## Solution
Use Amazon EFS (Elastic File System), a shared network file system that can be mounted on many servers at the same time.

## Architecture
Server-1 and Server-2 (EC2, security group `ec2-sg`) both mount one EFS (security group `efs-sg`).

## Security group rules

| Group | Type | Port | Source |
|---|---|---|---|
| ec2-sg | SSH | 22 | My IP |
| efs-sg | NFS | 2049 | ec2-sg |

Port 2049 is the NFS port used by EFS. Using `ec2-sg` as the source means only my two servers can reach the EFS.

## Steps
1. Created security groups `ec2-sg` and `efs-sg`
2. Created an EFS file system and attached `efs-sg` to its mount targets
3. Launched two Amazon Linux EC2 instances with `ec2-sg`
4. Installed the EFS tool on both servers:
   `sudo yum install -y amazon-efs-utils`
5. Created the mount folder: `sudo mkdir /efs`
6. Mounted the EFS: `sudo mount -t efs <file-system-id>:/ /efs`
7. Gave write permission to `ec2-user` (server-1): `sudo chown ec2-user:ec2-user /efs`

## Testing

EFS mounted on the server:

<img width="1364" height="767" alt="image" src="https://github.com/user-attachments/assets/a6b4f3e9-c215-4e41-b6d9-42c9cdf37ca2" />


File created on server-1:

<img width="545" height="127" alt="image" src="https://github.com/user-attachments/assets/8033296f-f6fc-4c60-989a-f2dca62d221c" />


Same file read on server-2:

<img width="1365" height="760" alt="image" src="https://github.com/user-attachments/assets/4a93101f-7830-4126-bf4e-d1ecb021062a" />


## What I learned
- How EFS gives shared storage to multiple servers
- How NFS port 2049 and security group referencing control access
- How to mount a network file system on Linux
- How file ownership (`chown`) works

## Next improvement
- Make the mount permanent after reboot using `/etc/fstab`

## Tools
AWS (EC2, EFS, Security Groups), Linux
