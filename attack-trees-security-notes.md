# Tuesday, June 12th - Attack Trees: Security 

Lessons:

  Attack trees: a tree-like diagram which shows all the steps people need to take to attack your system.

  Traditional methods - PCI, ISO27001, etc. - don't work! Proof: breaches in 2006 across companies which 
  claimed to be 'secure'. In 2010 and 2011, way more security breaches across BIG companies.

  Number one cause of security breaches: failure to patch your systems! The fastest way to do this right
  is CI/CD systems. All major exploits have been due to exploits that have been known for 30 days or more. 

  Types of risk methodologies:
    1. Component-based - if all components are right, then system as a whole is secure. 
       Thorough, exhaustive, objective.
    2. System-based (less common) - they look at systems holistically. 
       Subjective, holistic, interaction-based.

  Why are system-based risk managements better? Because of complexity theory.

  Complexity theory breaks systems into:

  1. Simple e.g. a bike. Easy to build.
  2. Complicated e.g. a car. Deterministic, easy in principle to understand, requires specialised understanding.
  3. Complex e.g. traffic. Very small changes cause massive changes in traffic conditions. Chaotic.

  Most systems that fail fail in a cascading way! 

  Risk-based management systems are good for complicated systems, but not complex ones.

  Attack trees:
    1. Get people from different domains together. e.g. business, developers, security experts.
    2. Lock them in a room, and ask them: if someone wanted to steal data, how could they do it?
    3. Make them write out a tree where every node is a step towards stealing data, and every sibling is an alternate way of stealing data[1].

  Analyse the _costs_ and _complexity_ and _consequences_ of trying an attack along each path. 

  Now you can add countermeasures to the most likely way someone could get through to the data in order of likelihood. 

  You just stick post-it notes on the wall and it works!

  aside: XMindZen for drawing attack trees (mindmapping software)

  Caveats:
    1. You need to scope the system! Otherwise you get attack paths you can do nothing about e.g. 3rd-party software.
    2. Understand your attackers! e.g. do personas for your attackers. This lets you understand what path attackers are likely
       to use given their resources.
       (note: most fraudsters have less criminal connections than you think - can't easily get fake passports, etc.)
       Example: Dorothy who commits fraud by accident (low-end); state-sponsored cyberterrorist groups (high-end, lots of capabilities)

  Steps:
    1. Pick your attacker types.
    2. What data would these attackers want?
    3. Draw a tree diagram outlining how they might do it.

  Examples of potential attackers:
    1. Insiders
    2. Criminal hackers
    3. Journalists
    4. Foreign powers.
    5. Activist groups
    6. Fraudsters

  Ask yourself: what data would attackers want?
    1. User identities
    2. Details about users's possessions
    3. Steal money directly (e.g. bank account details, etc.)
    4. Create fake details within the system i.e. ways to commit fraud
    5. Ways to take down the system 

  Ways attackers commonly use:
    1. Spoofing
    2. Tampering
    3. Repudiation
    4. Integrity
    5. DoS
    6. Privilege escalation


  [1] Correction: Ideally, you actually want your nodes on the first level to outline the overall technique of the attacker, and then breakdown how they want to accomplish it in subsequent steps. It's easier to visualise.

  Understand the impact of attacks!
    1. Rank every attack vector from 1 - 6, with 1 being the lowest order of magnitude and 6 the highest. The scale is exponential.
    2. Include the following in your estimate, and give each a ranking from 1-6:
        - Cost to attacker
        - Technical complexity (how much of the system do I need to understand?)
        - Consequences to attacker
        - Reward to the attacker
        - Damage to organisation
        - How often can it be repeated
    3. Note: numbers assigned to damage to organisation tend to be higher than or equal to reward to attacker.
    4. Formula for overall risk: (6 - cost to attacker) + (6 - technical complexity) + (6 - consequences) + reward + damage + repeatability

  Determine countermeasures: 
    1. Prevention
    2. Detection
    3. Correction
    4. Deterrence
    5. Recovery

  Figure out what they do to each factor in your attack impact.

  Why does it work so well with agile?
    - Product owner owns the tree, not a security consultant.
    - It's a workshop with the entire team (or representatives of as many of the team as possible)
      - Most developers surprisingly don't really understand security - this is often the first conversation
        most people have about this.
      - A good team size for this is about 9 or 10 people. Break it down if larger.
    - Visible output that can be hung on a wall - something that reinforces that message. You need to know what 
      vulnerabilities you might add by accident.
      - Story: a government branch classified the security threat information so nobody in team could read it and know
        where the threats were to fix them!
    - You can assess new features by referencing against the tree.
    - You can record each decision you made in a risk assessment in a wiki.
 
 This is not a panacea! Combine attack trees with a baseline of good controls. 
  - Patch OS!
  - Patch applications!
  - Whitelist applications!
  - Have separate admin controls!
