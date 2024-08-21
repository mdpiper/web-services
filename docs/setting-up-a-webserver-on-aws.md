# Setting up a webserver on AWS

These are the steps I've used to set up an EC2 instance
with an Apache web server.

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

Set up an EC2 instance based on an *Amazon Linux 2* (AL2) image.
If you use a different image, such as Ubuntu or Red Hat, the following instructions may not map directly.

## Notes

* Use the CSDMS 4 billing account on CloudBank to keep this work separate from OpenEarthscape.
