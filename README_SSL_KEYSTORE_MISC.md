# SSL Certificates and Keystores for the Beats input and Elasticsearch communication

[Beats Input plugin Settings](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-beats.html)

[Logstash Keystore Password](https://www.elastic.co/guide/en/logstash/current/keystore.html#keystore-password)

To enable logstash keystore set the variable:
`ls_enable_keystore: True`

Keystore Password set via `ls_keystore_pass: yourLSpassword` . Use non-empty string.

### Logstash Elasticsearch Security and TLS encryption
[Logstash ES Output Security](https://www.elastic.co/guide/en/logstash/current/ls-security.html)

We upload the local elasticsearch `.pem` Certificate Authority’s certificate 
 ```yaml
    ls_es_secure: True
    ls_es_user: elastic
    ls_es_pass: changeme
```
Encrypted communication with elasticsearch can be achieved with the either  
`es_pki: True` or `  es_ssl: True`

```yaml
    es_ssl: True
    es_ssl_cacert_file_name: cert.pem
    es_ssl_cacert: "files/certs/client/cert.pem"
```

```yaml
    es_pki: True
    es_keystore_filename: cert.pfx
    es_local_keystore: "files/certs/client/cert.pfx"
    es_keystore_pass: elastic
    es_truststore_filename: cert.pfx
    es_local_truststore: "files/certs/client/cert.pfx"
    es_truststore_pass: elastic

## Instalaton settings

### Install specific minro version from a custom  package url
 ```yaml
elastic_branch: 7
es_version: 7.6.2                                                                                                             
````
### Install from local source
You can disable the use of the repository with  `es_use_repository: False`
And set the custom location from which to install logstash.
```yaml
es_use_repository: False
logstash_custom_package_url: /local/path/logstash-7.8.1.rpm
```
    
    
### Example Configuration file with all settings enabled
```yaml
- name: Install and Configure Logstash
  hosts: logstash-node
  vars:
    solr_change_default_password: False
  roles:
 
    - role: lean_delivery.java
    - role: logstash
#    - role: lean_delivery.logstash
      #transport: repositories
      #java_major_version: 8
  vars:
    ls_enable_keystore: True
    ls_keystore_pass: somePAssword
    elastic_branch: 7
    es_version: 7.6.2
    ls_xms: 1g
    ls_xmx: 1g
    ls_plugins:
      - logstash-input-beats
      - logstash-filter-multiline
    elasticsearch_index_name: logstash-apache-type
    elasticsearch_host: "{{ ansible_default_ipv4.address }}"
    ls_enable_ssl: True
    ls_local_certs: True
    ls_ssl_key_passphrase: elastic
    ls_ssl_key_file_name: "client.key"
    ls_ssl_cert_file_name: "client.crt"
    ls_local_key_path: "files/certs/client/client.key"
    ls_local_cert_path: "files/certs/client/client.crt"
    ls_es_secure: True
    ls_es_user: elastic
    ls_es_pass: changeme
#    es_ssl: False
#    es_ssl_cacert_file_name: cert.pem
#    es_local_ssl_cacert: "files/certs/client/cert.pem"
    es_pki: True
    es_keystore_filename: cert.pfx
    es_local_keystore: "files/certs/client/cert.pfx"
    es_keystore_pass: elastic
    es_truststore_filename: cert.pfx
    es_local_truststore: "files/certs/client/cert.pfx"
    es_truststore_pass: elastic
    ls_config:
      dead_letter_queue.enable: false
      pipeline.workers: 4

```



### Debug Keystore
```shell script
export LOGSTASH_KEYSTORE_PASS=mypassword
/usr/share/logstash/bin/logstash-keystore list

```

