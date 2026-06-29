# Self-learning-track-bot
This repo consists of 2 simulations of bots in pre-built tracks i built using gymnasium algorithm and ppo framework, as a reinforcement learning project. 


**What I Actually Built & Learnt This Week**
This week, I finally got to move away from rigid, rule-based programming (writing endless "if-else" loops) and stepped into Reinforcement Learning (RL). Instead of telling the car exactly what to do, I set up a system where it learns how to drive through absolute trial and error.Using Gymnasium and the PPO algorithm, I coded two different track environments from scratch:

The Circular Track: A simple, donut-shaped track. Checking if the car was in or out of bounds was easy here because I just used a basic radius formula from the center point $(0,0)$.The Multi-Turn S-Curve Track: A much tougher layout built by stringing together a bunch of 2D coordinate points (Waypoints).To give the car "eyes," I built a 5-ray Lidar sensor array. It basically shoots out virtual lasers ahead and to the sides to measure distances to the walls. This feeds a neat array of geometric data straight into the neural network so it can decide how to steer or when to hit the gas. 

**My Thought Process & The Walls I Hit**
When I was working on the circular track, my initial logic was super simple: reward the car for going fast (reward = speed) and give it a tiny penalty (-10) if it crashed.The Problem: The car went completely chaotic. It locked the throttle at max speed  and just violently ping-ponged back and forth across the track until it slammed into a wall.  The AI discovered a massive mathematical loophole. Because it gained so many points for flooring it every single millisecond, it realized that sprinting like crazy and crashing at the end yielded a way higher total score than driving safely and slowly. It basically treated death as acceptable overhead.

The Fix: I had to completely redesign the reward function. I introduced a strict penalty based on how far the car drifted from the lane centerline and cranked the crash penalty up to a massive -250. This forced the car to care about its survival, and it instantly started driving smoothly.

**Upgrading to Vectors for the S-Curve** 
When I moved from the circle to a twisting S-curve, the simple radius check completely broke down.The Difficulty: I had to implement vector projection math to find the absolute closest track segment to the car at any given frame.
How I Fixed It: I wrote a helper method that projects the car’s coordinates onto the waypoint lines using a mathematical clamping mechanism . This calculates exactly how many meters the car is from the center line, keeping boundary tracking incredibly sharp even through tight, changing corners.

**Hyperparameter Benchmarking**
NEXT, I ran a benchmark test on the S-curve track by changing Gamma , which is the discount factor controlling how far into the future the AI looks. I pitted a Long-Term Planner (gamma = 0.99) against a Short-Term Speedster (gamma = 0.70).The Surprise: The short-term model (gamma = 0.70) actually won, hitting a higher mean evaluation score of 1982.63 over the long-term model's 1923.96.The Reason: Because our track layout was relatively short, the gamma = 0.99 model became hyper-cautious about crashing way down the line and drove way too slow. The $\gamma = 0.70$ model only cared about immediate speed gains, which ironically made it cut a beautiful, aggressive, high-speed racing line through the coordinates without breaking a sweat.Core TakeawaysThis project really made our course concepts click for me. I didn't write a single rule telling the car when to turn left or right; it mapped out the geometry entirely on its own.
