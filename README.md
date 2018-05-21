# ansible-peertube

Deploy a peertube instance in less than 10 minutes.
I installed it on Ubuntu 18.04 bionic virtual private server.

# Download the project

```
cd
git clone https://github.com/wiseflat/ansible-peertube.git
cd ansible-peertube
```

# Ansible configuration

## Create your private ssh key

```
ssh-keygen -f ssh/id_rsa
ssh-copy-id -i ssh/id_rsa.pub ubuntu@<ip of your server>
```

## Create your ssh/config file

```

Host peertube-01
    HostName <ip of your server>

Host *
    HostName %h
    Port 22
    ForwardAgent yes
    StrictHostKeyChecking no
    UserKnownHostsFile /dev/null
    IdentityFile ssh/id_rsa
    ServerAliveInterval 30
    User ubuntu

```

## SSH to your server and install some packages

```
ssh -F ssh/config peertube-01
sudo apt install python-apt
```

## Update your configuration env/group_vars/peertube.yml 

You only need to configure the peertube part of the file

```
peertube_version: v1.0.0-beta.4
peertube_domain: peertube.example.com
peertube_admin_email: peertube@example.com
peertube_database_suffix: _prod
peertube_database_username: peertube
peertube_database_password: peertube

peertube_smtp_hostname: smtp.example.com
peertube_smtp_port: 465
peertube_smtp_username: peertube@example.com
peertube_smtp_password: "eid2wueRudiv3ToX0oLkjnHy5rT("
peertube_smtp_tls: true
peertube_smtp_disable_starttls: false
peertube_smtp_ca_file: null # Used for self signed certificates
peertube_smtp_from_address: 'peertube@example.com'

peertube_instance_name: PeerTube
peertube_instance_short_description: 'PeerTube, a federated (ActivityPub) video streaming platform using P2P (BitTorrent) directly in the web browser with WebTorrent and Angular.'
peertube_instance_description: ""
peertube_instance_terms: ""
peertube_services_twitter_username: '@Chocobozzz'
peertube_services_twitter_whitelisted: false

```

# Create a DNS A record

For generating new letsencrypt certificate, your domain needs to point to your public IP address and a webserver needs to listen on port 80


# Deploy peertube

Nginx needs to be set up wihout a https virtualhost the first time because you don't have your letsencrypt certificate yet. 

## Disable the ssl nginx vhost in env/group_vars/peertube.yml


```

nginx_vhosts:

  - listen: "443 ssl http2"
    state: absent
```

## Run the default playbook

ansible-playbook site.yml

## Enable the ssl nginx vhost in env/group_vars/peertube.yml

```

nginx_vhosts:

  - listen: "443 ssl http2"
    state: present
```

## Run the nginx playbook

ansible-playbook play/nginx.yml

# Done

Go to your server and reset your admin password

```
$ cd /var/www/peertube/peertube-latest && NODE_CONFIG_DIR=/var/www/peertube/config NODE_ENV=production npm run reset-password -- -u root
```

## Now your instance is up you can

Subscribe to the mailing list for PeerTube administrators: https://framalistes.org/sympa/subscribe/peertube-admin
Add you instance to the public PeerTube instances index if you want to: https://instances.peertu.be/
