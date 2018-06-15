# Wednesday, June 13th - Kubernetes Security: Best Practices

Will talk about: 
  1. Security 101
  2. Runtime security
  3. Host security
  4. Network security

Example application: a guestbook application
  - Has a frontend (serves web traffic)
  - Message service: provides gRPC messaging
  - Redis backend, and NGWord package (checks for bad words)
  - Frontend talks to message service which talks to Redis backend

How would you attack such an application?
  1. Attack the Kubernetes API server?
    - Get token from frontend pod
    - Use token to attack cluster API server
    - Get secrets, etc. to further your attack
    - You can mitigate this by turning on RBAC (role-based access control)
      - We already do this
    - You can mitigate this further by firewalling the API server to certain IP addresses
      - We already do this with AWS security groups
    - Mitigate still further with network policies
      - A network policy restricts access between pods
      - You can set a network policy using your CNI e.g. Weave, Calico, Flannel, etc.
      - You can also create NetworkPolicy objects with the k8s API

  2. Get access to the host itself?
    - Break out of container using container/kernel exploits
    - Attack the kubelet, and other containers running on the same host
    - You can mitigate this by running your pods as non-root:
      - add a `runAsUser` key in the `securityContext` key in the `spec` element of the Pod template definition.
    - You can mitigate this by preventing your applications from writing to your filesystem:
      - add a `readOnlyFileSystem` key in the `securityContext` key in the `spec` element of the Pod template definition.
    - You can disallow privilege escalation
      - add a `allowPrivilegeEscalation` key in the `securityContext` key in the `spec` element of the Pod template definition.
    - You can create sandboxed pods:
      - Pods are sandboxed from other pods on the same host e.g. kata containers, gVisor
      - See below for more ways

  3. Listening to traffic (attacker tries to sniff traffic, etc.)
     - Use Istio, etc. to have a mesh network.1


Segue: gVisor
  - A sandbox that runs as a userspace kernel
  - Intercepts and implements syscalls in userspace
  - Sandbox has low capabilities and runs with restricted seccomp filters.
  - An attacker would have to find a flaw in your app and in gVisor to break out into host

Other sandboxing solutions:
  - seccomp
  - apparmor
  - selinux

Recommendation: use the default for Docker

```
annotations:
  seccomp.security.alpha.kubernetes.io/pod: runtime/default
```

in your pod spec. If you take one thing away, apply this annotation!

You can also add `seLinux` options into `securityContext` for your pod.

Other recommendations:
  - Restrict kubelet permissions
  - Enable `authorization-mode=RBAC,Node`
  - Enable `admission-control` to `NodeRestriction`

How can you ensure other people don't create insecure pods?
  - Set a PodSecurityPolicy
