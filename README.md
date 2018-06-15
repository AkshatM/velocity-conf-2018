These contain notes of all the talks I attended at Velocity Conference 2018.

Some of the most interesting talks in my opinion:
  1. There Can Only Be One Environment: A fascinating and radical argument in favor of not having a staging environment, and a list of useful patterns to adopt to do common tasks in a staging environment in a production environment instead.
  2. Networking in Kubernetes: A very useful breakdown of how to diagnose and debug an overlay network in Kubernetes, along with some neat war stories. Learned a lot about ARP tables in Linux
  3. Principia SLODica: An argument on how to reduce cognitive overload on on-call engineers by switching to not alerting on what failed, but instead alerting only when SLOs aren't failing and arming engineers with great debugging tools to figure out why. 
  4. Kubernetes Security - Best Practices: A lackluster talk with some surprisingly great material. Very useful in figuring out how to harden your Kubernetes applications, though some recommendations are definitely unorthodox (at one point, the speaker recommended containing your containers in a hypervisor... so why have a container at all? :thinking face:)

Some of the least useful talks in my opinion:
  1. Stateful Distributed Applications That Scale: Rehashes a classic CS paper and ends up being a litany of reasons why distributed systems are hard. Nothing particularly new or novel in the talk, though contains some retrospectively obvious recommendations on building your own.
  2. Moving Monolithic Apps to Kubernetes: Concentrated less on the mechanics and far more on whether Kubernetes is a good pattern for this. Focused very heavily on Java applications, to the expense of other types of monoliths.
  3. Performance Debugging in Production: While on the surface it sounds interesting, the lessons learned here appear to be well-known in the industry (use distributed tracing, instrument your apps and your systems). I learned very little outside the fact there are apparently far more profilers than I thought existed. 

Talks I was on the fence about:
  1. Container Vulnerability Scanning: Explains how to scan containers for vulnerabilities and describes some traps. I initially thought this was a great talk, but I've since discovered Google Cloud will run scans for free so there's not a lot of actionable material here. Useful for anyone looking to understand security here.
  2. Load Testing for DevOps: While it contains a great breakdown on the right way to do load testing (in principle, use headless browsers where possible instead of API and URL tests), it's more of a sales pitch. One interesting finding is that there is no good solution for simulating load from mobile clients yet or from native clients - most load testing solutions are browser-oriented. 

On Tuesday, I attended two workshops:

  1. End-to-End Observability: 
    - This talk aimed to explain the principles behind what constitutes a good end-to-end test, as well as how these tests should be instrumented to provide visibility. While the topic was sound, the execution felt lacking: networking issues prevented a lot of attendees from playing around with the test API they'd set up. The overall lessons were: end-to-end tests should run in production always, should only be oriented around your service level objectives (what you want the user to be able to do), and should capture just whether that action could be done as well as how long it took to do it. Rather than test every possible path through every possible layer, a good end-to-end test should ideally target a path through all layers (a great lesson, though I think combining this with random choices of which path to take is a great way to get probabilistic coverage). I took care to copy all my terminal output to a file - this file is in `sessions/`.

  2. Attack Trees:
    - A beautiful workshop on how to model the most likely ways people can attack your system. It's as much a critique of traditional compliance and security certifications as it is an exploration of threat modelling in practice. The core idea here is: think about the sorts of people who might attack you, think about the sorts of goals they might have (it's not always data! Sometimes they may just want to defame or DDoS you), think about as many ways they could accomplish their goal, and then write it all out in a tree-like diagram and hang it on the wall next to your team so people can use this as a security reference whenever considering new changes. It's really one of the more spectacularly useful talks I've seen - can definitely see it being useful at any company.

# Overall Verdict

Velocity 2018 was awesome!
