# Example HAProxy setup to reverse proxy multiple domains
This setup uses a docker-compose to setup HAProxy.<br>
The dockerized instance binds ports 80 and 443 on the host.<br>

## Configuring
The dockerized instance mounts a file, haproxy.cfg, and a folder ./certs.<br>
To add a new domain, create a new Access Control List (acl) and use_backend config in the frontend section.<br>
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
And add a pem file to ./certs.<br>
HAProxy uses a single pem file containing the full chain and private key.<br>
<br>
Any files other than valid certificates in ./certs will cause HAProxy to error.<br>
This is why there is no certs folder included with this project as I couldn't include a .gitkeep.<br>

## Running on non-linux
As is, this setup will only work on linux hosts.<br>
This is because the dockerized HAproxy uses network mode "host".<br>
This makes it simple to proxy to port on the machine, but isn't supported when running on non-linux.<br>
In order to run on non-linux, disable the line ```network_mode: "host"``` in the docker-compose.yml.<br>
Additionally, in the haproxy.cfg, change instances of localhost for host.docker.internal.
