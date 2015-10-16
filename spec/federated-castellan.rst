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
  would be called with a link to the host and a scoped token to authenticate to the 
  needed barbican would also be provided.

* Federated Oblivious Services are OpenStack services that do not know that
  federated barbican is being used under the hood. The APIs that they call
  match the current libarary APIs for the Castellan Barbican Key Manager (So they don't
  have to call the APIs differently). A mapping between project-id to a barbican host is
  required to automate the request flow and for keystone to keystone federation.

Architecture
============
