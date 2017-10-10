OpenShift Hosted
================

OpenShift Hosted Resources

* OpenShift Router
* OpenShift Registry

Requirements
------------

This role requires a running OpenShift cluster.

Role Variables
--------------

From this role:

| Name                                  | Default value                            | Description                                                                                                              |
|---------------------------------------|------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|
| openshift_hosted_router_certificate   | None                                     | Dictionary containing "certfile", "keyfile" and "cafile" keys with values containing paths to local certificate files.   |
| openshift_hosted_router_registryurl   | 'openshift3/ose-${component}:${version}' | The image to base the OpenShift router on.                                                                               |
| openshift_hosted_router_replicas      | Number of nodes matching selector        | The number of replicas to configure.                                                                                     |
| openshift_hosted_router_selector      | region=infra                             | Node selector used when creating router. The OpenShift router will only be deployed to nodes matching this selector.     |
| openshift_hosted_router_name          | router                                   | The name of the router to be created.                                                                                    |
| openshift_hosted_registry_registryurl | 'openshift3/ose-${component}:${version}' | The image to base the OpenShift registry on.                                                                             |
| openshift_hosted_registry_replicas    | Number of nodes matching selector        | The number of replicas to configure.                                                                                     |
| openshift_hosted_registry_selector    | region=infra                             | Node selector used when creating registry. The OpenShift registry will only be deployed to nodes matching this selector. |
| openshift_hosted_registry_cert_expire_days | `730` (2 years)                     | Validity of the certificates in days. Works only with OpenShift version 1.5 (3.5) and later.                             |

If you specify `openshift_hosted_registry_kind=glusterfs`, the following
variables also control configuration behavior:

| Name                                         | Default value | Description                                                                  |
|----------------------------------------------|---------------|------------------------------------------------------------------------------|
| openshift_hosted_registry_storage_glusterfs_swap     | False         | Whether to swap an existing registry's storage volume for a GlusterFS volume |
| openshift_hosted_registry_storage_glusterfs_swapcopy | True          | If swapping, also copy the current contents of the registry volume           |
| openshift_hosted_registry_storage_glusterfs_ips      | []            | If we use external gluster for Registry backend we need to provide list of IP's for EP creation|
| openshift_hosted_registry_storage_glusterfs_path     | []            | If we use external gluster we need to provide volume path from gluster cluster for mounting|
| openshift_hosted_registry_storage_glusterfs_endpoints| glusterfs-registry-endpoints             | Default value how SVC and EP for gluster will be named |

**Note:** if you have host group `glusterfs` or `glusterfs_registry` set in your invetory, variable `openshift_hosted_registry_storage_glusterfs_ips`, CAN'T be used because this is 2 different use-cases - CNS/CRS vs External Native Gluster. 
_

Dependencies
------------

* openshift_hosted_facts
* openshift_storage_glusterfs
* openshift_persistent_volumes

Example Playbook
----------------

```
- name: Create hosted resources
  hosts: oo_first_master
  roles:
  - role: openshift_hosted
    openshift_hosted_router_certificate:
      certfile: /path/to/my-router.crt
      keyfile: /path/to/my-router.key
      cafile: /path/to/my-router-ca.crt
    openshift_hosted_router_registryurl: 'registry.access.redhat.com/openshift3/ose-haproxy-router:v3.0.2.0'
    openshift_hosted_router_selector: 'type=infra'
    openshift_hosted_registry_storage_kind=glusterfs
    openshift_hosted_registry_storage_glusterfs_path=external_registry_path
    openshift_hosted_registry_storage_glusterfs_endpoints=external-gluster-ep-name
    openshift_hosted_registry_storage_glusterfs_ips=192.168.20.239,192.168.20.96,192.168.20.114

```

License
-------

Apache License, Version 2.0

Author Information
------------------

Red Hat openshift@redhat.com
