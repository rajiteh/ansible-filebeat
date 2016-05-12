Ansible Filebeat role
=========

[![Ansible Galaxy](https://img.shields.io/badge/galaxy-DavidWittman.filebeat-blue.svg?style=flat)](https://galaxy.ansible.com/detail#/role/6293)

Installs Elastic's Filebeat for forwarding logs.

Changelog
----------

### 12/05/2016
 * Ability to upload TLS certificate to target.
 * Optional configuration support for monit.
 
Role Variables
--------------

 - `filebeat_config` - YAML representation of your filebeat config. This is templated directly into the configuration file as YAML. See the [example configuration](https://github.com/elastic/filebeat/blob/master/etc/filebeat.yml) for an exhaustive list of configuration options. Defaults to:

  ``` yaml
  filebeat_config:
    filebeat:
      prospectors:
        - paths:
            - /var/log/messages
            - /var/log/*.log
          input_type: log
    output:
      file:
        path: /tmp/filebeat
        filename: filebeat
    logging:
      to_syslog: true
      level: error
  ```

You can also store the config in separate `filebeat.yml` file and include it using [lookup](http://docs.ansible.com/ansible/playbooks_lookups.html#intro-to-lookups-getting-file-contents):

```
filebeat_config: "{{ lookup('file', './filebeat.yml')|from_yaml }}"
```

 - `filebeat_ssl_certificate` - Contents of the SSL certificate that will be uploaded at `/etc/filebeat/forwarder.crt`. Make sure your config reflects the path.
 - `filebeat_monit_config` - Absolute path to place the monit config file. Optional. Will not be placed if not defined.

Common Configurations
---------------------

Connecting to Elasticsearch (with TLS):

  ``` yaml
  filebeat_config:
    filebeat:
      prospectors:
        - paths:
            - "/var/log/elasticsearch/elasticsearch_index_indexing_slowlog.log"
            - "/var/log/elasticsearch/elasticsearch_index_search_slowlog.log"
          fields:
            type: "elasticsearch_slow_logs"
            app: "elk"
    output:
      logstash:
        hosts: [ "localhost:5044" ]
        tls:
          certificate_authorities: [ "/etc/filebeat/forwarder.crt" ]
    logging:
      to_syslog: false
      to_files: true
      files:
        path: /var/log/filebeat
        name: filebeat.log
        rotateeverybytes: 10485760
        keepfiles: 7
      level: info
  ```

License
-------

BSD

Author Information
------------------

David Wittman
Raj Perera (@rajiteh)
