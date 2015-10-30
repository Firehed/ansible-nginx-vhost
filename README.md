Nginx Vhost
=========

Configures a single virtual host for nginx

Requirements
------------

Nginx should be installed on the system already (this can be done with the `Firehed.nginx` role)

Role Variables
--------------

Required:

    vhost_index: index.php
    vhost_root: /var/www/example.com
    vhost_name: example.com

Optional:

    vhost_alt_names: www.example.com www2.example.com
    vhost_fastcgi_params:
      SOME: value
      SOME_OTHER: value
    vhost_fastcgi_pass: 127.0.0.1:9000
    vhost_process_http: yes
    vhost_redirect_http: no
    
	# defaults to empty dictionary
    vhost_ssl:
      certificate: /path/to/public_key.pem
      private_key: /path/to/private_key.pem
      dhparam: /path/to/dhparam.pem (optional)
      hsts: 1576800 (optional, HSTS header lifetime duration in seconds)
      
Dependencies
------------

The `Firehed.nginx` role is highly recommended, as it installs and configures Nginx itself. However it's not listed as an explicit dependency as the two can run independently.

Example Playbook
----------------

**Important!** The default configuration *does not use SSL*, meaning your website *will be insecure*. The example below is secure.

    - hosts: servers
      roles:
        - role: Firehed.nginx-vhost
          vhost_index: index.php
          vhost_root: /var/www/example.com
          vhost_name: example.com
          vhost_process_http: no
          vhost_redirect_http: yes
          vhost_ssl:
            certificate: /etc/ssl/example.com/cert.pem
            private_key: /etc/ssl/example.com/key.pem
            dhparam: /etc/ssl/example.com/dhparam.pem
            hsts: 1576800
           
Recommended; shown above: To redirect HTTP to HTTPS (301), set `vhost_redirect_http: yes` and configure the `vhost_ssl` dictionary.

Ideal: To serve *only* HTTPS, set `vhost_process_http: no`, `vhost_redirect_http: no`, and configure the `vhost_ssl` dictionary. *The server will not even listen for plaintext HTTP requests!* This is not practial unless the domain is in the browser's HSTS Preload list or is an API that is only intended to be available over HTTPS.

**Not recommended**: To serve HTTP and HTTPS in parallel, set `vhost_process_http: yes` (default), `vhost_redirect_http: no` (default), and configure the `vhost_ssl` dictionary.


**Not recommended**: To serve HTTP only, do not provide a `vhost_ssl` value.


License
-------

MIT