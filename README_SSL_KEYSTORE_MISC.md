## Logstash Keystore, Secure SSL Elasticsearch communication and upload SSL Certificates. 

### Logstash Keystore
[Logstash Keystore Password](https://www.elastic.co/guide/en/logstash/current/keystore.html#keystore-password)

To enable logstash keystore set the variable:
`ls_enable_keystore: True`

Keystore Password set via `ls_keystore_pass: yourLSpassword` . Use non-empty string.
```yaml
ls_enable_keystore: True
ls_keystore_pass: yourLSKeyStorepass
```
### Logstash Elasticsearch Security and TLS encryption
[Logstash ES Output Security](https://www.elastic.co/guide/en/logstash/current/ls-security.html)

Set security in the elasticsearch output configuration. 
 ```yaml
    ls_es_secure: True
    ls_es_user: elastic
    ls_es_pass: changeme
```
If keystore enabled then the `ls_es_user` and `ls_es_pass` are accessed in the config files via
```shell script
user => "${ES_USER}"
password => "${ES_PASS}"
```
We upload the local elasticsearch `.pem/pfx` certificate file. 

Encrypted communication with elasticsearch can be achieved with either,
  
`es_pki: True` or `  es_ssl: True`

You can disable the ssl verification via  `es_ssl_verification: "false"`

For pki use these settings,
```yaml
    es_pki: True
    es_keystore_filename: cert.pfx
    es_local_keystore: "files/certs/client/cert.pfx"
    es_keystore_pass: elastic
    es_truststore_filename: cert.pfx
    es_local_truststore: "files/certs/client/cert.pfx"
    es_truststore_pass: elastic
    es_ssl_verification: "true"

```
If keystore is enabled via `ls_enable_keystore: True` then the passwords are referenced in the config files via
```shell script
keystore_password => "${ES_KEYSTORE_PASS}"
truststore_password => "${ES_TRUSTSTORE_PASS}"
```
Alternatively we can set the SSL settings,
```yaml
    es_ssl: True
    es_ssl_cacert_file_name: cert.pem
    es_ssl_cacert: "files/certs/client/cert.pem"
    es_ssl_verification: "true"

```

### Installation settings

#### Install a specific version or from a custom  package url
 ```yaml
elastic_branch: 7
es_version: 7.6.2
````
#### Install from local source
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
  vars:
    ls_enable_keystore: True
    ls_keystore_pass: mypassword
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
    es_ssl_verification: "false"
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
### ToDo
[Beats Input plugin Settings](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-beats.html)
Fix `File does not contain valid private key` issue for the Beats SSL input.
Keep `ls_enable_ssl: False` for now.
Even when uploading PKCS#8 key file.

### Debug Tips

#####  Keystore 

```shell script
export LOGSTASH_KEYSTORE_PASS=mypassword
/usr/share/logstash/bin/logstash-keystore list  --path.settings /etc/logstash

```
 ##### Sample Apache Log file
 ```shell script
wget https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/apache_logs/apache_logs
mv apache_logs /tmp/access_log
```
Alternatively run this playbook
```yaml
- name: Download sample apache log file for testing the file input pipeline 
  hosts: logstash-node
  tasks:
    - name: Download the file
      get_url:
          url: https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/apache_logs/apache_logs
          dest: /tmp/access_log
```

######## Generate your own CA file for HTTPS connectivity to the elasticsearch cluster.
```shell
openssl s_client -showcerts \
-connect es-node.example.com:443 \
</dev/null 2>/dev/null|openssl x509 \
-outform PEM >server-ca.pem
```