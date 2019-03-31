ryezone_labs.sssd
=========

This role installs and configures sssd to connect to an ldap domain.

Role Variables
--------------

### Global Variables

- `ryezone_labs_domain_suffix` (string)

   Domain suffix to use when creating the domain.
   
   Defaults to `local`.

- `ryezone_labs_domain_organization` (string)

   Organization name to use when creating the domain.
   
   Defaults to `domain`.

- `ryezone_labs_domain_admin_password` (string)

   Password to use for the default admin account `cn=admin,dc={{ dc_org }},dc={{ dc_suffix }}`.
   
   Defaults to `My$uper$ecretP@$$w0rd`.

- `ryezone_labs_domain_controller_hostname` (string)

   Short hostname of a domain controller the host can connect to for authorization information.

   Defaults to `dc`

### Role Variables

- `sssd_distinguished_name` (string)

   Distinguished name of the domain being connected to.

   Defaults to `dc={{ ryezone_labs_domain_organization }},dc={{ ryezone_labs_domain_suffix}}`.

- `sssd_domain_controller` (string)

   LDAP Uri of the domain controller to which the host should connect.

   Defaults to `ldaps://{{ ryezone_labs_domain_controller_hostname }}.{{ ryezone_labs_domain_organization }}.{{ ryezone_labs_domain_suffix }}`

- `sssd_bind_dn` (string)

   LDAP account authorized to query the directory for authentication information.

   Defaults to `cn=admin,{{ sssd_distinguished_name }}`

- `sssd_bind_authtok` (string)

   Authentication to use with `sssd_bind_dn`.

   Defaults to `{{ ryezone_labs_domain_admin_password }}`.

- `sssd_bind_authtok_type` (string)

   Sets the type of bind authentication token being used.

   Defaults to `password`.

- `sssd_config_file_version` (int)

   Configures the file version of sssd.conf to use.

   Defaults to `2`.

- `sssd_config_services` (list of string)

   Configures the services provided by sssd.

   Defaults to `[nss, pam, sudo]`.

- `sssd_packages` (list of string)

   Default packages to install when installing sssd.

   Defaults to `[libnss-ldap, sssd, libnss-sss]`.

### Role Variables (nss)

- `sssd_nss` (hash)

   This hash mirrors the NSS configuration section documented in the [sssd.conf MAN page ](https://linux.die.net/man/5/sssd.conf).

   Defaults to:

   ```yaml
   sssd_nss:
     fallback_homedir: /home/%u
     shell_fallback: /bin/bash
     debug_level: 6
   ```

### Role Variables (pam)

- `sssd_pam` (hash)

   This hash mirrors the PAM configuration section documented in the [sssd.conf MAN page ](https://linux.die.net/man/5/sssd.conf).

   Defaults to:

   ```yaml
   sssd_pam: {}
   ```

### Role Variables (domain/DOMAIN)

- `sssd_domains` (hash)

   This hash mirrors the Domain Sections configuration section documented in the [sssd.conf MAN page ](https://linux.die.net/man/5/sssd.conf).

   Defaults to:

   ```yaml
   sssd_domains:
     default:
       id_provider: ldap
       auth_provider: ldap
       ldap_schema: rfc2307
       ldap_uri: "{{ sssd_domain_controller }}"
       ldap_default_bind_dn: "{{ sssd_bind_dn }}"
       ldap_default_authtok: "{{ sssd_bind_authtok }}"
       ldap_default_authtok_type: "{{ sssd_bind_authtok_type }}"
       ldap_search_base: "{{ sssd_distinguished_name }}"
       ldap_user_search_base: "ou=users,{{    sssd_distinguished_name }}"
       ldap_group_search_base: "ou=groups,{{    sssd_distinguished_name }}"
       ldap_sudo_search_base: "ou=sudoers,{{    sssd_distinguished_name }}"
       ldap_user_object_class: posixAccount
       ldap_user_gecos: cn
       sudo_provider: ldap
       cache_credentials: true
       enumerate: true
       use_fully_qualified_names: false
   ```

License
-------

BSD
