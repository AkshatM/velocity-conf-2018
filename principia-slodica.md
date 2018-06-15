# Wednesday, June 13th - Principia SLOdica: A Treatise on the Metrology of Service Level Objectives

Summary: how to monitor with SLOs the right way!

The wrong way to alert: cause-based alerts.
  - At Google e.g. they paged on slow replication, or reads/writes failing to write.
    - All very useful, but caused really heavy operational toll as people kept getting paged.
  - Imperfect alert is an alert that targets a component, because of cascading failure
  - Cause-based alerts are useful but cause ops on-call rotations to suck.
  - Prefer to get paged on symptoms of bad user behaviour, rather than on components or causes! 

Operations tend to suck when cost of maintenance increases as system size increases. 
  - The cost of maintenance must scale sublinearly with the growth of the service to be scalable. 
  - Budget time to delete older alerts as your system changes. Focus on alerting on a small set of expectations, rather 
    than on components, of user behaviour.

What is a symptom?
  - Answered below
 
How much failure is acceptable?
  - Concept: engineering tolerance. The amount of failure a system can withstand.

Tool we can use to set our expectations:
  - SLOs: service level objectives (a goal)
  - SLOs apply in aggregate across all users.

How do we figure out an SLO?
  1. Look at current performance, and pick that.
  2. Look at operational tolerance (how hard you're willing to work) involved
  3. Look at what users expect

If service consistently beats an SLO, people come to rely on that SLO being higher than it usually is.

A symptom is anything that can be be measured by the SLO.

A symptom-based alert is when the SLA is in danger of being not met. 

Defining SLOs in terms of request success rate makes it easier to measure. Note: not the only way to define your SLO.

You can't really measure SLO with an external test without losing visibility - so you must instrument your application, or probe internally.

Best to define SLOs at the load balancer level rather than application backend - if backend is gone, you lose metrics instantly! SLO metrics should be resilient to failure!

Paging on momentary dips in SLOs is not a good practice - page on sustained dips over a loner period of time.

Burning your SLO violation budget quickly (i.e. one week's worth of failure in 24 hours) is a page-worthy event.

Components of good symptom-based alerting: 
  1. Have an error budget
  2. Measure your SLO error budget
  3. Measure your error budget burn rate vs. threshold
  4. Page when you exceed your threshold.

If we're only paging when symptoms occur, how do we find out the cause?
  - Have good diagnostic tools. Collect traces, metrics, and logs!
  - Debugging tools will replace cause-based alerts
  - example: a distributed gdb

Don't be afraid to migrate away from cause-based alerts
  - But try to downgrade them from pages to tickets

Summary:

1. Symptom-based alerts are good for your health.
2. SLO is defined by you, customers and system
3. SLO implies an error budget and informs your engineering tolerance.
4. Page only on SLO risk, because that's what matters.

You might think you're too small for this sort of alerting hygiene, and that's fine, but when you scale, consider this approach.
