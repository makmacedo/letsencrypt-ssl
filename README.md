# Installing Letsencrypt SSL and AutoRenew with CRON Job

## 1 - Preparing the environment

Access your server via `ssh` (u412901475)
```
ssh -p 65002 uXXXXXXXXX@XXX.XX.XX.XX 
```

Clone acme-client from github and enter the folder
```
git clone https://github.com/kelunik/acme-client
cd acme-client
```

Download and install the composer
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"; 
php composer-setup.php;
php -r "unlink('composer-setup.php');";
php composer.phar install --no-dev
```

## 2 - Generating SSL Certificate

Register an account on Lets Encrypt (replace `your@email.com`)
```
php bin/acme setup --server letsencrypt --email your@email.com
```

Generating the SSL certificate (replace `yourdomain.com` and `uXXXXXXXXX`)
```
php bin/acme issue --domains yourdomain.com:www.youdomain.com --path /home/uXXXXXXXXX/public_html:/home/uXXXXXXXXX/public_html --server letsencrypt
```

If the certificate was issued successfully, you will see the following message:
```
Requesting certificate ...
Successfully issued certificate.
See /home/uXXXXXXXXX/acme-client/data/certs/acme-v01.api.letsencrypt.org.directory/yourdomain.com
```

Enter the folder
```
cd /home/uXXXXXXXXX/acme-client/data/certs/acme-v01.api.letsencrypt.org.directory/yourdomain.com
```

Use cat command to see fullchain.pem content then copy it to a text file named `certificate.txt`
```
cat fullchain.pem
```

Use cat command to see key.pem content then copy it to a text file named `privatekey.txt`
```
cat key.pem
```

## 3 - Installing the SSL Certificate in your Pannel

- Navigate to the SSL options in your server managment under the tearget domain
- Paste contents from `certificate.txt` to `Certificate:(CRT)` field
- Paste contents from `privatekey.txt` to `Private Key:(KEY)` field. 
- Press Install button once you are done.


## 4 - Checking expiration date and renewing SSL certificate

Let’s Encrypt SSL certificates are set to expire every 90 days. You can check its expiration date via SSH using the following command

```
php acme-client/bin/acme check --name yourdomain.com --server letsencrypt
```

You can also try to automatically renew Let’s Encrypt SSL certificate with a cron job. For example, in order to automatically renew SSL certificate, you can set cron job to run the following command every other month

```
php acme-client/bin/acme issue --domains yourdomain.com:www.yourdomain.com --path /home/uXXXXXXXXX/public_html:/home/uXXXXXXXXX/public_html --server letsencrypt
```

## 5 - Automating the SSL Renewal With CRON JOB

A CRON job can be created with the following settings tho Auto-renew the Certificates

- Common Option(s) – Once a month (0 0 1 * *)
- Minute(s) – :00 top of the hour (0)
- Hour(s) – 12 a.m. Midnight (0)
- Day(s) – 1st (1)
- Month(s) – Every other month (*/2)
- Weekday(s) – Every weekday (*)
