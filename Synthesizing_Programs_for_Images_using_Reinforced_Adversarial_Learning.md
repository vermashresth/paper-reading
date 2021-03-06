# Synthesizing Programs for Images using Reinforced Adversarial Learning

## Abstract
For generative networks, use of graphic engines is benificial because they abstract away low level details and represent images as high level programs.
Current generative techniques are limiited by hand--crafted likelihood functions or distance functions. They have trained SPIRAL, an adversarially trained agent
that generates a program which is executed by a graphics engine to interpret and sample images using generator discriminator approach. The interesting add on here is to use 
discrimiator's output as a reward signal for meaningful learning progress.

## Introduction
- Humans learn to understand picture by decomposing it into strokes. Similarly, understanding a room through imagining it's layout.
- Inverse graphics: Given a scene, generate it's code. It is non-differnciable in general, so cant optimize directly. 
- Deep Reinforced Adversarial Learning: 
  1. An adversarially Trained Agent creates Visual program
  2. Visual Program Executed by Graphic Engine
  3. The Agent is optimized by being rewarded (RL Framework) for fooling discriminator
  4. Discriminator trained to distiinguish between rendered and real image
- The work's best contribution I saw was showing evidence that  utilizing a discriminator’s output as the
reward signal for reinforcement learning is significantly
better at optimizing the pixel error between renderings
and data, compared to directly optimizing pixel error
- Also, this approach is unsupervised, unlike sketch-rnn like stuff

## SPIRAL Agent
- There is an external black-box renderer that accepts sequence of actions a1, a2, ...aN and transforms them into the domai of interest, image of 3D rendering.
- Target data distribution for which we want Generator G is pd, we want to approximate it to output generated by rendered output of our generated sequence of actions pa.
- pa is generated by recurrent neural etwork PI (policy)
- For optimizing GAN, use wassersteins distance as it handles vastly dissimilar pg and pd more gracefully

For Generator
- Since output from pg (generated output) is created from Renderer, it isn't directly differencialble. Hence, naive gradient descent isn't possible.
The authors here have used maximization of expected return using RL (REINFORCE algo)
- The reward is set to 0 for time steps upto N and then for Nth timestep, it is score of discriminator on the generated image.
- Intermediate rewards could  be added to bias the search.

Conditional Generation (Didnt UNderstand much)
- Finding a specific program that generates an image Xtarget 

Distributed LEarning
- Extension of Impala
- Actor workers for generating trajectories of PIt, at; Policy Learner for optimizing PIt, Discriminator LEarner for optimizing discriminator
- For WGAN, better to update Discriminator more frequently than generator. HEre, generation is expensive, hence they use replay buffer and for communication between actor and discrimator and D is optimized at higher rate due to differences in network sizes.

## Experiments
- Action space for strokes: 8 tuple, 1st 2nd are start and end point definining bezier curve, next 5 control preasuure, brush size, RGB, last is binary value, produce stroke or jump right
- Rewards in MNIST: To encourage single continous motion of brush, small negative reward for starting each continous sequence of strokes. Also penalize for not producing any visible strokes.
- Mujoco (COuldnt understand very well)

## Results and Future Scope
-  Using  Wasserstein discriminator’s output as a reward
function with asynchronous reinforcement learning can provide
a scaling path for visual program synthesis.

- The current
exploration strategy used in the agent is entropy-based, but
future work should address this limitation by employing sophisticated
search algorithms for policy improvement. For
instance, Monte Carlo Tree Search can be used, analogous
to AlphaGo Zero 

- the use of two arbitrary control
points is perhaps not the best way to represent strokes, as it
is hard to deal with straight lines. Actions could also directly parametrize 3D surfaces, planes and learned texture models
to invert richer visual scenes.

- On the reward side, using
a joint image-action discriminator similar to BiGAN/ALI the policy can viewed as an encoder, while the renderer becomes
a decoder) could result in a more meaningful learning
signal, since D will be forced to focus on the semantics of
the image.
