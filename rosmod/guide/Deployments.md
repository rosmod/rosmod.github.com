---
layout: page
title: "Deployments"
description: ""
---
{% include JB/setup %}

# Deployments

Deployments are an abstract grouping of **Instances** of `Components` into processes (which are called, using ROS terminology, `Nodes`), and further grouping processes into `Containers`, which are an abstract definition of `Hosts`.  The actual mapping from `Containers` to `Hosts` actually happens automatically in an `Experiment`.