# CLI Operations

This page explains comprehensive examples of operations via GoBGP CLI.

## Prerequisites

Assumed that you finished [Getting Started](https://github.com/osrg/gobgp/blob/master/docs/sources/getting-started.md).

## Configuration

This example starts with the same configuration with [Getting Started](https://github.com/osrg/gobgp/blob/master/docs/sources/getting-started.md)

Make sure that all the peers are connected.

```
$ gobgp show neighbors
Peer          AS  Up/Down State       |#Advertised Received Accepted
10.0.255.1 65001 00:00:04 Establ      |          2        2        2
10.0.255.2 65002 00:00:04 Establ      |          2        2        2
```

## Adding or deleting a peer dynamically

You can add a new peer or delete the existing peer without stopping
GoBGP daemon. You can do such by adding a new peer configuration or
deleting the existing configuration of a peer in your configuration
file and sending `HUP` signal to GoBGP daemon.

In this example, 10.0.255.3 peer is added. The configuration file
should be like the following.

```
[Global]
  As = 64512
  RouterId = "192.168.255.1"
[[NeighborList]]
  NeighborAddress = "10.0.255.1"
  PeerAs = 65001
[[NeighborList]]
  NeighborAddress = "10.0.255.2"
  PeerAs = 65002
[[NeighborList]]
  NeighborAddress = "10.0.255.3"
  PeerAs = 65003
```

After you send `HUP` signal (`kill` command), you should see 10.0.255.3 peer.

```
$ gobgp show neighbors
Peer          AS  Up/Down State       |#Advertised Received Accepted
10.0.255.1 65001 00:03:42 Establ      |          3        2        2
10.0.255.2 65002 00:03:42 Establ      |          3        2        2
10.0.255.3 65003 00:01:39 Establ      |          4        1        1
```

## Temporarily disable a configured peer

Sometime you might want to disable the configured peer without
removing the configuration for the peer. Likely, again you enable the
peer later.

```
$ gobgp disable neighbor 10.0.255.1
$ gobgp show neighbors
Peer          AS  Up/Down State       |#Advertised Received Accepted
10.0.255.1 65001    never Idle(Admin) |          0        0        0
10.0.255.2 65002 00:12:32 Establ      |          1        2        2
10.0.255.3 65003 00:10:29 Establ      |          2        1        1
```

The state of 10.0.255.1 is `Idle(Admin)`. Let's enable the peer again.

```
$ gobgp enable neighbor 10.0.255.1
$ gobgp show neighbors
Peer          AS  Up/Down State       |#Advertised Received Accepted
10.0.255.1 65001    never Idle        |          0        0        0
10.0.255.2 65002 00:13:33 Establ      |          1        2        2
10.0.255.3 65003 00:11:30 Establ      |          2        1        1
```

Eventually, the state should be `Established` again.

```
$ gobgp show neighbors
Peer          AS  Up/Down State       |#Advertised Received Accepted
10.0.255.1 65001 00:00:02 Establ      |          3        2        2
10.0.255.2 65002 00:14:59 Establ      |          3        2        2
10.0.255.3 65003 00:12:56 Establ      |          4        1        1
```

## Reset, Reset, and Reset

Various reset operations are supported.

```
$ gobgp reset neighbor 10.0.255.1
$ gobgp softreset neighbor 10.0.255.1
$ gobgp softresetin neighbor 10.0.255.1
$ gobgp softresetout neighbor 10.0.255.1
```