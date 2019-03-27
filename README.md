logstash role
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-logstash/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-logstash.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-logstash)
[![Build Status](https://gitlab.com/lean-delivery/ansible-role-logstash/badges/master/build.svg)](https://gitlab.com/lean-delivery/ansible-role-logstash)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.logstash-blue.svg)](https://galaxy.ansible.com/lean_delivery/logstash)
![Ansible](https://img.shields.io/ansible/role/d/role_id.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2Frole_id%2F&query=$.min_ansible_version)

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

- `logstash_host`:
Logstash host name. Default to `'{{ ansible_fqdn }}'`
- `logstash_port`:
Logstash port. Used for agents connection. Default to `5555`

- `elasticsearch_host`:
Specifies the address to which the elasticsearch server will bind. Default to `elasticsearch.example.com`
- `elasticsearch_port`:
Port on which elasticsearch listen to incoming requests. Default value is `9200`

## SSL certificate
- `logstash_enable_ssl`:
To enable SSL on Logstash(to be accessed from agents). Default to `True`
- `logstash_ssl_key_file_name`:
Private key file name. Default to `'{{ logstash_host }}.key.pem'`
- `logstash_ssl_cert_file_name`:
Certificate file name. Default to `'{{ logstash_host }}.cert.pem'`
- `logstash_local_certs`:
To find prepared certificate files on ansible host. If False - will try to fing certificates on remote host. Default to `True`
- `logstash_local_key_path`:
Prepared private key file path. Default to `'{{ role_path }}/files/{{ logstash_ssl_key_file_name }}'`
- `logstash_local_cert_path`:
Prepared certificate file path. Default to `'{{ role_path }}/files/{{ logstash_ssl_cert_file_name }}'`
- `logstash_key_path`:
Private key file destination path. Default to `'{{ (ansible_os_family == "Debian") | ternary("/etc/ssl/private","/etc/pki/tls/private") }}'`
- `logstash_cert_path`:
Certificate file destination path. Default to `'{{ (ansible_os_family == "Debian") | ternary("/etc/ssl/certs","/etc/pki/tls/certs") }}'`
- `logstash_ssl_key_size`:
Private key encryption size (to create certificate if not exists). Default to `4096`
- `logstash_ca_domain`:
  Certificate domain (to create certificate if not exists). Default to `'{{ logstash_host }}'`
### https://docs.ansible.com/ansible/latest/openssl_certificate_module.html
- `logstash_ssl_certificate_provider`:
Certificater provuider (to create certificate if not exists). Default to `selfsigned`

- `logstash_dir`:
Logstash directory. Default to `/usr/share/logstash`
- `logstash_plugin_bin`:
Logstash plugin utility. Default to `'{{ logstash_dir }}/bin/logstash-plugin'`
- `logstash_plugins`:
Plugins list to install to Logstash. Default to `[logstash-input-beats]`

- `logstash_add_custom_filters`:
Add custom filter files. Default to `True`
- `logstash_local_custom_filters`:
If True - get files from ansible host. Otherwise get from remote host folder. Default to `True`
- `logstash_custom_filters_path`:
Custom filters folder. Default to `'{{ role_path }}/files/filters'`
- `logstash_custom_filters_files`:
Custom filter files to use. Default to `[25-nginx.conf,26-apache.conf,27-solr.conf]`

- `logstash_add_custom_grok_patterns`:
Add custom grok patterns. Default to `True`
- `logstash_custom_grok_patterns_files`:
List of custom grok patterns. Default to `[grok_patterns.custom]`

- `logstash_limits_set`:
To set OS limits. Default to `True`
- `logstash_limits_nofile`:
Set maximum number of open files. Default to `204800`
- `logstash_limits_stack`:
Set maximum stack size. Default to `4096`
- `logstash_limits_nproc`:
Set maximum number of processes. Default to `24576`

- `logstash_service_autostart`:
Add Logstash service to automatically start. Default to `True`
- `logstash_service_start`:
To start Logstash service after configuration. Default to `True`

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
```

License
-------
Apache

Author Information
------------------

authors:
  - Lean Delivery Team <team@lean-delivery.com>
