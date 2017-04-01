acme-tiny-wrapper
=================

This is a fork of [tasn/acme-tiny-wrapper][1] prepared for packaging. The basic
idea behind this is to go through all certificate requests in a given directory
and use acme-tiny to retrieve new, valid certificates.

Defaults
--------

- `/etc/acme-tiny/acme-tiny.cfg` contains the configuration
- `/etc/acme-tiny/csr/` is the location of the certificate requests to use
- `/var/lib/acme-tiny/` is used as work directory
- `/var/lib/acme-tiny/account.key` is the Let's encrypt account key
- `/var/lib/acme-tiny/certs` is the default certificate output directory
- `/var/www/acme-challenges/` is the default challenge directory

Configuration
-------------

0. Install acme-tiny
1. Set the `account.key` if one already exists
2. Adjust the values in `acme-tiny.cfg` and make sure the directory specified by `ACME_DIR` is served by a web browser.

Example
-------

Here we want to request a certificate for `test.example.com` using Nginx as web
server. For that we first need to configure Nginx to serve the challenges and
configure the directories accordingly. The Nginx configuration should look like
this:

    server {
        listen 80;
         ...
        location /.well-known/acme-challenge {
            alias /var/www/acme-challenges/;
        }
    }

You can use different locations but they need to be specified in the
`acme-tiny.cfg` configuration file.

The wrapper will automatically generate a new account key if it does not exist.
If you have an account key, copy it to `/var/lib/acme-tiny/account.key` and
ensure it is readable only by the user executing the acme-tiny-wrapper.

Next, generate a certificate request and place it in
`/etc/acme-tiny/csr/`

    openssl genrsa 4096 > test.example.com.key
    openssl req -new -sha256 -key test.example.com.key -subj "/CN=test.example.com" > test.example.com.csr
    cp test.example.com.csr /etc/acme-tiny/csr/

Finally, you may specify an additional output location for the generated
certificate. You can do that for each certificate by creating a location file:

    echo /etc/nginx/ssl/certs > /etc/acme-tiny/csr/test.example.com.location

or by specifying the `DEFAULT_LOCATION` in the `acme-tiny.cfg`.

Running `acme-tiny-wrapper` will now generate new certificates.


[1]: https://github.com/tasn/acme-tiny-wrapper
