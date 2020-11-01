# [auditd](#auditd)

Install and configure auditd on your system.

|Travis|GitHub|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-auditd.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-auditd)|[![github](https://github.com/robertdebock/ansible-role-auditd/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-auditd/actions)|[![quality](https://img.shields.io/ansible/quality/41359)](https://galaxy.ansible.com/robertdebock/auditd)|[![downloads](https://img.shields.io/ansible/role/d/41359)](https://galaxy.ansible.com/robertdebock/auditd)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-auditd.svg)](https://github.com/robertdebock/ansible-role-auditd/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.auditd
      auditd_local_events: "no"
      auditd_rules:
        - file: /var/log/audit/
          keyname: auditlog
        - file: /etc/audit/
          permissions:
            - write
            - attribute_change
          keyname: auditconfig
        - file: /etc/libaudit.conf
          permissions:
            - write
            - attribute_change
          keyname: auditconfig
        - file: /etc/audisp/
          permissions:
            - write
            - attribute_change
          keyname: audispconfig
        - file: /sbin/auditctl
          permissions:
            - execute
          keyname: audittools
        - file: /sbin/auditd
          permissions:
            - execute
          keyname: audittools
        - syscall: open
          action: always
          filter: exit
          filters:
            - auid!=4294967295
            - auid!=unset
          keyname: my_keyname
          arch: b32
        - syscall: adjtimex
          action: always
          filter: exit
          keyname: time_change
        - syscall: settimeofday
          action: always
          filter: exit
          keyname: time_change
```

The machine needs to be prepared in CI this is done using `molecule/resources/prepare.yml`:
```yaml
---
- name: prepare
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
```
```
Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for auditd

auditd_buffer_size: 32768
auditd_fail_mode: 1
auditd_maximum_rate: 60
auditd_enable_flag: 1

auditd_local_events: "yes"
auditd_write_logs: "yes"
auditd_log_file: /var/log/audit/audit.log
auditd_log_group: root
auditd_log_format: RAW
auditd_flush: incremental_async
auditd_freq: 50
auditd_max_log_file: 8
auditd_num_logs: 5
auditd_priority_boost: 4
auditd_name_format: none
auditd_max_log_file_action: rotate
auditd_space_left: 75
auditd_space_left_action: syslog
auditd_verify_email: "yes"
auditd_action_mail_acct: root
auditd_admin_space_left: 50
auditd_admin_space_left_action: suspend
auditd_disk_full_action: suspend
auditd_disk_error_action: suspend
auditd_use_libwrap: "yes"
auditd_tcp_listen_queue: 5
auditd_tcp_max_per_addr: 1
auditd_tcp_client_max_idle: 0
auditd_enable_krb5: "no"
auditd_krb5_principal: auditd
auditd_distribute_network: "no"

auditd_manage_rules: yes

auditd_default_arch: b64
```

## [Requirements](#requirements)

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

## [Status of requirements](#status-of-requirements)

| Requirement | Travis | GitHub |
|-------------|--------|--------|
| [robertdebock.bootstrap](https://galaxy.ansible.com/robertdebock/bootstrap) | [![Build Status Travis](https://travis-ci.com/robertdebock/ansible-role-bootstrap.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-bootstrap) | [![Build Status GitHub](https://github.com/robertdebock/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-bootstrap/actions) |

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/auditd.png "Dependency")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|el|7, 8|
|debian|buster, bullseye|
|fedora|all|
|opensuse|all|
|ubuntu|focal, bionic|

The minimum version of Ansible required is 2.9, tests have been done to:

- The previous version.
- The current version.
- The development version.

## [Exceptions](#exceptions)

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| alpine | auditd start-stop-daemon: /sbin/auditd does not exist |
| amazonlinux:1 | /etc/init.d/auditd: line 32: /etc/init.d/functions: No such file or directory |


## [Testing](#testing)

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-auditd) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-auditd/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## [License](#license)

Apache-2.0

## [Contributors](#contributors)

I'd like to thank everybody that made contributions to this repository. It motivates me, improves the code and is just fun to collaborate.

- [benformosa](https://github.com/benformosa)

## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
