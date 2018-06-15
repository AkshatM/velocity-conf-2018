# Wednesday, June 13th - Moving Monoliths to K8s

So why monolithic applications?
 - They're hard (no matter where they run)
 - Snowflakes, networking, are all complex
 - Not really a good story for migration

What is a monolithic application?
 - Tightly coupled dependencies are all that make a monolithic application.
 - Either UI is tied to data access, or data access is tied to data store, or all 3!

Enter Kubernetes:
  - Is declarative!
  - Benefits are well-known

Let's talk about state!
  - Ephemeral is transient; persistent will last outside the length of a pod.
  - Kubernetes will manage state volumes for you. 
  - State management is 
    - cloud provider-dependent, 
    - complex and has room for error,
  - You can backup state with Ark
    - But restoring backup can be hard! :D
    - OS tools are getting better for this
  - You have to deal with missing or corrupt state! But OS tools are getting better for this.
  - Managing your state (application data, etc.) is something you should monitor!

Running large stateful Kubernetes applications might make sense:
  - Java-specific: great support for JVM inside a Docker container now

Where do you draw the line in your monolith?
  - The network is the new application interface!
  - Any time you start to transfer large complete data structures in your app, that's an opportunity to break it up into smaller pieces!

- Encapsulate all resources for your app.
  - Define storage, ports, etc. in YAML
- Debugging and developing your applications takes work.

What about the migration?
  - Issues: you can have fragmented state!
  - You can have downtown, data loss, unforeseen problems, etc.

Kubernetes doesn't provide data management:
  - Stuff like replication
  - Backup restore
  - PITR, etc.

Audit your applications:
  - Understand your application and what it needs to run
  - Where is your list of dependencies?
  - Where do your configurations live?
  - Does your application care about what OS it's running on?

MNonolithic applications are significantly harder in k8s, but not impossible.

Logging, monitoring, alerting - lots of good tools for k8s


