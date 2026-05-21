# Configure a PQC reverse proxy for IIS

## Option 1 - Nginx reverse PQC-proxy on an Ubuntu server

### Prerequisites
Make sure you have a Nginx server that's configured to only use PQC for TLS key exchange. This will build upon [my previous Nginx setup](https://github.com/trout-mask-replica/pqc-cheatsheet/blob/main/guides/web-servers/nginx/configure-kem-ubuntu.md).  

Then make sure your IIS server is in the same local network as the Nginx server and that it only exposes its HTTP(s) port to the Nginx server. You can do this by configuring Windows Firewall rules on the IIS server, or the NSGs for the VM in Azure, with an Allow Inbound rule for the IP-address of Nginx server for port 443 (HTTPS) or 80 (HTTP).

### Configure
On the Nginx server, navigate to the location of your vhost-files, typically:  

    cd /etc/nginx/sites-enabled

Edit the file that contains the config for the site you will use for reverse proxying to the IIS server and add the following proxy_pass directive:  

    location / {
	proxy_pass http://<IIS-PRIVATE-IP>:<PORT>
}  

You will probably want to add other options here such as [proxy_set_header](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_set_header), but that's out of scope for my purposes (and beyond my knowledge).  

Restart Nginx to apply new settings:  

    systemctl restart nginx

### Validate
Different ways of validating are possible, for instance you could open a browser and navigate to the URL of your Nginx server. You should now be greeted by your IIS-hosted site. To validate that the key exchanged was performed with PQC open the developer tools. For Edge it's Ctrl + Shift + I and then + (More tools) -> Security. Validate that the negotiated group is [one of the PQC curves as configured in your Nginx setup](https://github.com/trout-mask-replica/pqc-cheatsheet/blob/main/guides/web-servers/nginx/configure-kem-ubuntu.md#configure).
