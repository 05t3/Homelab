## Update & Upgrade

![Waz-1](https://github.com/05t3/Homelab/assets/58165365/e03b6b68-9f22-4422-aa38-c40619ad0232)

# Network Configuration


```bash
root@analyst:/home/analyst# cd /etc/netplan/
root@analyst:/etc/netplan# ls -la
total 12
drwxr-xr-x  2 root root 4096 May 12 11:20 .
drwxr-xr-x 98 root root 4096 May 17 17:33 ..
-rw-r--r--  1 root root  116 May 12 11:20 00-installer-config.yaml
root@analyst:/etc/netplan# cp 00-installer-config.yaml 00-installer-config.yaml.bak
root@analyst:/etc/netplan# ls -la
total 16
drwxr-xr-x  2 root root 4096 May 17 17:44 .
drwxr-xr-x 98 root root 4096 May 17 17:33 ..
-rw-r--r--  1 root root  116 May 12 11:20 00-installer-config.yaml
-rw-r--r--  1 root root  116 May 17 17:44 00-installer-config.yaml.bak
root@analyst:/etc/netplan# cat 00-installer-config.yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      dhcp4: true
  version: 2
root@analyst:/etc/netplan# nano 00-installer-config.yaml
root@analyst:/etc/netplan# cat 00-installer-config.yaml
# This is the network config written by 'subiquity'
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.1.50/24
      gateway4: 192.168.1.1
      nameservers:
          addresses: [192.168.1.100, 8.8.8.8]
root@analyst:/etc/netplan# netplan apply
client_loop: send disconnect: Connection reset by peer
```


Reboot:


```bash
root@analyst:/home/analyst# cat /etc/hostname
analyst
root@analyst:/home/analyst# sudo vi /etc/hostname
root@analyst:/home/analyst# cat /etc/hostname
soc.com
root@analyst:/home/analyst# reboot
root@analyst:/home/analyst# Connection to 192.168.1.50 closed by remote host.
Connection to 192.168.1.50 closed.
```


Reboot:


## Installing prerequisites

```bash

root@soc:/home/analyst# apt-get install apt-transport-https zip unzip lsb-release curl gnupg -y
Reading package lists... Done
Building dependency tree
Reading state information... Done
lsb-release is already the newest version (11.1.0ubuntu2).
lsb-release set to manually installed.
curl is already the newest version (7.68.0-1ubuntu2.18).
curl set to manually installed.
gnupg is already the newest version (2.2.19-3ubuntu2.2).
gnupg set to manually installed.
The following NEW packages will be installed:
  apt-transport-https unzip zip
0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
Need to get 337 kB of archives.
After this operation, 1,393 kB of additional disk space will be used.
Get:1 http://ke.archive.ubuntu.com/ubuntu focal-updates/universe amd64 apt-transport-https all 2.0.9 [1,704 B]
Get:2 http://ke.archive.ubuntu.com/ubuntu focal-updates/main amd64 unzip amd64 6.0-25ubuntu1.1 [168 kB]
Get:3 http://ke.archive.ubuntu.com/ubuntu focal/main amd64 zip amd64 3.0-11build1 [167 kB]
Fetched 337 kB in 1s (495 kB/s)
Selecting previously unselected package apt-transport-https.
(Reading database ... 108791 files and directories currently installed.)
Preparing to unpack .../apt-transport-https_2.0.9_all.deb ...
Unpacking apt-transport-https (2.0.9) ...
Selecting previously unselected package unzip.
Preparing to unpack .../unzip_6.0-25ubuntu1.1_amd64.deb ...
Unpacking unzip (6.0-25ubuntu1.1) ...
Selecting previously unselected package zip.
Preparing to unpack .../zip_3.0-11build1_amd64.deb ...
Unpacking zip (3.0-11build1) ...
Setting up apt-transport-https (2.0.9) ...
Setting up unzip (6.0-25ubuntu1.1) ...
Setting up zip (3.0-11build1) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for mime-support (3.64ubuntu1) ...
```


## Installing Elasticsearch

```bash
root@soc:/home/analyst# curl -s https://artifacts.elastic.co/GPG-KEY-elasticsearch | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/elasticsearch.gpg --import && chmod 644 /usr/share/keyrings/elasticsearch.gpg
gpg: keyring '/usr/share/keyrings/elasticsearch.gpg' created
gpg: directory '/root/.gnupg' created
gpg: /root/.gnupg/trustdb.gpg: trustdb created
gpg: key D27D666CD88E42B4: public key "Elasticsearch (Elasticsearch Signing Key) <dev_ops@elasticsearch.org>" imported
gpg: Total number processed: 1
gpg:               imported: 1
root@soc:/home/analyst# echo "deb [signed-by=/usr/share/keyrings/elasticsearch.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | tee /etc/apt/sources.list.d/elastic-7.x.list
deb [signed-by=/usr/share/keyrings/elasticsearch.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main
root@soc:/home/analyst# apt-get update
Hit:1 http://ke.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://ke.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:3 http://ke.archive.ubuntu.com/ubuntu focal-backports InRelease
Hit:4 http://ke.archive.ubuntu.com/ubuntu focal-security InRelease
Get:5 https://artifacts.elastic.co/packages/7.x/apt stable InRelease [13.7 kB]
Get:6 https://artifacts.elastic.co/packages/7.x/apt stable/main amd64 Packages [111 kB]
Fetched 125 kB in 1s (172 kB/s)
Reading package lists... Done
root@soc:/home/analyst# apt-get install elasticsearch=7.17.9
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  elasticsearch
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 315 MB of archives.
After this operation, 527 MB of additional disk space will be used.
Get:1 https://artifacts.elastic.co/packages/7.x/apt stable/main amd64 elasticsearch amd64 7.17.9 [315 MB]
Fetched 315 MB in 6min 26s (816 kB/s)
Selecting previously unselected package elasticsearch.
(Reading database ... 108827 files and directories currently installed.)
Preparing to unpack .../elasticsearch_7.17.9_amd64.deb ...
Creating elasticsearch group... OK
Creating elasticsearch user... OK
Unpacking elasticsearch (7.17.9) ...
Setting up elasticsearch (7.17.9) ...
### NOT starting on installation, please execute the following statements to configure elasticsearch service to start automatically using systemd
 sudo systemctl daemon-reload
 sudo systemctl enable elasticsearch.service
### You can start elasticsearch service by executing
 sudo systemctl start elasticsearch.service
Created elasticsearch keystore in /etc/elasticsearch/elasticsearch.keystore
Processing triggers for systemd (245.4-4ubuntu3.21) ...
root@soc:/home/analyst# sudo systemctl daemon-reload
root@soc:/home/analyst# curl -so /etc/elasticsearch/elasticsearch.yml https://packages.wazuh.com/4.4/tpl/elastic-basic/elasticsearch_all_in_one.yml
root@soc:/home/analyst# curl -so /usr/share/elasticsearch/instances.yml https://packages.wazuh.com/4.4/tpl/elastic-basic/instances_aio.yml
root@soc:/home/analyst# /usr/share/elasticsearch/bin/elasticsearch-certutil cert ca --pem --in instances.yml --keep-ca-key --out ~/certs.zip
This tool assists you in the generation of X.509 certificates and certificate
signing requests for use with SSL/TLS in the Elastic stack.

The 'cert' mode generates X.509 certificate and private keys.
    * By default, this generates a single certificate and key for use
       on a single instance.
    * The '-multiple' option will prompt you to enter details for multiple
       instances and will generate a certificate and key for each one
    * The '-in' option allows for the certificate generation to be automated by describing
       the details of each instance in a YAML file

    * An instance is any piece of the Elastic Stack that requires an SSL certificate.
      Depending on your configuration, Elasticsearch, Logstash, Kibana, and Beats
      may all require a certificate and private key.
    * The minimum required value for each instance is a name. This can simply be the
      hostname, which will be used as the Common Name of the certificate. A full
      distinguished name may also be used.
    * A filename value may be required for each instance. This is necessary when the
      name would result in an invalid file or directory name. The name provided here
      is used as the directory name (within the zip) and the prefix for the key and
      certificate files. The filename is required if you are prompted and the name
      is not displayed in the prompt.
    * IP addresses and DNS names are optional. Multiple values can be specified as a
      comma separated string. If no IP addresses or DNS names are provided, you may
      disable hostname verification in your SSL configuration.

    * All certificates generated by this tool will be signed by a certificate authority (CA)
      unless the --self-signed command line option is specified.
      The tool can automatically generate a new CA for you, or you can provide your own with
      the --ca or --ca-cert command line options.

By default the 'cert' mode produces a single PKCS#12 output file which holds:
    * The instance certificate
    * The private key for the instance certificate
    * The CA certificate

If you specify any of the following options:
    * -pem (PEM formatted output)
    * -keep-ca-key (retain generated CA key)
    * -multiple (generate multiple certificates)
    * -in (generate certificates from an input file)
then the output will be be a zip file containing individual certificate/key files

Note: Generating certificates without providing a CA certificate is deprecated.
      A CA certificate will become mandatory in the next major release.


Certificates written to /root/certs.zip

This file should be properly secured as it contains the private key for
your instance and for the certificate authority.

After unzipping the file, there will be a directory for each instance.
Each instance has a certificate and private key.
For each Elastic product that you wish to configure, you should copy
the certificate, key, and CA certificate to the relevant configuration directory
and then follow the SSL configuration instructions in the product guide.

For client applications, you may only need to copy the CA certificate and
configure the client to trust this certificate.
root@soc:/home/analyst# unzip ~/certs.zip -d ~/certs
Archive:  /root/certs.zip
   creating: /root/certs/ca/
  inflating: /root/certs/ca/ca.crt
  inflating: /root/certs/ca/ca.key
   creating: /root/certs/elasticsearch/
  inflating: /root/certs/elasticsearch/elasticsearch.crt
  inflating: /root/certs/elasticsearch/elasticsearch.key
root@soc:/home/analyst# mkdir /etc/elasticsearch/certs/ca -p
root@soc:/home/analyst# cp -R ~/certs/ca/ ~/certs/elasticsearch/* /etc/elasticsearch/certs/
root@soc:/home/analyst# chown -R elasticsearch: /etc/elasticsearch/certs
root@soc:/home/analyst# chmod -R 500 /etc/elasticsearch/certs
root@soc:/home/analyst# chmod 400 /etc/elasticsearch/certs/ca/ca.* /etc/elasticsearch/certs/elasticsearch.*
root@soc:/home/analyst# rm -rf ~/certs/ ~/certs.zip
root@soc:/home/analyst# systemctl daemon-reload
root@soc:/home/analyst# systemctl enable elasticsearch
Synchronizing state of elasticsearch.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable elasticsearch
Created symlink /etc/systemd/system/multi-user.target.wants/elasticsearch.service → /lib/systemd/system/elasticsearch.service.
root@soc:/home/analyst# systemctl start elasticsearch
root@soc:/home/analyst# /usr/share/elasticsearch/bin/elasticsearch-setup-passwords auto
Initiating the setup of passwords for reserved users elastic,apm_system,kibana,kibana_system,logstash_system,beats_system,remote_monitoring_user.
The passwords will be randomly generated and printed to the console.
Please confirm that you would like to continue [y/N]Y


Changed password for user apm_system
PASSWORD apm_system = BOs77FthsoKRu4h4hZtu

Changed password for user kibana_system
PASSWORD kibana_system = DzQDHOfLuZcZM2Flnkd5

Changed password for user kibana
PASSWORD kibana = DzQDHOfLuZcZM2Flnkd5

Changed password for user logstash_system
PASSWORD logstash_system = cwUSER1DPGJZ6NQCHQ9x

Changed password for user beats_system
PASSWORD beats_system = HLfFZV6A7TvTKHqTIc85

Changed password for user remote_monitoring_user
PASSWORD remote_monitoring_user = bGzArfm9iMdWf3q8GFfz

Changed password for user elastic
PASSWORD elastic = DXPQNAX3kjtOeS20BVEC

root@soc:/home/analyst# curl -XGET https://localhost:9200 -u elastic:DXPQNAX3kjtOeS20BVEC -k
{
  "name" : "elasticsearch",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "G8F-iVRzT7qplNUcR-r5Lg",
  "version" : {
    "number" : "7.17.9",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "ef48222227ee6b9e70e502f0f0daa52435ee634d",
    "build_date" : "2023-01-31T05:34:43.305517834Z",
    "build_snapshot" : false,
    "lucene_version" : "8.11.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```


## Installing Wazuh server


```bash

root@soc:/home/analyst# curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
gpg: keyring '/usr/share/keyrings/wazuh.gpg' created
gpg: key 96B3EE5F29111145: public key "Wazuh.com (Wazuh Signing Key) <support@wazuh.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
root@soc:/home/analyst# echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main
root@soc:/home/analyst# apt-get update
Hit:1 http://ke.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://ke.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:3 http://ke.archive.ubuntu.com/ubuntu focal-backports InRelease
Hit:4 http://ke.archive.ubuntu.com/ubuntu focal-security InRelease
Get:5 https://packages.wazuh.com/4.x/apt stable InRelease [17.3 kB]
Hit:6 https://artifacts.elastic.co/packages/7.x/apt stable InRelease
Get:7 https://packages.wazuh.com/4.x/apt stable/main amd64 Packages [27.8 kB]
Fetched 45.1 kB in 1s (31.4 kB/s)
Reading package lists... Done
root@soc:/home/analyst# apt-get install wazuh-manager
Reading package lists... Done
Building dependency tree
Reading state information... Done
Suggested packages:
  expect
The following NEW packages will be installed:
  wazuh-manager
0 upgraded, 1 newly installed, 0 to remove and 1 not upgraded.
Need to get 125 MB of archives.
After this operation, 471 MB of additional disk space will be used.
Get:1 https://packages.wazuh.com/4.x/apt stable/main amd64 wazuh-manager amd64 4.4.1-1 [125 MB]
Fetched 125 MB in 2min 32s (822 kB/s)
Selecting previously unselected package wazuh-manager.
(Reading database ... 109923 files and directories currently installed.)
Preparing to unpack .../wazuh-manager_4.4.1-1_amd64.deb ...
Unpacking wazuh-manager (4.4.1-1) ...
Setting up wazuh-manager (4.4.1-1) ...
Processing triggers for systemd (245.4-4ubuntu3.21) ...
root@soc:/home/analyst# systemctl daemon-reload
root@soc:/home/analyst# systemctl enable wazuh-manager
Synchronizing state of wazuh-manager.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable wazuh-manager
Created symlink /etc/systemd/system/multi-user.target.wants/wazuh-manager.service → /lib/systemd/system/wazuh-manager.service.
root@soc:/home/analyst# systemctl start wazuh-manager
root@soc:/home/analyst# systemctl status wazuh-manager
● wazuh-manager.service - Wazuh manager
     Loaded: loaded (/lib/systemd/system/wazuh-manager.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2023-05-17 18:18:43 UTC; 2s ago
    Process: 73347 ExecStart=/usr/bin/env /var/ossec/bin/wazuh-control start (code=exited, status=0/SUCCESS)
      Tasks: 112 (limit: 5775)
     Memory: 391.4M
     CGroup: /system.slice/wazuh-manager.service
             ├─73418 /var/ossec/framework/python/bin/python3 /var/ossec/api/scripts/wazuh-apid.py
             ├─73458 /var/ossec/bin/wazuh-authd
             ├─73475 /var/ossec/bin/wazuh-db
             ├─73499 /var/ossec/bin/wazuh-execd
             ├─73502 /var/ossec/framework/python/bin/python3 /var/ossec/api/scripts/wazuh-apid.py
             ├─73505 /var/ossec/framework/python/bin/python3 /var/ossec/api/scripts/wazuh-apid.py
             ├─73519 /var/ossec/bin/wazuh-analysisd
             ├─73564 /var/ossec/bin/wazuh-syscheckd
             ├─73580 /var/ossec/bin/wazuh-remoted
             ├─73611 /var/ossec/bin/wazuh-logcollector
             ├─73631 /var/ossec/bin/wazuh-monitord
             ├─73642 /var/ossec/bin/wazuh-modulesd
             └─73786 apt -s upgrade

May 17 18:18:33 soc.com env[73347]: Started wazuh-db...
May 17 18:18:34 soc.com env[73347]: Started wazuh-execd...
May 17 18:18:36 soc.com env[73347]: Started wazuh-analysisd...
May 17 18:18:37 soc.com env[73347]: Started wazuh-syscheckd...
May 17 18:18:38 soc.com env[73347]: Started wazuh-remoted...
May 17 18:18:39 soc.com env[73347]: Started wazuh-logcollector...
May 17 18:18:40 soc.com env[73347]: Started wazuh-monitord...
May 17 18:18:41 soc.com env[73347]: Started wazuh-modulesd...
May 17 18:18:43 soc.com env[73347]: Completed.
May 17 18:18:43 soc.com systemd[1]: Started Wazuh manager.
```


## Installing Filebeat


```bash
root@soc:/home/analyst# apt-get install filebeat=7.17.9
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  filebeat
0 upgraded, 1 newly installed, 0 to remove and 1 not upgraded.
Need to get 35.5 MB of archives.
After this operation, 131 MB of additional disk space will be used.
Get:1 https://artifacts.elastic.co/packages/7.x/apt stable/main amd64 filebeat amd64 7.17.9 [35.5 MB]
Fetched 35.5 MB in 38s (941 kB/s)
Selecting previously unselected package filebeat.
(Reading database ... 129380 files and directories currently installed.)
Preparing to unpack .../filebeat_7.17.9_amd64.deb ...
Unpacking filebeat (7.17.9) ...
Setting up filebeat (7.17.9) ...
Processing triggers for systemd (245.4-4ubuntu3.21) ...
root@soc:/home/analyst# curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.4/tpl/elastic-basic/filebeat_all_in_one.yml
root@soc:/home/analyst# curl -so /etc/filebeat/wazuh-template.json https://raw.githubusercontent.com/wazuh/wazuh/4.4/extensions/elasticsearch/7.x/wazuh-template.json
root@soc:/home/analyst# chmod go+r /etc/filebeat/wazuh-template.json
root@soc:/home/analyst# curl -s https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.2.tar.gz | tar -xvz -C /usr/share/filebeat/module
wazuh/alerts/
wazuh/alerts/config/
wazuh/alerts/config/alerts.yml
wazuh/alerts/manifest.yml
wazuh/alerts/ingest/
wazuh/alerts/ingest/pipeline.json
wazuh/archives/
wazuh/archives/config/
wazuh/archives/config/archives.yml
wazuh/archives/manifest.yml
wazuh/archives/ingest/
wazuh/archives/ingest/pipeline.json
wazuh/module.yml
root@soc:/home/analyst# nano /etc/filebeat/filebeat.yml
root@soc:/home/analyst# nano /etc/filebeat/filebeat.yml
root@soc:/home/analyst# cat /etc/filebeat/filebeat.yml
# Wazuh - Filebeat configuration file
output.elasticsearch.hosts: ["127.0.0.1:9200"]
output.elasticsearch.password: DXPQNAX3kjtOeS20BVEC

filebeat.modules:
  - module: wazuh
    alerts:
      enabled: true
    archives:
      enabled: false

setup.template.json.enabled: true
setup.template.json.path: /etc/filebeat/wazuh-template.json
setup.template.json.name: wazuh
setup.template.overwrite: true
setup.ilm.enabled: false

output.elasticsearch.protocol: https
output.elasticsearch.ssl.certificate: /etc/elasticsearch/certs/elasticsearch.crt
output.elasticsearch.ssl.key: /etc/elasticsearch/certs/elasticsearch.key
output.elasticsearch.ssl.certificate_authorities: /etc/elasticsearch/certs/ca/ca.crt
output.elasticsearch.ssl.verification_mode: strict
output.elasticsearch.username: elastic

logging.metrics.enabled: false

seccomp:
  default_action: allow
  syscalls:
  - action: allow
    names:
    - rseq
root@soc:/home/analyst# cp -r /etc/elasticsearch/certs/ca/ /etc/filebeat/certs/
root@soc:/home/analyst# cp /etc/elasticsearch/certs/elasticsearch.crt /etc/filebeat/certs/filebeat.crt
root@soc:/home/analyst# cp /etc/elasticsearch/certs/elasticsearch.key /etc/filebeat/certs/filebeat.key
root@soc:/home/analyst# systemctl daemon-reload
root@soc:/home/analyst# systemctl enable filebeat
Synchronizing state of filebeat.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable filebeat
Created symlink /etc/systemd/system/multi-user.target.wants/filebeat.service → /lib/systemd/system/filebeat.service.
root@soc:/home/analyst# systemctl start filebeat
root@soc:/home/analyst# filebeat test output
elasticsearch: https://127.0.0.1:9200...
  parse url... OK
  connection...
    parse host... OK
    dns lookup... OK
    addresses: 127.0.0.1
    dial up... OK
  TLS...
    security: server's certificate chain verification is enabled
    handshake... OK
    TLS version: TLSv1.3
    dial up... OK
  talk to server... OK
  version: 7.17.9
```

## Kibana installation and configuration

```bash
root@soc:/home/analyst# apt-get install kibana=7.17.9
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  kibana
0 upgraded, 1 newly installed, 0 to remove and 2 not upgraded.
Need to get 272 MB of archives.
After this operation, 691 MB of additional disk space will be used.
Get:1 https://artifacts.elastic.co/packages/7.x/apt stable/main amd64 kibana amd64 7.17.9 [272 MB]
Fetched 272 MB in 8min 40s (522 kB/s)
Selecting previously unselected package kibana.
(Reading database ... 131456 files and directories currently installed.)
Preparing to unpack .../kibana_7.17.9_amd64.deb ...
Unpacking kibana (7.17.9) ...
Setting up kibana (7.17.9) ...
Creating kibana group... OK
Creating kibana user... OK
Created Kibana keystore in /etc/kibana/kibana.keystore
Processing triggers for systemd (245.4-4ubuntu3.21) ...
root@soc:/home/analyst# mkdir /etc/kibana/certs/ca -p
root@soc:/home/analyst# cp -R /etc/elasticsearch/certs/ca/ /etc/kibana/certs/
root@soc:/home/analyst# cp /etc/elasticsearch/certs/elasticsearch.key /etc/kibana/certs/kibana.key
root@soc:/home/analyst# cp /etc/elasticsearch/certs/elasticsearch.crt /etc/kibana/certs/kibana.crt
root@soc:/home/analyst# chown -R kibana:kibana /etc/kibana/
root@soc:/home/analyst# chmod -R 500 /etc/kibana/certs
root@soc:/home/analyst# chmod 440 /etc/kibana/certs/ca/ca.* /etc/kibana/certs/kibana.*
root@soc:/home/analyst# curl -so /etc/kibana/kibana.yml https://packages.wazuh.com/4.4/tpl/elastic-basic/kibana_all_in_one.yml
root@soc:/home/analyst# cat /etc/kibana/kibana.yml
server.host: 0.0.0.0
server.port: 443
elasticsearch.hosts: https://localhost:9200
elasticsearch.password: <elasticsearch_password>

# Elasticsearch from/to Kibana

elasticsearch.ssl.certificateAuthorities: /etc/kibana/certs/ca/ca.crt
elasticsearch.ssl.certificate: /etc/kibana/certs/kibana.crt
elasticsearch.ssl.key: /etc/kibana/certs/kibana.key

# Browser from/to Kibana
server.ssl.enabled: true
server.ssl.certificate: /etc/kibana/certs/kibana.crt
server.ssl.key: /etc/kibana/certs/kibana.key

# Elasticsearch authentication
xpack.security.enabled: true
elasticsearch.username: elastic
uiSettings.overrides.defaultRoute: "/app/wazuh"
elasticsearch.ssl.verificationMode: certificate
telemetry.banner: falseroot@soc:/home/analyst# nano /etc/kibana/kibana.yml
root@soc:/home/analyst# head /etc/kibana/kibana.yml
server.host: 0.0.0.0
server.port: 443
elasticsearch.hosts: https://localhost:9200
elasticsearch.password: DXPQNAX3kjtOeS20BVEC

# Elasticsearch from/to Kibana

elasticsearch.ssl.certificateAuthorities: /etc/kibana/certs/ca/ca.crt
elasticsearch.ssl.certificate: /etc/kibana/certs/kibana.crt
elasticsearch.ssl.key: /etc/kibana/certs/kibana.key
root@soc:/home/analyst# mkdir /usr/share/kibana/data
root@soc:/home/analyst# chown -R kibana:kibana /usr/share/kibana
root@soc:/home/analyst# cd /usr/share/kibana
root@soc:/usr/share/kibana# sudo -u kibana /usr/share/kibana/bin/kibana-plugin install https://packages.wazuh.com/4.x/ui/kibana/wazuh_kibana-4.4.1_7.17.9-1.zip
Attempting to transfer from https://packages.wazuh.com/4.x/ui/kibana/wazuh_kibana-4.4.1_7.17.9-1.zip
Transferring 38527322 bytes....................
Transfer complete
Retrieving metadata from plugin archive
Extracting plugin archive
Extraction complete
Plugin installation complete
root@soc:/usr/share/kibana# setcap 'cap_net_bind_service=+ep' /usr/share/kibana/node/bin/node
root@soc:/usr/share/kibana# systemctl daemon-reload
root@soc:/usr/share/kibana# systemctl enable kibana
Synchronizing state of kibana.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable kibana
Created symlink /etc/systemd/system/multi-user.target.wants/kibana.service → /etc/systemd/system/kibana.service.
root@soc:/usr/share/kibana# systemctl start kibana
root@soc:/usr/share/kibana#
```


## Access the web interface

![image](https://github.com/05t3/Homelab/assets/58165365/1ff46855-9f4c-40d2-9559-64721045c9cc)


![image](https://github.com/05t3/Homelab/assets/58165365/ef5cc8a2-6f24-462f-80b0-2d1638db5c73)

