# Mounting and Making an EBS Volume Persistent
First, understand this:
EC2 = a virtual computer (server)

EBS = a hard disk for that computer
# Mounting means connecting a hard disk to a folder so the operating system can use it.

Example:

In Windows, when a drive appears as D: or E:

In Linux, it appears as a folder like /data

After mounting:

Anything saved in that folder is stored on the EBS volume.

# What is Persistence?
Persistence means the data and disk connection remain available even after a reboot.

Important point:

If an EBS volume is mounted manually, it will disconnect after reboot

To avoid this, we make it persistent

# Why Persistence is Needed?
Without persistence:

Server reboot → volume unmounted → application fails

With persistence:

Server reboot → volume automatically mounted → application works normally
# Real-World Use Cases
Databases (MySQL, PostgreSQL)

Application data

Logs

Docker and Jenkins data

# AWS EC2 EBS Volume Setup (Persistent Storage)
🔧 Step 1: Launch EC2 Instance
An EC2 instance was launched in the same Availability Zone where the EBS volume was created to ensure compatibility, as EBS volumes can only be attached to instances within the same zone.
![instance1](https://github.com/user-attachments/assets/ceb95dbf-d080-48f7-b105-ad00f0d188ee)

### Step 2: Create and Attach EBS Volume
1. Go to the EC2 Dashboard in AWS Console.
2. In the left panel, click Elastic Block Store > Volumes.
3.Click Create Volume.
Fill in:
Size (e.g., 10 GiB)
4.Availability Zone (Must match your EC2 instance)
Leave other options default
5.Click Create Volume.
![volume1](https://github.com/user-attachments/assets/33c0917c-c7ac-49ef-9985-053503f8b852)

## 🔗 Step 2: Attach Volume to EC2 Instance

Select the newly created EBS volume from the AWS EC2 console.
Click **Actions → Attach volume**.
Choose the target EC2 instance.

Specify the device name as **/dev/sdf** during attachment.

Note: Since the EC2 instance is NVMe-based, the attached EBS volume is exposed to the operating system as **/dev/nvme1n1** instead of the specified device name.

Click **Attach** to complete the process.
![WhatsApp Image 2026-02-08 at 10 19 28 AM](https://github.com/user-attachments/assets/f16f67b7-5b24-4609-b750-d4bd367b3ccd)
![WhatsApp Image 2026-02-08 at 10 24 48 AM](https://github.com/user-attachments/assets/c31b1cc2-541c-405b-a39a-7d3d0137221c)

# Step 3: Verify the Volume is Attached
lsblk
The output will display the newly attached EBS volume as /dev/nvme1n1, indicating that the disk is present but not yet mounted.
![WhatsApp Image 2026-02-08 at 10 29 48 AM](https://github.com/user-attachments/assets/98307bd1-ae06-4a0b-a75f-1253d6148c61)

# Step 4: Format the Volume with ext4
Format the attached EBS volume using the appropriate device name based on the instance type.

For NVMe-based EC2 instances:
sudo mkfs.ext4 /dev/nvme1n1

## For non-NVMe (Xen-based) EC2 instances:
sudo mkfs.ext4 /dev/xvdf
![WhatsApp Image 2026-02-08 at 10 39 32 AM](https://github.com/user-attachments/assets/328e456e-b213-489d-8ad3-7b595d3666cc)

# ❓ Why Do We Format the Volume?

-New EBS volumes are created without a filesystem.

-Formatting initializes the volume for data storage.

-It enables the OS to mount and access the volume.

-Unformatted volumes cannot be used to store data.

 # 📂 Step 5: Create a Mount Directory
 sudo su : to swtich to super user
cd / : to go to home directory
mkdir /your-dir-name : to create new directory

# 📌 Step 6: Mount the Volume

After formatting, the EBS volume was mounted to the `/data` directory to make the storage accessible to the operating system.

# For NVMe-based EC2 instances
sudo mount /dev/nvme1n1 /data

# For non-NVMe (Xen-based) EC2 instances
sudo mount /dev/xvdf /data

![WhatsApp Image 2026-02-08 at 10 47 16 AM](https://github.com/user-attachments/assets/b63d13ce-3e40-4d5f-9f3a-b63984e76a77)

# Verify the mount:
df -h

# Step 7: Make the Mount Persistent (After Reboot)
To make the mount persistent, add an entry to /etc/fstab.
1. First, get the UUID:
   blkid
   <img width="1366" height="638" alt="image" src="https://github.com/user-attachments/assets/eb3562e8-1484-411a-a014-449432079139" />

2. Open the fstab file:
   vi /etc/fstab

# Note:
press "i" to insert mode
press "esc" to return 
press ":wq!" to write and exit

![WhatsApp Image 2026-02-08 at 11 03 22 AM](https://github.com/user-attachments/assets/04234c68-3d04-4ed6-a4e3-9a8f1b33f369)

# 🔄 Step 8: Reboot and Verify
reboot

After reboot, connect again and run:
df -h
You should see /test mounted. ✅

# 🧹 Optional: Test Persistence
You can unmount and remount to test:

umount /test
mount -a
df -h






 









