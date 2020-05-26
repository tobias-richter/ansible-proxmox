# tobias_richter.proxmox_backup

[![Build Status](https://travis-ci.org/tobias-richter/ansible-proxmox.svg?branch=master)](https://travis-ci.org/tobias-richter/ansible-proxmox)

This roles takes care of configuring some parts of the Proxmox virtual environment:

* lxc template download
* sysctl configuration
* special lxc container configurations (e.g. allow zigbee stick in container, allow tinc vpn in container)
* ldap auth domains
* zvdump tmp dir
* influxdb monitoring backend
* postfix mail relay configuration for sending mails
* dns domain
* nameserver

## Requirements

This role requires Ansible 2.7 or higher.

## Role Variables

Available variables are listed below, along with their default values (see [defaults/main.yml](defaults/main.yml)):

    pve_templates: []
      #- debian-10.0-standard_10.0-1_amd64.tar.gz
      #- ubuntu-18.04-standard_18.04.1-1_amd64.tar.gz

Defines the templates to be provisioned / downloaded.

    pve_sysctl: []
      #- name: kernel.shmall
      #  value: 4194304
      #- name: kernel.shmmax
      #  value: 17179869184
      
Define sysctl values to be configured.

    pve_ldap_domains: []
      #- name: "My name"
      #  password: "bind password"
      #  server: "ldap.corp
      #  bind_dn: "uid=ldapbind,ou=users,dc=ldap,dc=corp"
      #  base_dn: "ou=users,dc=ldap,dc=corp"
      #  user_attr: "uid"
      #  default: 1
      #  secure: 1

Configure ldap auth domains.

    pve_vzdump_tmpdir: /opt/vzdump-tmp

Path to the pve_vzdum_tmpdir.


pve_lxc_config: []
    #  - id: 111
    #    options:
    #      # allow tunnel creation on lxc for VPN
    #      - name: lxc.cgroup.devices.allow
    #        value: "c 10:200 rwm"
    #        state: absent
    #      - name: lxc.cap.keep
    #        value: "sys_module mknod"
    #        state: absent
    #      - name: lxc.mount.entry
    #        value: /dev/net/tun dev/net/tun none bind,create=file
    #  - id: 117
    #    options:
    #      # allow zigbee stick in container
    #      - name: lxc.cgroup.devices.allow
    #        value: "c 166:* rwm"
    #        state: present
    #      - name: lxc.mount.entry
    #        value: /dev/ttyACM0 dev/ttyACM0 none bind,optional,create=file

Custom lxc configurations. See commented lines for examples.

    # pve_influxdb_port:
    # pve_influxdb_host:

Configure influxdb monitoring.

    # pve_mail_relay_host:
    # pve_mail_relay_port:
    # pve_mail_relay_username:
    # pve_mail_relay_password:
    pve_mail_domain: proxmox
    # pve_mail_default_recipient: proxmox
    pve_mail_smtp_tls_wrappermode: "yes"
    pve_mail_smtp_use_tls: "yes"

Postfix relay configuration for sending mails with proxmox.

    pve_dns_domain: local

Configure dns search domain.

    pve_nameserver: 8.8.8.8

Cconfigure primary mailserver (google is default).


## Example Playbook

This playbook downloads the lxc template for Debian 10 / Buster and applies configurations
when you want to run gitlab in an lxc container.

    - hosts: pve
	  roles:
	    - role: tobias_richter.proxmox
	      # prepare debian template
	      pve_templates:
            - debian-10.0-standard_10.0-1_amd64.tar.gz
          # kernel configs for gitlab when running in lxc container 
          pve_sysctl:
            - name: kernel.shmall
              value: 4194304
            - name: kernel.shmmax
              value: 17179869184