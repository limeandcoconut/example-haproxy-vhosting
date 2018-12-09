# Example HAProxy setup to reverse proxy multiple domains
This uses a docker container to setup HAProxy.<br>
The dockerized instance binds ports 80 and 443 on the host.<br>
This setup uses Http2 and http->https redirection via a 301 for all domains.<br>

## Configuring
The dockerized instance mounts a file, haproxy.cfg, and a folder ./certs.<br>
To add a new domain, add to the config a new Access Control List (acl) and use_backend config in the frontend section.<br>
```
acl host_example.org hdr(host) -i example.org
use_backend example.org if host_example.org
```
Then add a new backend.
```
backend example.org
    option forwardfor
    server localhost localhost:3005 check
```
And add a pem file to ./certs.<br><!-- FYI two spaces here will do the same thing as the br. I sorta like it explicit -->
HAProxy uses a single pem file containing the full chain and private key (order seems to be ambiguous).<br>
<br>
Any files other than valid certificates in ./certs will cause HAProxy to error.<br>
This is why there is no certs folder included with this project as I couldn't include a .gitkeep.<br>

## Running on non-linux
Comments in both files contain working examples for Mac.<br>
The docker-compose uses `network mode: "host"`.<br>
This makes it simple to proxy to a port on the machine, but isn't supported when running on non-linux.<br>

In order to run on non-linux, disable the line `network_mode: "host"` in the docker-compose.yml.<br>
Additionally, in the haproxy.cfg, in the `backend` section, change all instances of `localhost` to `host.docker.internal`.
