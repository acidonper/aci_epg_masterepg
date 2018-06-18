# aci_epg_masterepg
Manage End Point Groups (EPG) on Cisco ACI fabrics with Master EPG Contract Master asignation included

Notes:
- The tenant and app_profile used must exist before using this module in your playbook.
  The aci_tenant and aci_ap modules can be used for this.
- More information about the internal APIC class fv:AEPg from the APIC Management Information Model reference,https://developer.cisco.com/docs/apic-mim-ref/.

```
Options:
  tenant:
    description:
    - Name of an existing tenant.
    aliases: [ tenant_name ]
  ap:
    description:
    - Name of an existing application network profile, that will contain the EPGs.
    required: yes
    aliases: [ app_profile, app_profile_name ]
  epg:
    description:
    - Name of the end point group.
    required: yes
    aliases: [ epg_name, name ]
  masterepg:
    description:
    - Name of the Master end point group.
    required: no
    aliases: [ masterepg_name, mastername ]
  bd:
    description:
    - Name of the bridge domain being associated with the EPG.
    required: yes
    aliases: [ bd_name, bridge_domain ]
  priority:
    description:
    - QoS class.
    choices: [ level1, level2, level3, unspecified ]
    default: unspecified
  intra_epg_isolation:
    description:
    - Intra EPG Isolation.
    choices: [ enforced, unenforced ]
    default: unenforced
  description:
    description:
    - Description for the EPG.
    aliases: [ descr ]
  fwd_control:
    description:
    - The forwarding control used by the EPG.
    - The APIC defaults new EPGs to C(none).
    choices: [ none, proxy-arp ]
    default: none
  preferred_group:
    description:
    - Whether ot not the EPG is part of the Preferred Group and can communicate without contracts.
    - This is very convenient for migration scenarios, or when ACI is used for network automation but not for policy.
    type: bool
    default: 'no'
    version_added: '2.5'
  state:
    description:
    - Use C(present) or C(absent) for adding or removing.
    - Use C(query) for listing an object or multiple objects.
    choices: [ absent, present, query ]
    default: present
extends_documentation_fragment: aci
```

## Examples

```
- name: Add a new EPG
  aci_epg_masterepg:
    host: apic
    username: admin
    password: SomeSecretPassword
    tenant: production
    ap: intranet
    epg: web_epg
    description: Web Intranet EPG
    bd: prod_bd
    masterepg: uni/tn-production/ap-intranet/epg-web_epg
    preferred_group: yes

- aci_epg_masterepg:
    host: apic
    username: admin
    password: SomeSecretPassword
    tenant: production
    ap: ticketing
    epg: "{{ item.epg }}"
    description: Ticketing EPG
    bd: "{{ item.bd }}"
    priority: unspecified
    intra_epg_isolation: unenforced
    state: present
    masterepg: "uni/tn-production/ap-ticketing/epg-{{ item.epg }}"
  with_items:
    - epg: web
      bd: web_bd
    - epg: database
      bd: database_bd

- name: Remove an EPG
  aci_epg_masterepg:
    host: apic
    username: admin
    password: SomeSecretPassword
    validate_certs: no
    tenant: production
    app_profile: intranet
    epg: web_epg
    state: absent

- name: Query an EPG
  aci_epg_masterepg:
    host: apic
    username: admin
    password: SomeSecretPassword
    tenant: production
    ap: ticketing
    epg: web_epg
    state: query

- name: Query all EPGs
  aci_epg_masterepg:
    host: apic
    username: admin
    password: SomeSecretPassword
    state: query

- name: Query all EPGs with a Specific Name
  aci_epg_masterepg:
    host: apic
    username: admin
    password: SomeSecretPassword
    validate_certs: no
    epg: web_epg
    state: query

- name: Query all EPGs of an App Profile
  aci_epg_masterepg:
    host: apic
    username: admin
    password: SomeSecretPassword
    validate_certs: no
    ap: ticketing
    state: query
```
