# Secrets Ansible Role

This role takes user provided secrets and makes them available to applications being deployed. Currently it accepts a list of environment variables and stores them in a ``/etc/sysconfig/app-secrets`` file on CentOS and Fedora Vagrant VMs. Systemd units, such as [the one managed by the IDI ansible-nodejs role](https://github.com/idi-ops/ansible-nodejs/blob/master/templates/systemd_unit.j2), can then [access the variables](http://www.freedesktop.org/software/systemd/man/systemd.exec.html#EnvironmentFile=) stored in that file.

## Requirements

The following roles are required:

*  [facts](https://github.com/idi-ops/ansible-facts/)

## Example

To use this role a file, perhaps named ``secrets.yml``, should be created but not committed to a repository. It should contain a list of environment variables in ``KEY=value`` format. For example:

```
secrets_env_vars:
  - SECRET1=password
  - SECRET2=token
```

A playbook could use this role like this:

```
---
- hosts: localhost
  user: root

  vars_files:
    - vars.yml

  pre_tasks:
    - include_vars: secrets.yml
      ignore_errors: yes
      tags:
        - always

  roles:
    - facts
    - secrets
```

``ignore_errors`` is being used, making the presence of ``secrets.yml`` optional. The playbook will need be executed using the ``configure`` tag. For example:

```
sudo ansible-playbook your-playbook.yml --tags="configure"
```
