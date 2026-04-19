# ansible-role-squid
An Ansible role that deploys [Squid](http://www.squid-cache.org/) as an HTTP forward proxy on RHEL-family or Debian-family hosts.

Installs Squid, manages its configuration file and allow/block ACLs, and keeps the service running. Supports destination-domain allow/block lists, source-host allow/block lists, extra custom ACL definitions, and drop-in sub-module configs under `/etc/squid/conf.d/`.

## Requirements
* Squid is available from the OS package repositories (RHEL 7/8/9, Debian bookworm and similar)

## Usage

### 1. Define your ACLs

Override the relevant variables in your `group_vars` or `host_vars`. All available variables and their defaults are documented in [`defaults/main.yml`](defaults/main.yml).

```yaml
squid_port: 3128
squid_allowed_domains:
  - '.example.com'
squid_blocked_domains:
  - '.malware.example'
squid_allowed_hosts:
  - '10.0.0.0/8'
squid_blocked_hosts: []
```

### 2. Run the role

```yaml
---
- hosts: proxy_servers
  roles:
    - squid
```

### Example: full playbook with overrides

```yaml
---
- hosts: proxy_servers
  vars:
    squid_port: 3128
    squid_allowed_domains:
      - '.internal.example.com'
      - '.github.com'
    squid_blocked_domains:
      - '.social.example'
    squid_allowed_hosts:
      - '10.0.0.0/8'
      - '192.168.0.0/16'
    squid_access_control_list:
      - name: 'jenkins_ci'
        type: 'src'
        arg: '10.10.1.0/24'
    squid_http_access:
      - perm: 'allow'
        aclname: 'jenkins_ci'
  roles:
    - squid
```

## Custom sub-module configs

Drop-in configs can be placed under `/etc/squid/conf.d/` via `squid_custom_config`:

```yaml
squid_custom_config:
  - name: 'tenant_a'
    access_control:
      - name: 'tenant_a_domains'
        type: 'dstdomain'
        arg: '.tenant-a.example'
```

## License
GPLv3
