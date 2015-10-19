========================================
Federated Castellan Barbican Key Manager
========================================

Overview
========
* The goal of the Federated Castellan Barbican Key Manager is to change existing code
  as least as possible. The barbican client and barbican key manager would be changed to be flexible
  enough to be able to connect to any barbican instance if host and 
  authentication parameters are given. A new federated key manager would then create
  instances of the flexible barbican key manager to connect to a specific barbican host.
  There are two high level architecture designs.

Establishing trust
==================
* Keystone to Keystone federation will be used. A new module to automate this will be created.

Federation of secrets
=====================
* Castellan will be in charge of forwarding the request to the right barbican. 

Federated Aware Services vs Federated Oblivious Services
========================================================
* Federated Aware Services (which do not currently exist) are OpenStack services that have
  control over where to store a barbican secret. The federated key manager's APIs
  would provide parameters that include a link to the specific barbian 
  host and a scoped token.

* Federated Oblivious Services are OpenStack services that do not know that
  federated barbican is being used under the hood. The APIs that they call
  match the current libarary APIs for the Castellan Barbican Key Manager (So they don't
  have to call the APIs differently). A mapping between project-id to a barbican host is
  required to automate the request flow and for keystone to keystone federation.

Architecture 1: Federated Barbican Aware Services 
=================================================
The APIs for the current Barbican KeyManager will be expanded to include target host (keyhost_url)
and a scoped token (host_auth).

create_key(self, context, algorithm, length, expiration=None, name=None, keyhost_url, host_auth)
create_key_pair(self, context, algorithm, length, expiration=None, name=None,  keyhost_url, host_auth):
store(self, context, managed_object, expiration=None, keyhost_url, host_auth):
get(self, context, managed_object_id, keyhost_url, host_auth)
delete(self, context, managed_object_id, keyhost_url, host_auth)

This implies that the service that uses the federated castellan library will need to keep
track of which barbican host has the key and provide the scoped token to authenticate to it.

Once provided with the federated parameters, a barbican client instance will be created
to connect to the given barbican.


Architecture 2: Federated Barbican Oblivious Services
=====================================================
The APIs for the current Barbican KeyManager will not be changed.


