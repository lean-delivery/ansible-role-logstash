# SSL Certificates and Keystores for the Beats Input and Elasticsearch communication

[Beats Imnput plugin Settings](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-beats.html)

[Logstash Keystore Password](https://www.elastic.co/guide/en/logstash/current/keystore.html#keystore-password)

To enable logstash keystore set the variable:
`ls_enable_keystore: True`

Keystore Password is set via `ls_keystore_pass: yourLSpassword` . Set to non-empty string.

### Logstash Elasticsearch Security and TLS encryption
[Logstash ES Output Security](https://www.elastic.co/guide/en/logstash/current/ls-security.html)

We do not generate Elastic
 ```shell script
    ls_es_secure: True
    ls_es_user: elastic
    ls_es_pass: changeme
    es_ssl: True
    es_ssl_cacert_file_name:
    es_ssl_cacert: "files/certs/client/cert.pem"
```
### Example Configuration file with all settings enabled
```
- name: Install and Configure Logstash
  hosts: logstash-node
  vars:
    solr_change_default_password: False
  roles:
          #    - role: lean_delivery.logstash
    - role: logstash
    - role: lean_delivery.java
      transport: repositories
      java_major_version: 8
  vars:
    ls_enable_keystore: True
    ls_keystore_pass: somePAssword
    elastic_branch: 7
    ls_xms: 128M
    ls_xmx: 128M
    ls_local_certs: True
    ls_ssl_key_passphrase: elastic
    ls_ssl_key_file_name: "client.key"
    ls_ssl_cert_file_name: "client.crt"
    ls_local_key_path: "files/certs/client/client.key"
    ls_local_cert_path: "files/certs/client/client.crt"
    ls_es_secure: True
    ls_es_user: elastic
    ls_es_pass: changeme
    es_ssl: True
    es_ssl_cacert_file_name: cert.pem
    es_local_ssl_cacert: "files/certs/client/cert.pem"
    ls_config:
      dead_letter_queue.enable: false
      pipeline.workers: 4
      pipeline.output.workers: 1
      queue.type: memory
```



### Debug Keystore
```shell script
export LOGSTASH_KEYSTORE_PASS=mypassword
/usr/share/logstash/bin/logstash-keystore list

```

