..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

==================
Federated Barbican
==================

https://blueprints.launchpad.net/barbican/+spec/federated-barbican

Currently, it is impossible to federate secrets between clouds.
This blueprint proposes a way to allow Barbican to federate secrets
between clouds.

Problem Description
===================
(Use Cases should be listed here as potential problems that can be solved with
Federated Barbican. They need to be clearly explained.)

There are a few problems that Federated Barbican can solve.

* A client wants to bring their own keys. A client who wants to
  utilize an HSM or KMIP device with Barbican yet does not want to remove
  the device off-prem or buy another device in the cloud provider's data
  center. Federated Barbican would allow a client to bring their own keys and
  use the encryption services offered by the cloud provider.

* Hybrid Cloud Federation. A client with a hybrid cloud setup can decide
  to use the Barbican services on a different cloud than the one on
  which Barbican resides. This makes it possible for the client to use swift
  encryption, glance encryption; block encryption, certificate management
  services on a cloud which might not have a Barbican.


Proposed Change
===============

There are 2 mechanisms that need to be implemented in order for Federated
Barbican to be successful.

1. Trust. An establishment of trust must be made between the two clouds.
   A possible solution is Keystone to Keystone Federation.

2. Federation of secrets. There needs to be a way for a service to retrieve
   or create a key in a Barbican that resides in another cloud.

*PENDING*

We need to choose the best path from the ideas we have come up with and
describe it as detailed as possible as a solution to the problems above.


Alternatives
(These are the solutions we have come up with so far.)
============

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

  * Elvin's proposal. Explanation needs to go here.
