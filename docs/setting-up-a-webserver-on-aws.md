# Setting up a webserver on AWS

These are the steps I used to set up an EC2 instance
with an Apache web server.

I'm using the N. California (us-west-1) region for all settings.

## Network and security

Before creating an EC2 instance,
check the **Network & Security** settings under the **EC2 Dashboard**.

If it's not present,
import the *csdms-jh* public key.
This is most helpful for SSH access to the EC2 instance we'll create.
You'll need the private key stored locally.

Also, if not present,
set up a security group that allows inbound SSH, HTTP, and HTTPS traffic.
For example,
here's the exported CSV of the security group I made called *csdms-secure*:
```csv
GroupName,Type,IpProtocol,FromPort,ToPort,IpRanges,Ipv6Ranges,PrefixListIds,UserIdGroupPairs
csdms-secure,Inbound/Ingress,tcp,80,80,0.0.0.0/0,,,
csdms-secure,Inbound/Ingress,tcp,80,80,,::/0,,
csdms-secure,Inbound/Ingress,tcp,22,22,0.0.0.0/0,,,
csdms-secure,Inbound/Ingress,tcp,443,443,0.0.0.0/0,,,
csdms-secure,Inbound/Ingress,tcp,443,443,,::/0,,
csdms-secure,Outbound/Egress,'-1,,,0.0.0.0/0,,,
```
SSH only needs IPv4, but also add IPv6 for HTTP and HTTPS.

## Create the EC2 instance

Set up an EC2 instance for the webserver.
The settings I used follow.

| Setting | Value
| ------- | -----
| Name | csdms.io
| Amazon Machine Image | Amazon Linux 2023
| Architecture | x86_64
| Instance type | t3a.medium (4 G, 2 vCPUs)
| Key pair | csdms-jh
| Security group | csdms-secure
| Storage | 100 G, gp3
| Default user (with sudo) | ec2-user

Note that you can you use a different AMI, such as Ubuntu or Red Hat, but the following instructions may not map exactly.

I chose a t3a.medium instance for the additional memory.

## Connect to the EC2 instance

After creating the EC2 instance,
return to the **Network & Security** settings
and allocate an Elastic IP address.
Associate this Elastic IP with the *csdms.io* EC2 instance.
This gives the instance a static IP address.

If you have the *csdms-jh* private key,
you can now SSH into the instance:
```bash
ssh -i "csdms-jh.pem" ec2-user@<elastic-ip-address>
```
where `<elastic-ip-address>` is the IPv4 address of the Elastic IP.

Once logged in, check the OS version:
```bash
cat /etc/system-release
```

## Install and test the web server

SSH into the EC2 instance.

First, check that the system software on the instance is up to date:
```bash
sudo yum update
```

Install the Apache web server:
```bash
sudo yum install httpd
```

Once installed, start the web server:
```bash
sudo systemctl start httpd
```
and then check that it's running:
```bash
sudo systemctl status httpd
```

Last, set the web server so that it starts automatically:
```bash
sudo systemctl enable httpd
```
and check this with:
```bash
sudo systemctl is-enabled httpd
```

Test the web server by going to the IP address of the EC2 instance.
If no content has been placed in the webroot, `/var/www/html`, you should see the Apache test page with a helpful message.

## Notes

* Use the CSDMS 4 billing account on CloudBank to keep this work separate from OpenEarthscape.
