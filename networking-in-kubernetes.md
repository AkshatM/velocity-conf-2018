# Wednesday, June 13th - Networking in Kubernetes

Things you sometimes see:
- Why can pod A not talk to pod B?
- Why can pod A only sometimes not talk to pod B?

Complicating factors:
  - When networks go bad, it's not always easy to diagnose.
  - When networks go bad, it's not always in an extreme state.
  - There are over 19 CNI plugins in K8s, each of which can have different settings! 
  - Most mature solutions are only a few years old. 
  - Most Kubernetes operators aren't network engineers.

Main focus for this talk: Flannel + Calico on bare metal (covers a Layer 2 VXLAN and Layer 3 IP-in-IP overlay)

Background (story time):
  - Speaker ran K8s with Weave on CoreOS with upgraded kernel to 4.10 in 2017
  - Saw Kubernetes response times were all over the place compared to non-Kubernetes times
  - Saw weird things in logs (MAC addresses for devices that weren't there)
  - Pods failing because of `connect: no buffer space available`
  - Saw a bunch of nodes listed on Weavescope that had no name.
  - Ended up moving workloads off of Kubernetes, replaced the network layer with Flannel, moved it backed.
  - But ended up running into further issues after a sucessful while.
  - Tracked it down to SYNs from ingress controllers to our pods.
  - Search pointed to `tcp_tw_recycle` kernel setting - was enabled on all machines.
    - Standard advice: use `tcp_tw_reuse`, not `recycle`
    - Was completely broken at Linux kernel 4.10 (now fixed in 4.12)
  - Lesson: Any issues in the underlying TCP stack could have caused erratic behaviour, since Weave uses a full-mesh of TCP connections
 
Brief docker networking segue:
  - Default mode is bridge mode
  - Docker will create a bridge device (assigns a block of IPs to devicea)
  - Creates a new network interface for each container, and creates a VETH pair. `eth0` interface is assigned an IP from bridge device.
  - For incoming connections, Docker can do port forwarding.

Kubernetes networking model:
  - Has pods, can contain containers all in same network namespace. Containers within pods must use unique ports and reach each otgher over localhost.
  - Networking model is available on K8s website.
  - IF YOU NEED TO connect to a SERVICE, JUST USE ITS IP AND PORT LIKE A VM. (How k8s works by default)
    - Can be challenging to set up (e.g. size of available IP range, ability to assign multiple IPs, network hardware configuration can be bottlenecks)
    - A little bit about K8s services:
      - It's really easy for two pods to talk, and services give a DNS name inside the cluster DNS (resolves to a cluster IP, a fake IP)
      - This gets mapped through iptables to different pods (this is done by kube-proxy). 
      - Load balancing is distributed using iptables randomly
      - Services are just a DNS name and a set of iptables rules. So debug these!)
  - CNIs:
    - Have an IPAM plugin (IP address allocation)
    - Have a NetPlugin (take care of networking layer)
  - Prefer to use underlay networks since they have less overheard in terms of processing time and packet size (though requirs underlying network supports your functionality)
    - MACvlan (gives unique MAC address to a virtual network adapter in each container). Limited by some networks.
    - IPvlan (similar to MACvlan, unique IP address per container sharing a host MAC address)
    - K8s services can't use iptables if you're using MACvlan/IPvlan.
  - Encapsulation in networking (assumes IPv4 over Ethernet):
    - Say you want to send a message. Stick a UDP header on it. Stick a TCP header. Stick an Ethernet header.
    - This is how sending a message works on the internet.
  - Flannel: 
    - VXLAN is preferred overlay technology. Uses a `host-gw` mode where it only adds routes to hosts and doesn't provide a true overlay.
    - VXLAN is an improvement over VLAN (supports 16 million unique networks)
    - VXLAN mode encapsulates Ethernet packets in UDP packets sent to port 8472 using a small 8-byte header.
    - VXLAN rthen address that UDP packet to ... ?
      - Segue: Netlink is a socket family in Linux kernel. Flannel uses this.
      - Say trying to reach IP 172.20.15.1
      - Can inspect routes with `ip route`
      - Lost thread here - something about cache misses in the ARP table.
    - VXLAN debugging:
      - Make sure flanneld is running on all nodes
      - Get pod A and B's IP address and node it is on from `kubectl`.
      - Check IP address has valid ARP table entry using `ip neigh`
      - Watch traffic leave source and destination host (use `tshark`)
    - Debugging intermittent network connectivitiy:
      - Check syslog! Sometimes large number of pods can overflow the ARP table.
      - Remember VXLAN creates one large L2 network
      - Try increasing ARP table limits (Linux caps out at 1000 entries)
      - Make sure max number of pods is well below `gc_thresh2` limit on your system
      - Consider if you have enough DNS pods! K8s can put a lot of load on the internal DNS pods, doing several lookups for each connection in the cluster.
    - Calico:
      - Layer 3 overlay (IP-in-IP):
        - Pros: simple
        - Cons: unicast only.
      - Only change to inner IP packet is to decrement TTL by one.
      - Works by wrapping an IP packet in an IP packet.
      - Calico creates an IP-in-IP tunnel in root network namespace.
      - When pod is created, kernel insert a tunl0 interface (is not used by Calico) and Calico will insert a VETH interface called eth0, assigning it an IP from one of the /26 networks that node has reserved.
      - Root namespace will have a route to the VETH interface.
    - Networking across nodeS:
      - Needs a route in local route table, and routes are advertised via BGP with every nord running a BGP daemon called `bird`.
    - Calico debugging across nodes:
      - Check IP settings are right (IPV4POOL_CIDR variable should be right)
      - Check calico node pods are running everywhere
      - Calico-kube-control pods should also work
      - Kubelet and calico-node should be working.
      - Make sure calico-node can talk to etcd cluster for Calico
      - Check routes in both directions (get `ip route` on pod A) - check it has a route to pod B (and vice versa))
      - If routes are missing, make sure `bird` is running. Run `tcpdump -i any port 179` to see if BGP traffic is flowing between the two nodes.
      - `Run ip addr` in the pod namespace, the eth0 adaptor will have have name like `eth0@if1714` - look for a matching interface in root network namespace.
      - Watch pod traffic.


Summaryt:
  - Learn how networks work.
  - Learn from other's mistakes
  - Be prepared for network being down.

Slides: http://jeffpoole.net/talks/2018/k8s-networking.pdf
