---
layout: post
title: "My First Published Research Paper!"
date: 2026-11-24
categories: [blog]
---

# Motivation
The reason behind me writing this blog is mainly to refresh my thoughts and my experiences of being on a published paper. I want to be able to explain what this paper
was about as well as my contribution to this paper, and what my thoughts were during the process of being apart of this project. 

# Introduction
I first got onto this project from an invitation from a friend who needed help with this research project, [Rohan Yelandur](https://www.linkedin.com/in/rohan-yelandur/). So, I would like to first express my gratitude to him for giving me the opportunity to join this project and be apart of a published paper! At first when I joined the project, initially all I thought I would be doing was doing data collection for a Franka Panda Robot Arm, using a space mouse. Rohan got me caught up with all of the onboarding of the project, which included learning how to use the spacemouse, how to boot up the robot, and how to run the data collection pipeline scripts. I didn't meet frequently with the main mastermind behind the project, [Shivin](https://shivindass.github.io/), until summer hit.

# What the paper was about
Now, time to get into the techincal details of this paper. I will explain this as concisely as I can using the Fei-Fei Li Black Box.

## What is the problem we are trying to solve?
Say you want to collect robot data in a particular environment so the robot can be deployed there, but collecting data in that environment is impractical due to a couple of potential reasons such as: privacy (collecting data in the consumer's home), safety, cost, and plain access. However, we have alternative environments where data collection is easier, such as lab spaces and mock setups such as apartments or kitchens. These are useful, but they aren't as useful as the target environment.

So the real problem we are trying to solve is "what data should we collect in the accessible environments so that it transfers best to the deployment task?"

## Why is SOTA not enough?

In the modern state-of-the-art (SOTA), robot data collection is mostly unguided. Teleoperators collect data based on their intution, and much of that data ends up being wasted as it contributes little to the final policy. Also, the enviroment you collect data in matters as well. 

The default answer in robot learning has been to collect broadly and rely on scale. However, recent work has shown us that carefully curated datasets can beat much larger, unfiltered ones when targeting a specific downstream domain, which tells us that not all demonstrations are equally useful. However, we can only know which subsets of data are good only **after** we've collected all of the data, so the time and labor of collecting that data has already been paid.

We want to move data collection *upstream*: instead of filtering data after collection, we guide humans toward collecting the most useful demonstrations at the start.

## What is our clever idea?
We propose Guided Data Collection (GDC), which asks which data to collect in the data collection environments to maximize performance on the deployment task.

We are given:
$\mathcal{D}$: the current dataset,

$\mathcal{D}'$: the new data to collect in data collection environments, under a fixed budget $k=\|\mathcal{D}'\|$,

$\mathcal{A}$: the learning algorithm, which maps a dataset to a policy $\pi = \mathcal{A}(\mathcal{D})$,

$\mathcal{M}$: the performance metric, which measures a deployment performance of a policy (e.g., success rate).

The goal is to find the new data that improves the policy once it is added to the training set:
$$ argmax_{\mathcal{D}'} \mathcal{M}(\mathcal{A}(\mathcal{D} \cup \mathcal{D}')) \ s.t \  |\mathcal{D}'| = k $$

## How will we evaluate it?
GUILD splits this problem into $2$ pieces and interleaves them in a close loop: **task-space exploration** using a VLM (Chat-GPT 5.2), which decides where it could be worth collecting data, and **data evaluation** using predictive data attribution[^1], which decides *how much* data each of those tasks deserves.

### Commonsense-grounded task proposals
**GUILD** gives the VLM the target-domain context (a demonstration video, a language description of the target task and a specification of available environments and objects) and asks it to propose a set of feasbile collection tasks. Each proposal specifies *which environment* to collect in, and *how to set up the scene and what manipulation task to demonstrate*.

Something to note, the VLM is not asked how much data to collect per task. It only defines the *support* of the collection distribution, i.e. the promising regions of the task space. 

### Data evaluation via predictive attribution
After a few demonstrations are collected for each proposed task, **GUILD** estimates how useful each trajectory is for the deployment without training and rolling out a new policy for every candidate dataset. We use **MAGIC**, a metagradient-based data attribution method. 

### Iterate on two timescales
**GUILD** collects data in small batches and recomputes utilities after every batch:
- Inner loop (redefine the distribution): Compute the utility for every task in the current support, ask the teleoperator to collect a small batch, add it to the dataset, repeat
- Outer loop (expand support): every $K$ inner steps, query the VLM again, this time also giving it the ranked list of previously evaluated tasks. This feedback steers new proposals toward regions related to high-utility tasks, while still allowing genuinely new ideas.

![Guild Pipeline]({{ "/assets/files/guild.png" | relative_url }})

# Closing Thoughts
Ok, now that I've explained the project, now I can get what I learned implicitly from research. The first thing I want to start off with is that I respect PhD students and research scientists even more now after doing a project like this, because this was very tough mentally. Specifically, the hard part about this project was showing up to the lab which had limited light coming through and just sitting at the desk, collecting data and doing evaluations of policies for $10$+ hours a day.

Maybe this was just a **very** labor-intensive project, but still it was hard mentally to take a break from time to time because I felt the pressure to continue to work as fast as possible, especially because my work was the main part of the project, and without it the paper wouldn't have been published in time. (It didn't help that Shivin and Rohan left early for their internships, leaving me to take the real-world experiments to the finish line)

This made me realize if I really wanted to pursue a PhD or not, something I'm still thinking about as I write this. However, I'm glad I was able to push through and get the project done, as our paper got published and accepted to the Conference of Robot Learning (CoRL '26)! I was able to go to this conference and experience my very first conference, which is something that was really cool to experience.

I'm definitely going to try another project to really see if the path of research is for me. I'll also try to get an internship during this summer to see if I like that, and choose which path I like better.

Also I realize Shivin made a whole website about **GUILD**, which explains what GUILD does better than I have. I just made this post to explain GUILD in my own words so that I can remember it better.

That's all I have to write about for right now, I'll see you in the next blog post!


