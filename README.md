# Self-learning-track-bot
This repo consists of 2 simulations of bots in pre-built tracks i built using gymnasium algorithm and ppo framework, as a reinforcement learning project. 



This week, I finally stepped into Reinforcement Learning (RL). Instead of telling the car exactly what to do, I set up a system where it learns how to drive through absolute trial and error.Using Gymnasium and the PPO algorithm, I coded two different track environments from scratch:

The Circular Track: A simple, donut-shaped track. Checking if the car was in or out of bounds was easy here because I just used a basic radius formula from the center point $(0,0)$.The Multi-Turn S-Curve Track: A much tougher layout built by stringing together a bunch of 2D coordinate points (Waypoints).To give the car "eyes," I built a 5-ray Lidar sensor array. It basically shoots out virtual lasers ahead and to the sides to measure distances to the walls. This feeds a neat array of geometric data straight into the neural network so it can decide how to steer or when to hit the gas. 


When I was working on the circular track, my initial logic was super simple: reward the car for going fast (reward = speed) and give it a tiny penalty (-10) if it crashed.The Problem: The car went completely chaotic. It locked the throttle at max speed  and just violently ping-ponged back and forth across the track until it slammed into a wall.  The AI discovered a massive mathematical loophole. Because it gained so many points for flooring it every single millisecond, it realized that sprinting like crazy and crashing at the end yielded a way higher total score than driving safely and slowly. It basically treated death as acceptable overhead.

To fix that,  I had to completely redesign the reward function. I introduced a strict penalty based on how far the car drifted from the lane centerline and cranked the crash penalty up to a massive -250. This forced the car to care about its survival, and it instantly started driving smoothly.

**Upgrade to Vectors for the S-Curve** 
When I moved from the circle to a twisting S-curve, the simple radius check completely broke down.The Difficulty: I had to implement vector projection math to find the absolute closest track segment to the car at any given frame.
To fix that, I wrote a helper method that projects the car’s coordinates onto the waypoint lines using a mathematical clamping mechanism . This calculates exactly how many meters the car is from the center line, keeping boundary tracking incredibly sharp even through tight, changing corners.


NEXT, I ran a benchmark test on the S-curve track by changing Gamma , which is the discount factor controlling how far into the future the AI looks. I pitted a Long-Term Planner (gamma = 0.99) against a Short-Term Speedster (gamma = 0.70).The Surprise: The short-term model (gamma = 0.70) actually won, hitting a higher mean evaluation score of 1982.63 over the long-term model's 1923.96.The Reason: Because our track layout was relatively short, the gamma = 0.99 model became hyper-cautious about crashing way down the line and drove way too slow. The gamma = 0.70 model only cared about immediate speed gains, which ironically made it cut a beautiful, aggressive, high-speed racing line through the coordinates without breaking a sweat.

UPDATE:-
to make the project more interesting, i added a 'crashed car' in the middle of the tracks. In basic terms, i added a circular obstacle in the middle of the track such that after a certain point, the closer you get to the circle the more point it deducts every millisecond. when you finaally crash to the obstacle, it causes a MASSIVE penalty and deduction of point. This teaches the car to learn to go around it. the updated code is in the trackforbot-with-obstacle file with the graph as well.
