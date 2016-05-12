Ansible Filebeat role
=========

[![Ansible Galaxy](https://img.shields.io/badge/galaxy-DavidWittman.filebeat-blue.svg?style=flat)](https://galaxy.ansible.com/detail#/role/6293)

Installs Elastic's Filebeat for forwarding logs.

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

 - `filebeat_ssl_certificate` - Contents of the SSL certificate that will be uploaded at `/etc/filebeat/filebeat.crt`. Make sure your config reflects the path.
 - `filebeat_confd` - Array of sub configurations to be placed in the `/etc/filebeat/conf.d/` folder. Useful if you have multiple host_groups for the same host. Again, make sure the `config_dir` option is set in your configuration.
  
  ``` yaml
  filebeat_confd:
    - name: conf_file_x # name of the config (without '.yml')
      config: # content for 'filebeat_config -> filebeat -> prospectors'
        - paths:
            - /var/log/messages
            - /var/log/*.log
          input_type: log 
    - name: conf_file_y
      config:
        - paths:
            - /var/log/messages_y
          input_type: log
  ```

Common Configurations
---------------------

Connecting to Elasticsearch:

  ``` yaml
  filebeat_config:
    filebeat:
      prospectors:
        - paths:
            - /var/log/messages
            - /var/log/*.log
          input_type: log
    output:
      elasticsearch:
        hosts:
          - "http://localhost:9200"
        username: "bob"
        password: "12345"
    logging:
      to_syslog: true
      level: error
  ```

License
-------

BSD

Author Information
------------------

David Wittman
