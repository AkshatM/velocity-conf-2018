# Wednesday, June 13th - Load Testing for Devops

Definitions: load testing - simulate performance and see how it affects your app

FloodIO: Cloud-based distributed load testing. 
  - No master/slave, no controller.
  - Upload a load testing plan, and distribute it across as many servers as you like.

Cloudcraft: network diagrams

Why load test?
  - Risk management - identifying damages to production
  - Do we have enough infrastructure to meet the expected workload?
  - Do we have enough infrastructure to _scale up_ to more than we expect?
  - Simulate demand, measure performance.

How do you do load testing?
  - You can either do things in the browser, or in the protocol layer.
  - When doing browser tests, you're typically doing functional test automation.
  - When doing protocol layer stuff, you might not be taking into account stuff your browser is doing for you.
    - Question (unanswered): what about mobile clients?
  - Options for perf testing: JMeter, Selenium, Gatling.
  - People use tools like `siege` for URL testing - this is not enough!
    - Load testing this way isn't very useful because it doesn't deal with caching issues at the browser/CDN level.
    - Just load testing the same path obscures this.
  - Only APIs benefit from protocol testing. 
    - Most browsers can take 300 or more requests, interaction paths between which protocol tests don't check for
    - Plus, protocols have evolved: Http/1 -> HTTP/2, Websockets by 2011, etc.
    - Protocol level traps - visit https://challenge.flood.io
    - Insufficient to really capture what users are doing.
    - Protocol level debt: lots of code, isolated assets, fragile frameworks
  - Browser level benefits: less code, resuable assets, repeatable frameworks.

  SO: let's reinvent browser testing, which is so much more useful!

  - Modern tools: SeleniumJS/Puppeteer (Puppeteer is JS library, Chrome only, for headless testing)
  - Always run your load test with 1 user before running them with 1000 users
  - You can upload test plans to FloodIO

Lessons:
  - Simulate user behaviour (don't pretend to be a browser)
  - Use modern tools (use browsers, not recorders)
  - Leverage cloud resources (don't try this on premise)


