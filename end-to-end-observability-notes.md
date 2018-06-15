# Tuesday, June 12th - End-to-End Observability Notes

Setup: a lightbulb server in the middle of the room, and a REST API visible to the world.

Tools: `requests` (Python)

Scope: How do we investigate once we get paged? How do we identify trends?

Motivation:
  - As an industry, we're paging ourselves too much! 
    - We end up with anti-patterns: either too many alerts or not enough, and not enough visibility.
    - Number of sources of pain have increased exponentially in a decade.
  - Figure out what your SLO is (service level objectives): what should a user absolutely be able to do?
    - What's the sort of thing worth getting out of bed for?

Simple example: an app that tells a light to turn red. What's the SLO for this? 
  - It should turn red when asked. 
  - End-to-end test for this: usually involves polling + a timeout

Three pillars of "your thing doesn't work":
  - Availability - can you get to it?
  - Performance - is it fast enough?
  - Correctness - is it doing the right thing?
  - End-to-end checks do all three.

Tests trade off automation and coverage (?)
  - Test after deploy! (Integration tests, canarying, A/B tests, etc.)


Exercise: think of an end-to-end check for a lightbulb service using just a write and read endpoint
  - Notes: read endpoint doesn't expose same attributes as the write endpoint. Read has hue, saturation, brightness; write allows color. 
  - Notes: write endpoint only exposes certain subsent of colors (orange, yellow, lime, green, cyan, blue, purple, pink, white)
  - Idea: identify what hue maps to each colour, and test writes against reads?
  - Actual first pass: https://gist.github.com/christineyen/591cb9053191ab3b7a823a39bfd9fc6b - probably not what's best

Problem with first pass: dealing with concurrency! How did we know, when using a shared API, that it was our request that made it change?

Problem with first pass: there's inherent latency! It might take a few cycles for your request to be processed.

So: let's change the API. Let's add a request ID.

Rough sample of proposed solution:

```
  COLORS = [...]

  def make_write(color,req_id):
      return requests.put('http://35.173.220.113:8080/bulb', headers={'color': color},data={'request_id': id})

  def get_changes():
      return requests.get('http://35.173.220.113:8080/changes').json()

  def e2e_check():

      for color in COLORS:

        req_id = random.randint(1, 100)

        make_write(color,req_id)

        our_change = filter(lambda change: change['id'] == req_id, get_changes())

        assert our_change['color'] == color, "fail: not right color"
        assert our_change['action'] == 'update, "fail: did not register as update"

        ...

```

Why doesn't this work? Too many changes, too many writes, slows your tests down, doesn't check for performance!

Lesson: test isolation can miss connections between components.
Lesson: relying on tests to ensure behaviour can omit these.
Lesson: production is the last, best place to make sure your system works, to watch it, and make sure it keeps working.
Lesson: end to end checks are the bridge between devs (who deal with exceptions, git commits, customer complaints) to ops ()

Understand your terms!

1. Monitoring = known unknowns (things you deliberately watch out for)
2. Observability = unknown unknowns (things you can catch even though you haven't added alerting for it)

Not all problems manifest as exceptions! Not all interesting things are problems!

Things that encompass observability:
  1. Behaviour of your application across time - number of requests per color, and whether they changed the state of the bulb.
  2. When did your app fail? 
  3. How many requests were in the queue?
  
Instrument, instrument, instrument!

Graph: `http://ui.honeycomb.io/join_team/velocity-2018-workshop`

# Scaling

What happens when you have to shard? This is hidden from consumers.

Let's add a `shard_override` header that lets you force a selection of shard.

```
      COLORS = [...]

      def make_write(color,req_id, shard):
          return requests.put('http://35.173.220.113:8080/bulb', headers={'color': color, 'shard_override': shard},
                              data={'request_id': id})

      def get_changes():
          return requests.get('http://35.173.220.113:8080/changes').json()

      def e2e_check():

          for shard in ['odd','even']:

             for color in COLORS:

               req_id = random.randint(1, 100)

               make_write(color,req_id)

               our_change = filter(lambda change: change['id'] == req_id, get_changes())

               assert our_change['color'] == color, "fail: not right color"
               assert our_change['action'] == 'update, "fail: did not register as update"

               ...

```

Instrumentation evolves: add timing data to capture performance. 

# Summary

1. Identify SLOs
2. Write E2E checks.
3. Capture a paper trail to track trends and investigate.


An E2E check should run in production.
An E2E check favours high significance - for example, it should assume failure of each intermediate step and only alert if the whole check consistently fails.
An E2E check should check a path through all layers - but not every path through every layer.

http://get.honeycomb.io/velocity2018/
