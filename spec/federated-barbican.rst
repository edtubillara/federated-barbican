..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

==================
Federated Barbican
==================

https://blueprints.launchpad.net/barbican/+spec/federated-barbican

Currently, it is impossible to federate secrets between clouds.
This blueprint proposes a solution to allow Barbican to connect to multiple
clouds to federates its services.

Problem Description
===================
(Use Cases should be listed here as potential problems that can be solved with
Federated Barbican. They need to be clearly explained.) 


There are a few problems that Federated Barbican can solve.

* A client wants to bring their own keys to a private cloud. A client who
  wants to utilize an HSM or KMIP device with Barbican yet does not want to
  remove the device off-prem or buy another device in the cloud provider's
  data center. Federated Barbican would allow a client to bring their own keys
  and use the encryption services offered by the cloud provider.

* A client want to bring their own keys to a public cloud. This is a hybrid
  cloud scenario. It is similar to the scenario above with the exception that
  the customer will be connecting their on-prem device to a public cloud
  instead of a dedicated one. The public cloud infrastructure is different
  than a private one and will most likely offer additional challenges not
  apparent for a customer who wants to bring their own keys to a fully
  dedicated cloud.

* Limited HSM or KMIP availability. It is common for the HSM's or KMIP devices
  to be located in very few data centers. This scenario necessitates cloud
  service providers the ability to leverage different HSM's or KMIP devices
  in another public cloud, and hence a separate Barbican. Federated Barbican
  can provide data centers around the world a chance to use an HSM or KMIP
  device located on another Barbican in another cloud.

* Scalability. Federated Barbican allows for horizontal scaling across
  different clouds. If another Barbican is needed but the resources on a
  cloud are full, federated Barbican allows the cloud provider to leverage a
  Barbican located in a different cloud to meet the resource demands of
  any cloud.


Proposed Change
===============

There are 2 mechanisms that need to be implemented in order for Federated
Barbican to be successful.

1. Trust. An establishment of trust must be made between the two clouds.
   A possible solution is Keystone to Keystone Federation.

2. Federation of secrets. There needs to be a way for another service to
   leverage Barbican's services in another cloud.

*PENDING*

We need to choose the best path from the ideas we have come up with and
describe it as detailed as possible as a solution to the problems above.


Alternatives
============
(These are the solutions we have come up with so far.)

* Proxy

  * A proxy can sit in front of Barbican. The Proxy can handle the Keystone
    to Keystone authentication as well as the federation of secrets. The
    added benefit is that all changes made will be made client side. An
    extra flag can be added to tell the proxy the secret will be federated.
    Example, "barbican secret store -p 'payload' --federated". The location
    of the user's unique federated barbican can be stored in a database for
    the proxy to read from, stored in a configuration file, or it can be
    given by the user in the client request.


* Change Barbican Client

  * This is a solution similar to a project done by Boston University,
    https://github.com/CCI-MOC/moc-public/wiki/Mix-and-Match-Federation
    We could rewrite the client to handle both the Keystone to Keystone
    authentication as well as the federation of secrets. The Keystone client
    makes it possible to automate the authentication between clouds given
    that the location of the cloud you are requesting to authenticate to is
    provided. It could then get the location of Barbican from the external
    cloud's service list and use that Barbican instead.

* Use Castellen

  * Create a new federated barbican key manager in Castellan. We would
    change the barbican client library to be flexible and be able to connect to
    any barbican instance given federated parameters. The goal of the federated key manager would
    be to expose APIs that could control which host a barbican request would flow to. 

Elvin's Notes:
==============
BYOK does not entirely get solved through BYOD(Federated Barbican), the other openstack services (cinder, swift)
have to implement importing customer keys for encryption.

