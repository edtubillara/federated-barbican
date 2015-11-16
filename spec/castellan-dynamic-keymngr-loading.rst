=====================================================================
Dynamic Loading of Key Managers and Connection Paramters in Castellan
=====================================================================

Problem Description
===================
* In the current implementation of Castellan, a user will set a single
  keymanager interface for their openstack implementation. This keymanager
  interface will send accept and forward requests to create,store,get,
  and delete keys. One of the keymanager interfaces  is for barbican.
  This keymanager sends key requests to a single barbican endpoint.

* We want to expand Castellan and other Openstack services to support
  the following use cases:
    Have the customer use their own keys from a key manager server
    that is on their ownpremises.
    Give the customer the option to work with their cloud service provider to
    set a different default key manager endpoint.
    Give the customer the ability to set different key manager endpoints per
    "resource" (For example set a kmip endpoint on some volumes and use
    a public barbican for other volumes).


Proposed Change:
================
* Create a new key manager class in castellan called dynamickeymngr.py. This
  contains a factory method to initialize an auth plugin and a key manager
  interface implentation. For example, a kmip keymanager interface can
  be dynamically loaded with a kmip auth plugin. Then for another request,
  a barbican key manager interface with keystone to keystone federation can
  be created. A connection info optional parameter will be added to the
  standard calls to castellan key manager interfaces.

  .. code-block:: python
    create_key(self, context, algorithm, length, conn_info, expiration=None, name=None)
    create_key_pair(self, context, algorithm, length, conn_info, expiration=None, name=None)
    store(self, context, managed_object, conn_info, expiration=None)
    get(self, context, managed_object_id, conn_info)
    delete(self, context, managed_object_id, conn_info)

* Auth plugin- Developers will have to create an auth plugin so that a
  key manager interface can utilize different authentication schemes
  (keystone to keystone federation/single keystone implementation).

* Create new key manager interfaces that can utilize different authentication
  plugins. For example, a barbican key manager interface could either
  use a single keystone implentation, keystone to keystone federation,
  or other authentication schemes.

* Create a scheme for cascading JSON user policies for mapping
  tenant/project id to key manager service.
  A default key manager service will be designated for all users.
  A key manager service will then be designated per user.
  A user will be able to set key manager endpoints per resource (cinder volume,
  swift containers).
  A file called /etc/castellan/user-policy.json file will define the default
  endpoint for a key manager and mappings for tenant/project id to key manager
  interface. For example:
  .. code-block:: JSON
    {
      "default":{
              "auth-plugin-class": "Keystone",
              "keymanager": "BarbicanKeyManager",
              "barbican_host": "barbican host info"
      },
      "tenant-name1 or tenant-id1":{
            "auth-plugin-class": "KeystoneToKeystoneFederation",
            "keymanager": "BarbicanKeyManager",
            "keystone-identity-provider": "{keystone-identity-provider-info}",
            "keystone-service-provider": "{keystone-service-provider-info}",
            "barbican_host": "{barbican-host-info}"
      },
      "tenant-name2 or tenant-id2":{
            "auth-plugin-class": "kmip auth",
            "keymanager": "KMIPKeyManager",
            "kmip-authorization-info": "{kmip-auth-stuff-here}",
            "kmip-host": "{barbican-host-info2}"
      },
    }


 A user will can set key_info for resources on cinder volumes and swift
containers and this will override any setting set in the user-policy.json.
.. code-block:: JSON
    {
        "key_info":{
          "auth-plugin-class": "KeystoneToKeystoneFederation",
          "keymanager": "BarbicanKeyManager",
          "keystone-identity-provider": "{keystone-identity-provider-info}",
          "keystone-service-provider": "{keystone-service-provider-info}",
          "barbican_host": "{barbican-host-info}"
          "key_uuid": "uuid-123-324234blabla"
        }
    }


