# Ansible role [influxdb2](https://galaxy.ansible.com/ui/standalone/roles/buluma/influxdb2/documentation)

Install and configure InfluxDB 2.0 using Ansible.

|GitHub|Version|Issues|Pull Requests|Downloads|
|------|-------|------|-------------|---------|
|[![github](https://github.com/buluma/ansible-role-influxdb2/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-influxdb2/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-influxdb2.svg)](https://github.com/buluma/ansible-role-influxdb2/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-influxdb2.svg)](https://github.com/buluma/ansible-role-influxdb2/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-influxdb2.svg)](https://github.com/buluma/ansible-role-influxdb2/pulls/)|[![Ansible Role](https://img.shields.io/ansible/role/d/buluma/influxdb2)](https://galaxy.ansible.com/ui/standalone/roles/buluma/influxdb2/documentation)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-influxdb2/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  gather_facts: true
  become: yes
  vars:
    apt_autostart_state: enabled
    pip_package: python3-pip
    pip_executable: "{{ 'pip3' if pip_package.startswith('python3') else 'pip' }}"
    pip_install_packages:
      - name: setuptools
      - name: requests

  pre_tasks:
    - name: Update apt cache.
      apt: update_cache=true cache_valid_time=600
      when: ansible_os_family == 'Debian'

    - name: Set package name for older OSes.
      ansible.builtin.set_fact:
        pip_package: python-pip
      when: >
        (ansible_os_family == 'RedHat') and (ansible_distribution_major_version | int < 8)
        or (ansible_distribution == 'Debian') and (ansible_distribution_major_version | int < 10)
        or (ansible_distribution == 'Ubuntu') and (ansible_distribution_major_version | int < 18)
  roles:
    - role: buluma.influxdb2
      influxdb_orgs:
        - name: main-org
          description: Main organization
        - name: guest-org

      influxdb_users:
        - name: admin01
          org: main-org
          password: secretPassword
        - name: guest01
          org: guest-org
          password: secretPassword

      influxdb_buckets:
        - name: bucket01
          description: First bucket
          org: main-org
          retention: 1d
        - name: bucket02
          org: main-org
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-influxdb2/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare container
  hosts: all
  gather_facts: true
  become: yes
  serial: 30%
  vars:
    apt_autostart_state: enabled

  roles:
    - role: buluma.bootstrap
    - role: buluma.apt_autostart
    - role: buluma.pip
    - name: buluma.influxdb2

  post_tasks:
    - name: place /environmentfile.txt
      ansible.builtin.copy:
        content: "value=influxdb"
        dest: /environmentfile.txt
        mode: "0644"
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-influxdb2/blob/master/defaults/main.yml):

```yaml
---
influxdb_dependencies:
  - apt-transport-https
  - curl
  - gnupg

influxdb_packages:
  - influxdb2
  - influxdb2-cli

influxdb_package_state: present

influxdb_config_path: /etc/influxdb
influxdb_bolt_path: /var/lib/influxdb/influxd.bolt
influxdb_engine_path: /var/lib/influxdb/engine

influxdb_host: http://localhost:8086

influxdb_config: {}
#  http-bind-address: 0.0.0.0:8086
#  reporting-disabled: true

influxdb_primary_org: example-org
influxdb_primary_bucket: example-bucket
influxdb_primary_username: example-user
influxdb_primary_password: ExAmPl3PA55W0rD

# Set your root token for admin user
influxdb_admin_token: EXAMPLE-TOKEN

influxdb_orgs: []
#  - name: main-org
#    description: Main organization
#  - name: guest-org

influxdb_users: []
#  - name: admin01
#    org: main-org
#    password: secretPassword
#  - name: guest01
#    org: guest-org
#    password: secretPassword

influxdb_buckets: []
#  - name: bucket01
#    description: First bucket
#    org: main-org
#    retention: 1d
#  - name: bucket02
#    description: Second bucket
#    org: main-org

influxdb_service_enabled: true

influxdb_service_state: started

influxdb_skip_onboarding: false
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-influxdb2/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | Version |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Ansible Molecule](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bootstrap.svg)](https://github.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.apt_autostart](https://galaxy.ansible.com/buluma/apt_autostart)|[![Ansible Molecule](https://github.com/buluma/ansible-role-apt_autostart/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-apt_autostart/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-apt_autostart.svg)](https://github.com/shadowwalker/ansible-role-apt_autostart)|
|[buluma.pip](https://galaxy.ansible.com/buluma/pip)|[![Ansible Molecule](https://github.com/buluma/ansible-role-pip/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-pip/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-pip.svg)](https://github.com/shadowwalker/ansible-role-pip)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-influxdb2/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[Debian](https://hub.docker.com/repository/docker/buluma/debian/general)|all|
|[Ubuntu](https://hub.docker.com/repository/docker/buluma/ubuntu/general)|all|
|[Kali](https://hub.docker.com/repository/docker/buluma/kali/general)|all|

The minimum version of Ansible required is 2.12, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-influxdb2/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-influxdb2/blob/master/CHANGELOG.md)

## [License](#license)

[MIT](https://github.com/buluma/ansible-role-influxdb2/blob/master/LICENSE)

## [Author Information](#author-information)

[Shadow Walker](https://buluma.github.io/)

