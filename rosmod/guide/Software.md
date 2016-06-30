---
layout: page
title: "Software"
description: ""
---
{% include JB/setup %}

# Software

The `software model` contains all the information required to generate and compile the software for the project.  Because ROSMOD is an extension of [ROS](http://www.ros.org), software is organized by `Packages`, which are `ROS` applications that contain executable code, as well as _message_ and _service_ definitions.  We have extended and formalized these concepts (described in the documentation for a `Package`) and have included information related to required `System Libraries` or `Source Libraries`.  These libraries are defined in the software model and include relevant attributes required for compilation (e.g. `link libraries` or `include directories`).

In this tutorial, we will not be covering the definition or use of these libraries, but examples of their use can be found in the `Blinking LEDs`, `AGSE` and `Traffic Light Controller` examples.

