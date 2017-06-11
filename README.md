firewall
========

Manage all firewall systems. Implemented firewalld based systems.

Requirements
------------

- `ansible` >= 2.4.0
- `firewalld` >= 0.2.11
- `python-firewall`
- `python3-firewall` (if used python version 3)

Role Variables
--------------

This role does not use defaults for variables. Users should set all required
variables themself.

### Used variables

- `firewall_type` - `string` - specify used firewall,  
   if not specified, default is detect by OS and its major version
- `firewall_firewalld_zones` - `dictionary` - definition of firewall zones used by  
   `firewalld` system.
- `firewall_firewalld_services` - `array` - definition of custom services

#### Structure of `firewall_zones` dictionary

`firewall_firewalld_zones` dictionary defines all zones and its sources, interfaces,
rich rules, services. All items are required, and must be valid firewalld zone
names, sources, interfaces, rich rules and services. Sources, interfaces,
rich rules and services can be empty array (services: []).

```yaml
firewall_firewalld_zones:
  - name: ZoneName
    ports:
      - 81/tcp
      - 54-55/tcp
      - 53/udp
    services: # Names of services as defined in firewalld
      - http
      - ssh
      - rsync
      - dns
      - ftp
    interfaces: # Names of interfaces belonging to this zone
      - eth0
      - br0
      - docker0
      - virbr0
      - enp0s25
    sources: # Sources IP addresses (both types IPv4 and IPv6)
      - 127.0.0.1/8
      - 10.88.32.0/24
      - 10.88.48.0/20
    rich_rules: # rich rules in grammer used by firewalld
      - 'rule service name="ftp" audit limit value="1/m" accept'
      - 'rule service name="http" audit limit value="1/m" accept'
    masquerade: false # true or false

```

Empty zone definition:

```yaml
firewall_firewalld_zones:
  - name: ZoneName
    ports: [] # none ports
    services: [] # no services
    interfaces: [] # no interfaces
    sources: [] # no sources
    rich_rules: [] # no rich rules
    masquerade: false # true or false
```

#### Structure of `firewall_firewalld_services`

`firewall_firewalld_services` defines custom services as xml definition file
with syntax same as firewalld service xml file.

xml file is generated by template or copied. Copied is if `src`
attribute is present. Generated is if `template` attribute is present.

```yaml
firewall_firewalld_services:
  - src: myservice.xml
    service: myservice
    owner: root
    group: root
    mode: u=rw,g=rw,o=r
    seuser: system_u
    serole: object_r
    setype: firewalld_etc_rw_t
    selevel: s0
  - template: hisservice.xml.j2
    service: hisservice
    owner: root
    group: root
    mode: u=rw,g=rw,o=r
    seuser: system_u
    serole: object_r
    setype: firewalld_etc_rw_t
    selevel: s0
```

### Variables defined in vars


- `firewall_known_types` - array - known types of firewall system, that this  
   role handle.
- `firewall_defaults` - array - default firewall type used for OS/version  
    if not specified by firewall_type var.

Dependencies
------------

This role does not depend on other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD-3-Clause - see LICENSE.txt

Author Information
------------------

This role was authored by Tomas Dobrovolny <sdobrtomas@gmail.com>. Thank to
all contributors for their work. List of contributors is in AUTHORS.txt
