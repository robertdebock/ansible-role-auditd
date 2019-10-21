auditd
=========

<img src="https://docs.ansible.com/ansible-tower/3.2.4/html_ja/installandreference/_static/images/logo_invert.png" width="10%" height="10%" alt="Ansible logo" align="right"/>
<a href="https://travis-ci.org/robertdebock/ansible-role-auditd"><img src="https://travis-ci.org/robertdebock/ansible-role-auditd.svg?branch=master" alt="Build status" align="left"/></a>

Install and configure auditd on your system.

<img src="https://img.shields.io/ansible/role/d/41359"/>
<img src="https://img.shields.io/ansible/quality/41359"/>

Example Playbook
----------------

This example is taken from `molecule/resources/playbook.yml`:
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.auditd
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
          keyname: my_keyname
        - syscall: adjtimex
          action: always
          filter: exit
          keyname: time_change
        - syscall: settimeofday
          action: always
          filter: exit
          keyname: time_change
```

The machine you are running this on, may need to be prepared.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - robertdebock.bootstrap
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

Role Variables
--------------

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for auditd
auditd_buffer_size: 32768
auditd_fail_mode: 1

auditd_local_events: "yes"
auditd_write_logs: "yes"
auditd_log_file: /var/log/audit/audit.log
auditd_log_group: root
auditd_log_format: RAW
auditd_flush: INCREMENTAL_ASYNC
auditd_freq: 50
auditd_max_log_file: 8
auditd_num_logs: 5
auditd_priority_boost: 4
auditd_disp_qos: lossy
auditd_dispatcher: /sbin/audispd
auditd_name_format: NONE
auditd_max_log_file_action: ROTATE
auditd_space_left: 75
auditd_space_left_action: SYSLOG
auditd_verify_email: "yes"
auditd_action_mail_acct: root
auditd_admin_space_left: 50
auditd_admin_space_left_action: SUSPEND
auditd_disk_full_action: SUSPEND
auditd_disk_error_action: SUSPEND
auditd_use_libwrap: "yes"
auditd_tcp_listen_queue: 5
auditd_tcp_max_per_addr: 1
auditd_tcp_client_max_idle: 0
auditd_enable_krb5: "no"
auditd_krb5_principal: auditd
auditd_distribute_network: "no"
```

Requirements
------------

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap

```

This role uses the following modules:
```yaml
---
- command
- file
- package
- service
- template
```

Context
-------

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/auditd.png "Dependency")


Compatibility
-------------

This role has been tested on these [container images](https://hub.docker.com/):

|container|tag|allow_failures|
|---------|---|--------------|
|docker-alpine-openrc|latest|no|
|docker-alpine-openrc|edge|yes|
|docker-debian-systemd|stable|yes|
|docker-debian-systemd|unstable|yes|
|docker-debian-systemd|latest|no|
|docker-centos-systemd|7|no|
|docker-redhat-systemd|7|no|
|docker-centos-systemd|latest|no|
|docker-redhat-systemd|latest|no|
|docker-fedora-systemd|latest|no|
|docker-fedora-systemd|rawhide|yes|
|docker-opensuse-systemd|latest|no|
|docker-ubuntu-systemd|rolling|yes|
|docker-ubuntu-systemd|devel|yes|
|docker-ubuntu-systemd|latest|no|

This role has been tested on these Ansible versions:

- ansible~=2.7
- ansible~=2.8
- git+https://github.com/ansible/ansible.git@devel

The indicator '\~=' means [compatible with](https://www.python.org/dev/peps/pep-0440/#compatible-release). For example 'ansible\~=2.8' would pick the latest ansible-2.8, for example ansible-2.8.6.




Testing
-------

[Unit tests](https://travis-ci.org/robertdebock/ansible-role-auditd) are done on every commit and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-auditd/issues)

To test this role locally please use [Molecule](https://github.com/ansible/molecule):
```
pip install molecule
molecule test
```

To test on Amazon EC2, configure [~/.aws/credentials](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html) and set a region using `export AWS_REGION=eu-central-1` before running `molecule test --scenario-name ec2`.

There are many specific scenarios available, please have a look in the `molecule/` directory.

License
-------

Apache-2.0


Author Information
------------------

[Robert de Bock](https://robertdebock.nl/)
