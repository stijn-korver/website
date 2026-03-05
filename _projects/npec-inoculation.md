---
layout: project
title: End-to-End Inoculation System
summary: Autonomous plant inoculation system combining deep learning computer vision with robotic control, built for the Netherlands Plant Eco-phenotyping Centre (NPEC).
category: AI & Robotics
tags: [Computer Vision, Robotics, Deep Learning]
icon: 🌱
order: 1
---

## Overview

Plant research requires precise, repeatable inoculation of hundreds of plants — a process that is traditionally manual, time-consuming, and prone to inconsistency. For meaningful scientific results, researchers need standardized conditions across large sample sizes.

Developed at Breda University of Applied Sciences for the Netherlands Plant Eco-phenotyping Centre (NPEC), this project delivers a fully autonomous end-to-end inoculation system. It handles the entire workflow: from real-time plant detection using image segmentation, to precise inoculation delivery via robotic control — targeting *Arabidopsis thaliana* roots across NPEC's Hades system, which processes up to 10,000 seedlings across 2,000+ Petri dishes.

## Computer Vision Pipeline

The system needed to reliably locate root tips in images taken under challenging, variable conditions — condensation, varying root density, and limited contrast.

A U-Net-based segmentation model was trained to identify root structures (roots, seeds, shoots) in Petri dish images. A key challenge was class imbalance: background pixels vastly outnumber root pixels. This was addressed through square root class weighting, background patch filtering, and data augmentation, bringing the model from an F1-score of 0.27 on the baseline to **0.83** after 39 training iterations.

The model competed in a blind Kaggle competition, achieving **rank 12** with a sMAPE score of 6.877 on root length estimation.

| Iteration | Change | F1-Score |
|---|---|---|
| Baseline | — | 0.268 |
| + Square Root Weights | Class balancing | 0.667 |
| + Background Filtering | Patch sampling | 0.763 |
| + Data Augmentation | Regularisation | 0.771 |
| + LR on Plateau | Optimisation | **0.831** |

## Robotic Control

Two controllers were designed and benchmarked for positioning the pipette precisely over detected root tips.

A **PID controller** (Proportional-Integral-Derivative) was tuned with Kp=15.0, Ki=0.5, Kd=1.0, achieving a 100% success rate with sub-millimetre accuracy across all axes within ~70 steps.

A **Reinforcement Learning controller** was developed using Stable Baselines 3 with a custom Gymnasium environment and PyBullet simulation. While it converged significantly faster (~3 steps), it achieved only 12% success rate with higher positional error, indicating the need for further reward shaping and training time.

| Controller | Success Rate | Settling Time | Mean Error |
|---|---|---|---|
| PID | 100% | ~70 steps | 0.10 mm |
| RL | 12% | ~3 steps | 0.85 mm |

## System Integration

A coordinate transformation pipeline converts pixel-space root tip coordinates from the vision model into the robot's physical workspace coordinates. This allows the full system to run autonomously: capture image → segment roots → localise tips → position robot → inoculate.

The integration was built around NPEC's existing Hades infrastructure, with a refactored Python API that decoupled simulation control from vision and controller logic.

## Outcome

The delivered system provides NPEC with a scalable, automated alternative to manual inoculation. The PID controller proved reliable and accurate enough for production use, while the RL approach showed promise for future development with more training data and refined reward design.
