
---
title: 'How Picking a Priority State Simplifies System Design'
description: 'Defining a priority state early helps focus your implementation on what matters most'
pubDate: 'September 12 2025'
heroImage: '../../assets/blog-placeholder-4.jpg'
---

## The Porty Problem

I've been thinking of a way to make working with edge cases and state transitions a bit easier starting in the planning phase of a project.
 
I started thinking about this during a meeting where the project team was trying to interpret the requirements of an Internal Employee Web Portal - we'll call this app Porty. One of the requirements for a Porty optimization project was to only allow one session to be active at a time per x.
 
I say per x, because the requirement was very vague. If you asked any of the project team what the requirement meant, you would get a different answer from each stakeholder. Is x per user? Per device? Per a maximum of x devices? We are still not sure.
 
I could go on for a very long time about how many questions are unanswered about this requirement, and perhaps one day I will, once a decision is made for this still-ongoing project. However, there is one question that I feel the most strongly about, and triggered my thoughts about state.
 
If a user has too many active sessions, what do we do?
 
We could block them from having another session until they do (insert process here).
Or we can allow them a new session then do (insert process here).
 
I'm disregarding a few details that aren't strictly needed, but I'll mention a few of them here.
 
*We can't just give a new session without killing some old ones.*
 
*If we kill sessions without notifying the user they can lose their unsubmitted changes.*
 
*Even if we allow a user a new session, we probably need to block them temporarily to warn them before killing an old session.*
 
*Implementation could potentially look very similar between both approaches anyway.*
 
*A bunch more details that a domain expert would pick up on.*


## Priority State
 
What is really of interest to me is the effect of clearly outlining which of the two states is the priority state of the system.

Is the priority state of the user that they are unauthorized or authorized?  

The state that best serves the overall meaning, purpose or function of the application should be the priority state.

Put another way, is it more important for the overall system, to block users with too many sessions, or to maintain accessibility for users with too many sessions.

Using the outlined example, if the priority state is an unauthorized user, we would lean heavily towards the requirement that a user is blocked until session count is corrected.
If the priority state is an authorized user, a requirement where a user isn't ever blocked from starting a new session would be a winner.

Even after requirements are determined, I would approach implementation very differently depending on which state is the priority.
 
Using Porty as an example, if a user being unauthorized is the priority state, a development team would naturally fill in implementation details like "what do we do if a user is denied too many times" much earlier in the process. Whereas this would be an edge case or even a non-starter if the priority state was for a user to be authorized.

On the other hand, if the priority state is a user being authorized, a team might pursue allowing a user access to the application, then cleaning up and limiting session count asynchronously after the fact. If the priority state was a user being unauthorized, it would make a lot less sense to try to tackle the synchronization and concurrency concerns that could allow a user to violate the authorization contstraints.
 
Of course, the desired state won't be the same from app to app. If I had an app containing super sensitive information, I would prioritize the unauthorized state in this example. But if the app was a game or something, I might prioritize the authorized state. For Porty, I believe the authorized state should be the priority, as its used several times a day by its core user base, doesn't contain very much sensitive data, and the session limitations already assume that the user has provided valid credential data.

## Baldurian Example
 
I wouldn't limit this thinking to security. I think it has implications for any feature that requires managing state.
 
An example of this popped up while I was watching a video about a game I really like: Baldur's Gate 3.
 
I'll spare most of the details, but simply put, there was a bug where if a player tried to sell a sword that was magically bound to their character, they would get the money from the sale, but still keep the sword.
 
Would it be better if the bug didn’t exist? Of course, and it would be better if a development team never missed an edge case as well, but I'd like to imagine that somewhere during the planning of this magical-binding feature, the team decided that it would really suck if your magic sword got lost, way more than it would suck if the magical sword didn't follow the rest of the rules that keep the game close to reality.
In this imaginary conference room, I'd like to think someone stood up and said, "Let’s make the priority state that the sword stays with the player — and we’ll figure out the rest later"

This leads me to believe defining priority state at the beginning of a project, would lead to bugs having less severe impact, because we are intentionally targeting what the more important behaviors are for our app and making those more understood and focused on. Definining a priority state would naturally lead to thinking of the priority state as a default, and alternate states being transitional states or edge cases. This way, most of the unconsidered edge cases, or complex state transitions would be on account of the deprioritized states, rather than the prioritized state.

Using the Baldur's Gate example, during implementation if the sword were made immune to all processes that could seperate the player from the sword, then exceptions to the rule were addressed, the unconsidered edge cases would be instances where the sword should be removed but wasn't. Also, if we look at the sword being on the player as a default, most of the code written to change the state of the sword, would be moving from having the sword to no longer having it. If these processes are bugged, and having the sword is the default, the buggy code would usually result in the player keeping the sword.

Every project has some amount of bugs and complexity, so I’d rather direct that complexity toward the lower priority states.

## TL;DR
 
 Defining a priority state early helps focus your implementation on what matters most, and makes edge cases easier to manage. Whether it’s a banking app or a game, this mindset can guide better design decisions.
 Every system has bugs and complexity. By deciding up front which state matters most, you ensure those inevitable problems show up in the least painful places.
 
I haven’t seen this concept formalized elsewhere — if you have, I’d love to hear about it.