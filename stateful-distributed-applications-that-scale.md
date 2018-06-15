# Thursday, June 14th - Pat Helland and Me: Stateful Distributed Applications that Scale

Pat Helland published a paper called "Distributed Transactions: An Apostate's Opinion". It's about databases, and is applicable elsewhere. 

Background:
  - Speaker built a lot of tools that tried to stick databases into everything: web servers, Zope, etc.
  - Worked on Gemstone Smalltalk, a distributed database / Smalltalk language dialect.
  - Works on a distributed stream processor now; scale-independent computing for Python.

Some axioms from Pat Helland's paper:
  - To scale infinitely, you have to scale horizontally
  - To scale infinitely, you must avoid coordination
  - Distributed transactions are a form of coordination
  - Thus, to scale infinitely, you cannot use transactions!

Distributed systems are hard because they don't have the same rules as single-processor systems have.

So: what is scaling?
  - Scale resource units without violating what the resource represents e.g. customer orders can be large, but it can be handled with extra resources without having to break up the customer order.
  - The units that we scale are called 'entities': they live on a single machine and are manipulated individually. 
      - They don't have to be machine resources! Can be data, etc. 

Let's talk about entities:
  - Each entity has a unique identifier. 
  - Entities are disjoint sets of data.
  - Entities are boundaries of atomicity: either you update it all or don't update at all.
    - So to get multiple updates, you have to serialise them. Multiple updates form a queue.

Big Ideas:
  - In order to scale, you must denormalise your data so it can avoid coordination.
  - Two layers of architecture are necessary:
    - A scale-agnostic layer e.g. business logic, etc. Usually wrapper around an API.
    - A scale-aware layer. Usually done by the API.
    - To scale infinitely, your business logic has to be independent of scale.

This got applied to Wallaroo's product:
  - They call entities 'state objects'
  - 2-layer arch. is called 'scale independence' by Wallaroo

So what's hard about this? All of it!
  - Satisficing the CAP theorem (aside: partition tolerance isn't an option - see Coda Hale's https://codahale.com/you-cant-sacrifice-partition-tolerance/)
  - Ensuring message delivery guarantees (at most once? at least once? effectively once? exactly once?)
  - Ensuring message ordering guarantees (can you maintain the order?)
  - Keeping local knowledge (you have to work hard to avoid coordination)
    - Can't really refactor your way into local knowledge: you have to start with it when you architect.
  - Worry about the programming model (what will you expose to the end user?)
  - You can't refactor your way into performance - start with it from the beginning. 
    - "If it's not in a hot path, then who cares?" 
    - If you need global knowledge in order to add or remove a node, then sure, go ahead, because it's not in the hot path.
  - Network overhead (TCP congestion, etc.)
  - Data serialisation is HARD.
  - How do you verify your system? Can you even verify your system?
    - Build a test harness before you build your dist. system, otherwise you'll have a lot of pain and effort.

More links: https://github.com/SeanTAllen/pat-helland-and-me
