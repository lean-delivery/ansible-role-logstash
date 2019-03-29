logstash role
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-logstash/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-logstash.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-logstash)
[![Build Status](https://gitlab.com/lean-delivery/ansible-role-logstash/badges/master/build.svg)](https://gitlab.com/lean-delivery/ansible-role-logstash/pipelines)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.logstash-blue.svg)](https://galaxy.ansible.com/lean_delivery/logstash)
![Ansible](https://img.shields.io/ansible/role/d/39169.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2F39169%2F&query=$.min_ansible_version)

This role:
  - Installs Logstash server
  - Configures Logstash server

Requirements
------------

- Minimal Version of the ansible for installation: 2.5
- **Supported OS**:
  - CentOS
    - 7
  - Ubuntu
  - Debian
    - 9

Role Variables
--------------

- `elastic_branch`:
 Is used to select main Elasticsearch branch to be installed (5.x or 6.x current stable versions). By default this variable is set to `6`. So, 6.x version is installed by default. You can override this by setting this variable in playbook. Default to `6`
- `es_major_version`:
The version to install. Default to `'{{ elastic_branch }}.x'`

- `ls_host`:
Logstash host name. Default to `'{{ ansible_fqdn }}'`
- `ls_port`:
Logstash port. Used for agents connection. Default to `5555`
- `ls_xms`:
Initial size of total heap space. Default to `1g`
- `ls_xmx`:
Maximum size of total heap space. Default to `1g`

- `ls_config`:
All Logstash configuration parameters are supported. This is achieved using a configuration map parameter `ls_config` which is serialized into the logstash.yml file.
The use of a map ensures the Ansible playbook does not need to be updated to reflect new/deprecated/plugin configuration parameters. Default to `[]`
- `ls_home`:
Path to logstash home directory. Default to `/usr/share/logstash`
- `ls_pid_file`:
Path to logstash pid file. Default to `/var/run/logstash.pid`
- `ls_log_dir`:
Path to logstash log directory. Default to `/var/log/logstash`
- `ls_conf_dir`:
Path to logstash configuration directory. Default to `/etc/logstash`
- `ls_data_dir`:
Path to logstash data directory. Default to `/var/lib/logstash`
- `ls_nice_level`:
Logstash nice level. Default to `19`

- `elasticsearch_host`:
Specifies the address to which the elasticsearch server will bind. Default to `elasticsearch.example.com`
- `elasticsearch_port`:
Port on which elasticsearch listen to incoming requests. Default value is `9200`

## SSL certificate
- `ls_enable_ssl`:
To enable SSL on Logstash(to be accessed from agents). Default to `True`
- `ls_ssl_key_file_name`:
Private key file name. Default to `'{{ ls_host }}.key.pem'`
- `ls_ssl_cert_file_name`:
Certificate file name. Default to `'{{ ls_host }}.cert.pem'`
- `ls_local_certs`:
To find prepared certificate files on ansible host. If False - will try to fing certificates on remote host. Default to `True`
- `ls_local_key_path`:
Prepared private key file path. Default to `'{{ role_path }}/files/{{ ls_ssl_key_file_name }}'`
- `ls_local_cert_path`:
Prepared certificate file path. Default to `'{{ role_path }}/files/{{ ls_ssl_cert_file_name }}'`
- `ls_key_path`:
Private key file destination path. Default to `'{{ (ansible_os_family == "Debian") | ternary("/etc/ssl/private","/etc/pki/tls/private") }}'`
- `ls_cert_path`:
Certificate file destination path. Default to `'{{ (ansible_os_family == "Debian") | ternary("/etc/ssl/certs","/etc/pki/tls/certs") }}'`
- `ls_ssl_key_size`:
Private key encryption size (to create certificate if not exists). Default to `4096`
- `ls_ca_domain`:
  Certificate domain (to create certificate if not exists). Default to `'{{ ls_host }}'`
### https://docs.ansible.com/ansible/latest/openssl_certificate_module.html
- `ls_ssl_certificate_provider`:
Certificater provuider (to create certificate if not exists). Default to `selfsigned`

- `ls_plugin_bin`:
Logstash plugin utility. Default to `'{{ ls_dir }}/bin/logstash-plugin'`
- `ls_plugins`:
Plugins list to install to Logstash. Default to `[logstash-input-beats]`

- `ls_add_custom_filters`:
Add custom filter files. Default to `True`
- `ls_local_custom_filters`:
If True - get files from ansible host. Otherwise get from remote host folder. Default to `True`
- `ls_custom_filters_path`:
Custom filters folder. Default to `'{{ role_path }}/files/filters'`
- `ls_custom_filters_files`:
Custom filter files to use. Default to `[25-nginx.conf,26-apache.conf,27-solr.conf]`

- `ls_add_custom_grok_patterns`:
Add custom grok patterns. Default to `True`
- `ls_custom_grok_patterns_files`:
List of custom grok patterns. Default to `[grok_patterns.custom]`

- `ls_limits_set`:
To set OS limits. Default to `True`
- `ls_limits_nofile`:
Set maximum number of open files. Default to `204800`
- `ls_limits_stack`:
Set maximum stack size. Default to `4096`
- `ls_limits_nproc`:
Set maximum number of processes. Default to `24576`

- `ls_service_name`:
Logstash service name: Default to `logstash`
- `ls_service_autostart`:
Add Logstash service to automatically start. Default to `True`
- `ls_service_start`:
To start Logstash service after configuration. If False - do not start Logstash after installation (useful for e.g. packer image preparation). Default to `True`

- `elasticsearch_index_name`:
Elasticsearch index name. Default to `%{[@metadata][beat]}-%{[@metadata][version]}-%{+YYYY.MM.dd}`

- `es_apt_url`:
Path to official Elastic APT repository. Default to `deb https://artifacts.elastic.co/packages/{{ es_repo_name }}/apt stable main`
- `es_yum_url`:
Path to official Elastic RPM repository. Default to `https://artifacts.elastic.co/packages/{{ es_repo_name }}/yum`
- `es_repo_file`:
Configure repo header. Default to `elastic-{{ es_major_version }}`
- `elastic_gpg_key`:
GPG-key from elasticsearch repository. Default value is `https://artifacts.elastic.co/GPG-KEY-elasticsearch`.
- `es_use_oss_version`:
Installs open source software version (contains only features that are available under the Apache 2.0 license). Default to `false`

Dependencies
------------

- **Java 8** [![Build Status](https://travis-ci.org/lean-delivery/ansible-role-java.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-java)

Example Playbook
----------------

```yml
- name: Install and Configure Logstash
  hosts: logstash
  vars:
    solr_change_default_password: False
  roles:
    - role: lean_delivery.java
    - role: lean_delivery.logstash
  vars:
    elastic_branch: 6
    ls_config:
      dead_letter_queue.enable: false
      path.dead_letter_queue: "/opt/dead-queue"
      pipeline.workers: 4
      pipeline.output.workers: 1
      queue.type: persisted
      queue.page_capacity: 100mb
      queue.max_bytes: 10gb
      path.queue: "/opt/logstash-queue"
```

License
-------
Apache

Author Information
------------------

authors:
  - Lean Delivery Team <team@lean-delivery.com>
