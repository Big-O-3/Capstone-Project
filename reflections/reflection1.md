# Reflection #1

Pod Members: **Prateek Oblum, Charles Mada, Brandon Curo**

## Reflection Questions

* Name at least one successful thing this week.

We got the core discovery experience fully working end-to-end — venue search, filtering, an interactive map, and a 0–100 accessibility scoring algorithm all running against a live PostgreSQL database. On the ML side, we validated that Grounding DINO can reliably detect accessibility features like ramps, stairs, and doors in real photos.

* What were some challenges you and/or your group faced this week?

Our biggest challenge was that our original ML model, YOLO-World, couldn't detect key architectural accessibility features at any usable confidence, so we had to pivot to Grounding DINO mid-sprint. We also ran into environment setup differences across team members' laptops, where the app worked for one person but showed no data for another because of database setup inconsistencies.

* Did you finish all of your tasks in your sprint plan for this week? If you did not finish all of the planned tasks, how would you prioritize the remaining tasks on your list?  (i.e over planned, did not know how to implement certain features, miscommunication from the team, had to pivot from original plans, etc.)

We finished most of our planned tasks — the search, map, scoring, and backend endpoints are all done — but a few items slipped, mainly the frontend pages for adding venues, uploading photos, and user accounts. We'd prioritize connecting the ML detections to the venue scores first, since that's the feature that ties the whole app together, and then the community-contribution UI, since it's the least-started area.

* Did the resources provided to you help prepare you in planning and executing your capstone project sprint this week? Be specific, what resources did you find particularly helpful or which tasks did you need more support on?

Our mentor meeting was especially helpful — our mentor pushed us to de-risk the ML model first and focus on nailing 3–5 hero use cases before adding features, which directly shaped how we prioritized this week. We could still use more support on deployment, since we haven't yet moved from local development to a hosted environment.

* Which features and user stories would you consider “at risk”? How will you change your plan if those items remain “at risk”?

The community-contribution flow (adding venues, uploading photos, user login) is most at risk, since those pages are still unbuilt and depend on a teammate's portion. If they stay at risk, we'll fall back to demoing with seeded sample data and prioritize wiring the AI detections into the scoring pipeline so our core "AI-powered accessibility" story still works end-to-end.
