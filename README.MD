# Network Modeling for applications

Some inspiration:
https://docs.oasis-open.org/cti/stix/v2.1/os/stix-v2.1-os.html#_rgnc3w40xy
https://cybox.mitre.org/language/version2.1/xsddocs/objects/Network_Connection_Object.html

>***As a platform engineer***, I would like to know how systems connect

>***So that I*** can build robust, secure networks to cater for these connections

>***And*** analyse traffic flows and identify gaps

In scope:

- layer 3 and 4 network connections - connections between namespaces and pods within a cluster or between cluster is a nice-to-have and could be translated to NetPols?
- Outbound connections, as apps won't know what will be connectiing to them

Aim is to have all intended network flows documented in code beside ANZx application and platform code

This would enable:

1. Network Connectivity for components to be well documented
1. Changes to network connectivity be surfaced before a release
1. Modeling and analysis of flows between components in the design phase
1. Identification of network security gaps
1. Identification of network bottlenecks and SPOFs
1. Identification of external dependencies
1. Automated comparisons between expected traffic flows and obsserved traffic flows (makes unknowns into knowns)

An application should know what external dependencies are called. These can be represented as:

```yaml
metadata:
  name: sample-app
  labels:
    confidentiality: internal
    integrity: trusted
    trustlevel: high
dependencies:
  - api-gateway: 
      domain: test.example.com
      port: 443
      trustlevel: high
      protocols:
        - HTTP
        - GRPC
      tls: 1.3
      auth: mutual
  - file-service: 
      domain: another.example.com
      port: 22
      ipv4: 
      trustlevel: high
      protocols: 
        - SSH
      auth: mutual
  - external-api: 
      domain: another.example.com
      port: 443
      trustlevel: low
      protocols:
        - GRPC
      tls: 1.2
      auth: server-only

```

The only things a developer should need to know to connect to an external systems are:

- The domain name
- The ports or protocols to connect to
- The authentication method ( if needed)

They shouldn't need to know about 