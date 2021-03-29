Role Name: Victoriamertics vmagent
=========

Deploy and configure [vmagent](https://victoriametrics.github.io/vmagent.html)

Requirements
------------

Ansible 2.10

Role Variables
--------------

Name | Default Value | Description
---|---|---
`vmagent_version` |  1.55.1 | current version
`vmagent_system_user` |  "vmagent" | 
`vmagent_system_group` |  "vmagent" | 
`vmagent_config_dir` |  "/etc/vmagent" | 
`vmagent_install_dir` |  "/usr/local/bin" | 
`vmagent_repo_dir` |  "/var/tmp/archive" | 
`vmagent_promscrape_config` |  "{{ vmagent_config_dir }}/vmagent.yml" | Optional path to Prometheus config file with 'scrape_configs' section containing targets to scrape. See [how-to-scrape-prometheus-exporters-such-as-node-exporter](https://victoriametrics.github.io/#how-to-scrape-prometheus-exporters-such-as-node-exporter) for details
`vmagent_remoteWrite_tmpDataPath` |  "/var/lib/vmagent" | Path to directory where temporary data for remote write component is stored
`vmagent_remoteWrite_url` |  "http://127.0.0.1:8428/api/v1/write" | Remote storage URL to write data to. It must support Prometheus remote_write API. It is recommended using VictoriaMetrics as remote storage. Example url: http://<victoriametrics-host>:8428/api/v1/write . Pass multiple -remoteWrite.url flags in order to write data concurrently to multiple remote storage systems
`vmagent_promscrape_suppressScrapeErrors` |  true |  Whether to suppress scrape errors logging. The last error for each target is always available at '/targets' page even if scrape errors logging is suppressed
`vmagent_remoteWrite_relabelConfig` |  "{{ vmagent_config_dir }}/rewrite.yml" | Optional path to file with relabel_config entries. These entries are applied to all the metrics before sending them to -remoteWrite.url. See [relabeling](https://victoriametrics.github.io/vmagent.html#relabeling) for details
`vmagent_influxTrimTimestamp` |  "1s" | Trim timestamps for Influx line protocol data to this duration. Minimum practical duration is 1ms. Higher duration (i.e. 1s) may be used for reducing disk space usage for timestamp data (default 1ms)
`vmagent_http_maxGracefulShutdownDuration` |  "10s" | The maximum duration for graceful shutdown of HTTP server. Highly loaded server may require increased value for graceful shutdown (default 7s)

Read this [https://github.com/VictoriaMetrics/VictoriaMetrics#environment-variables](https://github.com/VictoriaMetrics/VictoriaMetrics#environment-variables) аnd set more vars, put it into `templates/vmagent.j2`

```sh
mkdir -p /var/tmp/archive
cd /var/tmp/archive
wget https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/v1.55.1/vmutils-amd64-v1.55.1.tar.gz
wget -O vmutils_checksums.txt https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/v1.55.1/vmutils-arm64-v1.55.1_checksums.txt
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

  tasks:
    - name: copy vmagent targets
      copy:
        src: "{{ item }}"
        dest: "{{ vmagent_config_dir }}/targets/"
        force: true
        owner: root
        group: vmagent
        mode: 0644
      with_fileglob: "{{ vmagent_targets_files }}"
      tags:
        - vmagent_configure
```
host_vars/exporters.yml
```yaml
vmagent_targets_files:
  - targets/*.yml
```
targets/some.yml
```yaml
---
- labels:
    dc: a
    rack: b
  targets:
    - some01
    - some02
```

License
-------

BSD
