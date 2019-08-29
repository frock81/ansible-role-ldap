frock81.openldap
================

A brief description of the role goes here.

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

### Simple Example

    - hosts: all
      vars:
        openldap_slapd_domain: myorg.com
        openldap_slapd_organization: My Organization
        openldap_slapd_admin_password: supersecret
      roles:
        - frock81.openldap

### Complex Example

    ---
    - hosts: ldap_replicas
      gather_facts: no #controlled machines dont have /usr/bin/python
      vars:
        php_packages:
          - libapache2-mod-php7.0
        openldap_slapd_domain: ic.pcdf.gov.br
        openldap_slapd_organization: Instituto de Criminalística
        openldap_slapd_purge_database: true
        openldap_slapd_services: "{{ hosted_openldap_slapd_services }}"
        openldap_rootpw_remove: true
        openldap_configdit_admin_enable: true
        openldap_repl_enable: true
        openldap_repl_servers:
          - id: 1
            uri: ldaps://ldap01.ic.pcdf.gov.br:636
          - id: 2
            uri: ldaps://ldap02.ic.pcdf.gov.br:636
        openldap_pla_enable: true
        openldap_schemas_add:
          - file: samba.ldif
            name: samba
          - file: openssh-lpk-openldap.ldif
            name: openssh-lpk-openldap
          - file: ic_schema.ldif
            name: ic
        openldap_seed_enable: true
        openldap_seed_ldif_file: ic.ldif
        openldap_seed_continue_on_errors: true
        openldap_seed_skipped_entries_file: /vagrant/skipped.ldif
        openldap_seed_output_log_file: /vagrant/ldap_output
        opeldap_seed_ouput_verbose: true
        openldap_tls_enable: true
        openldap_tls_ca_cert_file: cacert.pem
        openldap_tls_cert_file: ldap.ic.pcdf.gov.br-crt.pem
        openldap_tls_key_content: "{{ vaulted_openldap_tls_key_content }}"
        openldap_tls_key_filename: ldap.ic.pcdf.gov.br-key.pem
        openldap_ldap_conf_enable: true
        openldap_ldap_conf_reqcert: never
      vars_files:
        - vars/secret.yml
      pre_tasks:
        - include_role:
            name: frock81.ansible_bootstrap
        - name: hosts file
          lineinfile:
            path: /etc/hosts
            regexp: "^{{ item.ip }} "
            line: "{{ item.ip }} {{ item.host }}"
          with_items:
            - ip: 192.168.4.11
              host: ldap01.ic.pcdf.gov.br
            - ip: 192.168.4.12
              host: ldap02.ic.pcdf.gov.br
      roles:
        - role: frock81.ansible_bootstrap
        - role: frock81.ntp
          tags: ntp
        - role: geerlingguy.apache
        - role: geerlingguy.php
        - role: frock81.openldap

References
----------

### LDAP

- https://www.redpill-linpro.com/techblog/2016/08/16/ldap-password-hash.html
- https://www.openldap.org/faq/data/cache/1347.html
- https://www.digitalocean.com/community/tutorials/how-to-change-account-passwords-on-an-openldap-server
- https://www.openldap.org/faq/data/cache/1467.html
- https://guillaumemaka.com/2013/07/17/openldap-create-a-custom-ldap-schema.html

### Ansible

- https://docs.ansible.com/ansible/latest/modules/ldap_entry_module.html
- https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#hashing-filters
- https://github.com/unchained-capital/ansible-ldap-modules

### Debian/Ubuntu

- https://wiki.debian.org/LDAP/OpenLDAPSetup#Access_control
- https://wiki.debian.org/LDAP
- https://help.ubuntu.com/lts/serverguide/samba-ldap.html.en

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
