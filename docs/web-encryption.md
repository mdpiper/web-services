# Set up web encryption

These are the steps I followed to enable web encryption
on an EC2 instance with an Apache web server.

## Enable TLS on the server

After [setting up a web server](./web-server.md),
we can access it with HTTP:

http://13.56.97.225/

We want to be able to access it with HTTPS, though, as this is

1. secure
1. the only way we can associate a domain name.

Start by SSH-ing into the EC2 instance.

Make sure the web server is running.
```bash
sudo systemctl status httpd
```
If not, start it.

Install the Apache module *mod_ssl*:
```bash
sudo yum install mod_ssl
```
This gives us:
* the `openssl` command
* the SSL configuration file located at `/etc/httpd/conf.d/ssl.conf`
* certificates in `/etc/pki/tls/certs`

For testing purposes,
generate a self-signed dummy TLS certificate:
```bash
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -nodes -keyout localhost.key -out localhost.crt -subj "/CN=localhost" -addext "subjectAltName=DNS:localhost,DNS:*.localhost,IP:10.0.0.1"
```
This is from https://stackoverflow.com/a/41366949.

Copy the cert file to `/etc/pki/tls/certs` and the key file to `/etc/pki/tls/private`:
```bash
sudo cp localhost.crt /etc/pki/tls/certs
sudo cp localhost.key /etc/pki/tls/private
```
This should match the default settings in the `ssl.conf` file.
Check `/etc/httpd/conf.d/ssl.conf` to be sure.
Find the lines:
```
SSLCertificateFile /etc/pki/tls/certs/localhost.crt
```
and
```
SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
```

Restart the web server:
```bash
sudo systemctl restart httpd
```
and check that it's running:
```bash
sudo systemctl status httpd
```

Now access the site with HTTPS:

https://13.56.97.225/

Because the certificate is self-signed it's insecure and web browsers will reject it.
Override the objections of the web browser. 

We're now using TLS on the server.
All data passing between the browser and server are encrypted.

## References

* *Tutorial: Configure SSL/TLS on AL2*: https://docs.aws.amazon.com/linux/al2/ug/SSL-on-amazon-linux-2.html
