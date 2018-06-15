# Thursday, June 14th - There Can Only Be One Environment: Design Patterns for Killing your Staginv Environment

Argument: have only a single production environment

Nodrstrom tests in prod:
  - They've been doing it for two and a half years now
  - Are pretty big
Talk is about patterns they've seen emerge in this environment.

Why kill a staging environment?
  - Cost
  - Instability
  - Inconsistent data: killing monoliths is awesome (including ones in dev/test)!
  - The fallacy of tests:
    - Test users just don't scale as much as production users do
  - Anything that isn't prod isn't valuable
  - Use your power for good, and not evil
    - This means time spent killing off environments makes your production environment better
    - You end up only caring about the important environments

Use cases and patterns:
  1. Testing new features in production
    - Issues with multiple staging environments: coordination needed across teams to release all features
    - To do this in production:
      - Use request-based feature flags:
        - Allows teams to publish features independently
        - Avoids gummming up pipelines
        - Avoids testing integrations in each environment
        - Prepares for exposing feature to limited traffic
        - When Nordstrom does it, they use their Experiments platfor that hooks A/B testing in 

  2. Releasing new services in production
    - To do this, they use:
      - Shadow testing: a request hits a routing service, which sends all traffic to both a live and a test system, but only returns results from live system
      - Captures log results across both test and live system
      - Allows you to get real production traffic in your test system
      - Allows you to analyse baseline differences between systems
      - Encourages teams to get appropriate telemetry

  3. Continuous delivery in production
    - Most CI/CD systems go from laptop to staging environment to production
    - To do this in production:
      - Local end-to-end tests: lets you mock and instantiate services locally
      - Lets your tests pass consistently regardless of dependency status
      - This tests all of the functionality of your service.
        - Doesn't test for scale
        - Doesn't test for latency
        - Scale and latency are tested in prod
      - Use canary deployments
      - Create enough alerts to know when your service fails in prod
      - Have automated rollback procedures: offsite this to teams
      - Protect your service against downstream outages

  4. Performance and load testing
    - Nordstrom gets a lot of traffic during the holidays: almost 10x regular traffic
    - How to do this:
      - Synthetic transactions: hit your system with load in a way you can identify where that load is not real
        - Make sure your load is coming from a user that's not real, for example. 
        - Allows you to test on actual production configuration
        - You can screen load from legacy or financially impacting systems
        - You can use this as a monitoring tool

  5. Going live on a specific date
    - Nordstorm has an anniversary sale which starts at midnight on a very particular day
    - Is usually a huge change to the site's UI/UX
    - They used to handle this by setting up a staging environment and set up a runbook on how to do it
    - Often this wouldn't work in prod
    - To do this in production, they useD:
      - Time machine: All requests have a header that tells the system to run as if it is a specific date
      - Avoids garish, risky runbooks
      - Allows them to test using real production data
      - They use it to launch categories, A/B tests, etc. 
      - Lots of content publishers do this too!

Summary:
  1. Spend time on prod, not your staging. That's where you'll do the most good.
  2. Create new patterns - the list above is not exhaustive
  3. Spread knowledge: tell people to do it in prod, not staging

Q/A:

 1. How do you guys test new infrastructure?

 Usually, they set up new infrastructure by duplicating things in prod, rather than setting up a new environment for it.

 2. What happens when you get mandates from vendors?

 3. Did you write your own traffic-splitter?

 They found one online, but a better dedicated service might be better. Also you want to get this bit right. 

  4. What's your release cadence been like after this?

  It's actually improved.

  5. Do you need another environment for penetration testing?

  No, they do it in prod.

  6. Did you have any metrics for this?

  No, they didn't while doing this.

  7. How do you do database migrations?

  Make everything backwards-compatible: make sure all columns stay until you're sure all code referencing that column is gone.
  You have to be good about cleaning up unused columns.
