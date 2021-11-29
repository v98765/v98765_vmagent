Role Name: Victoriamertics vmagent
=========

Deploy and configure [vmagent](https://victoriametrics.github.io/vmagent.html)

Requirements
------------

Ansible 2.10 , ubuntu 20+

Role Variables
--------------

Name | Default Value | Description
---|---|---
`vmagent_version` |  1.69.0 | current version
`vmagent_system_user` |  "vmagent" | 
`vmagent_system_group` |  "vmagent" | 
`vmagent_config_dir` |  "/etc/vmagent" | 
`vmagent_install_dir` |  "/usr/local/bin" | 
`vmagent_repo_dir` |  "/var/tmp/archive" | 
`vmagent_promscrape_config` |  "{{ vmagent_config_dir }}/vmagent.yml" | Optional path to Prometheus config file with 'scrape_configs' section containing targets to scrape. See [how-to-scrape-prometheus-exporters-such-as-node-exporter](https://victoriametrics.github.io/#how-to-scrape-prometheus-exporters-such-as-node-exporter) for details
`vmagent_remote_write_tmp_data_path` |  "/var/lib/vmagent" | Path to directory where temporary data for remote write component is stored
`vmagent_remote_write_url` |  "http://127.0.0.1:8428/api/v1/write" | Remote storage URL to write data to. It must support Prometheus remote_write API. It is recommended using VictoriaMetrics as remote storage. Example url: http://<victoriametrics-host>:8428/api/v1/write . Pass multiple -remoteWrite.url flags in order to write data concurrently to multiple remote storage systems
`vmagent_promscrape_suppress_scrape_errors` |  true |  Whether to suppress scrape errors logging. The last error for each target is always available at '/targets' page even if scrape errors logging is suppressed
`vmagent_remote_write_relabel_config` |  | Optional path to file with relabel_config entries. These entries are applied to all the metrics before sending them to -remoteWrite.url. See [relabeling](https://victoriametrics.github.io/vmagent.html#relabeling) for details
`vmagent_remote_write_url_relabel_config` |  |

Read this [https://docs.victoriametrics.com/#environment-variables](https://docs.victoriametrics.com/#environment-variables) аnd set more vars, put it into `templates/vmagent.j2`

```sh
mkdir -p /var/tmp/archive
cd /var/tmp/archive
wget https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/v1.69.0/vmutils-amd64-v1.69.0.tar.gz
```

Example Playbook
----------------

```yaml
---
- hosts: exporters
  gather_facts: true
  connection: ssh
  roles:
    - v98765_vmagent
```

License
-------

BSD
