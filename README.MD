# Network Modeling for applications

## Aim

Aim is to have all intended network flows documented in code beside both application and platform code.

This would enable:

1. Network connectivity for components to be well documented
1. Changes to network connectivity be surfaced before a release
1. Modeling and analysis of flows between components in the design phase
1. Identification of network security gaps
1. Identification of network bottlenecks and SPOFs
1. Identification of external dependencies
1. Automated comparisons between expected traffic flows and observed traffic flows (makes unknowns into knowns)

Similar schemas for documenting network connections:

- https://docs.oasis-open.org/cti/stix/v2.1/os/stix-v2.1-os.html#_rgnc3w40xy
- https://cybox.mitre.org/language/version2.1/xsddocs/objects/Network_Connection_Object.html

## User Story

>***As a platform engineer***, I would like to know how systems connect
>***so that I*** can:
> - build robust, secure networks to allow these connections
> - analyse suggested traffic flows
> - identify gaps in documentation

## Scope

In scope:

- Layer 3, 4 and 7 info
- Outbound connections, as apps won't know what will be connectiing to them

Out of scope:

- Documenting pod and namespace components of [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

## Data Model

An application should know what external dependencies are called (things like APIs ). These can be represented as:

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
        - GRPC
        - HTTP
        - HTTP/2
      tls: 1.3
      auth: mutual
  - file-service: 
      domain: files.example.com
      port: 22
      ipv4: 
      trustlevel: high
      protocols: 
        - SSH
      auth: mutual
  - external-api: 
      domain: ext.example.com
      port: 443
      trustlevel: low
      protocols:
        - GRPC
        - HTTP
      tls: 1.2
      auth: server-only

```

The only things developers should need to know for external dependencies are:

- the domain name
- ports or protocols to connect to
- Certificate Authorities for internal services
- an authentication method (*if needed*)

Developers shouldn't need to know destination IP addresses or how DNS resolution works. This should all be transparent.

Having connection data allows platform and security engineers to review flows before releases and get forewarning for potential changes to firewall rules and outbound proxy allow-lists.

## Visualization

Once connection data is stored, a centralised service can pull in this data from repos in scope for aggregation. Once aggredated data can be visualised in many ways:

- system to system mapping
- connections encriched with historical metrics from flowlogs and other sources
- additional data about systems such, as data labels, can be used to map flows from different data domains or trust zones

D3.js has many examples of interesting visualizations:

- https://observablehq.com/@d3/force-directed-graph
- https://observablehq.com/@vasturiano/hilbert-map-of-ipv4-address-space
- https://observablehq.com/@xianwu/force-directed-graph-network-graph-with-arrowheads-and-lab


## Pre-flight network testing

DNS resolution of endpoints and connectivity could be tested from within a cluster before actual app deployment. For authed connections testing is probably unlikely but port status (Open/Closed) should be testable for local unproxied endpoints.

Future state and current state can be compared and clashes can 

