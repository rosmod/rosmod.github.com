---
layout: post
title: "Getting Started with ROSMOD"
description: "Describes what ROSMOD is and how to use it."
category: guide
tags: [guide, introduction, rosmod, project, software, systems, component]
---
{% include JB/setup %}

This post describes the design of the ROSMOD meta-model, how to use
the rosmod toolsuite, and how to deploy ROSMOD code onto systems.  If
you aren't familiar with WebGME (on which ROSMOD is built), then you
should start by reading [Introduction to WebGME]({% post_url
2016-07-01-introduction-to-webgme %}).

[ROSMOD](http://github.com/rosmod) is an extension and formalization
of [ROS](http://www.ros.org) which extends the scheduling layer,
formalizes a component model with proper execution semantics, and adds
modeling of software and systems for a full tool-suite according to
model-driven engineering.

When developing models using ROSMOD, the top-level entity is a ROSMOD
`Project`, which is a self-contained collection of

* Software 
* Systems 
* Deployments 
* Experiments

These collections act as folders to categorize and separate the many
different model objects by their associated modeling concept.  In this
way, the software defined for a project can be kept completely
separate from any of the systems on which the software may run.
Similarly, the different ways the software may be instantiated and
collocated at run-time is separated into specific deployments which
are independent of the hardware model and to some degree the software
model.

# Project Creation

To create a new project, you can either drag and drop a `Project`
object from the `Part Browser` into the canvas when viewing the
`Projects` _root node_ or you can right click on the `Projects` _root
node_ of the `Tree Browser` and create a new child of type `Project`.
Please note that you cannot drag a `Documentation` object into the
`Projects` canvas to create a documentation object and that if you
choose to create a `Documentation` object inside the `Projects` _root
node_ it will not be displayed in the `RootViz` visualizer which shows
all the Projects and will not be included in any of the generated
documentation.

# Project Attributes

Each project has special `Code Documentation` attributes: `Authors`,
`Brief Description`, and `Detailed Description`, which are best edited
by clicking on the `CodeEditor` visualizer.  This visualizer fills the
canvas with a [CodeMirror](http://www.codemirror.net) instance which
allows the user to easily edit multi-line strings with:

* automatic saving when changes are made 
* undo/redo 
* syntax highlighting
* code completion, activated with `ctrl+space` 
* code folding, using the _gutter_ buttons or `ctrl+q` on the top of the code to be folded (e.g. start of an `if` block)

while allowing the user to configure (using drop-down menus):

* the currently viewed/edited **attribute** 
* the current **color theme** of the code editor 
* the current **keybindings** associated with the code editor (supported keybindings are `sublime`, `emacs`, and `vim`)

The `CodeEditor` visualizer is used in many places throughout the UI;
any object that has attributes which support editing using the
`CodeEditor` will display the `CodeEditor` as a selection in the
visualizers list in the `Visualizer Panel`.

# Project Plugins

While viewing a project, the user can run the following plugins:

* **SoftwareGenerator**: for generating and optionally compiling the **`software` defined for the project according to the `host architectures` defined in the `system models` of the project.
* **GenerateDocumenation**: aggregates all the `Documentation` objects in the project's tree, converts them to `ReStructuredText` and compiles them into `html` and `pdf`.
* **TimingAnalysis**: generates a `Colored Petri-Net` model for performing timing analysis on the`deployments` (software instanced on _abstract_ hardware)

# Saving/Exporting the Project

Currently there are two ways to save/export and Load a `Project` or
sub-model (e.g. a `System` model).

The first way is to save a selected (sub-)tree of the WebGME model, by
right-clicking on the sub-tree's root (e.g. a `System` model) and
selecting `Export Library`.  This exported library can be shared among
WebGME models and organizations.  To **load** a library saved in this
way, simply right-click on the `parent` node in the `Tree Browser` for
which you want the library to be a child.  Select `Import/Update
Library` and choose the library file you like.

The second way is to go to the `root` of the WebGME model (i.e. the
`Projects` node), and run the plugin `ExportImport` which will provide
you a dialog to configure what you wish to do.

# Software

The `software model` contains all the information required to generate
and compile the software for the project.  Because ROSMOD is an
extension of [ROS](http://www.ros.org), software is organized by
`Packages`, which are `ROS` applications that contain executable code,
as well as _message_ and _service_ definitions.  We have extended and
formalized these concepts (described in the documentation for a
`Package`) and have included information related to required `System
Libraries` or `Source Libraries`.  These libraries are defined in the
software model and include relevant attributes required for
compilation (e.g. `link libraries` or `include directories`).

In this tutorial, we will not be covering the definition or use of
these libraries, but examples of their use can be found in the
`Blinking LEDs`, `AGSE` and `Traffic Light Controller` examples.

## Package

A ROSMOD package contains the definitions for its associated
`Messages` and `Services`, which follow the [ROS](http://www.ros.org)
definitions, as well as the definitions for ROSMOD `Components`.

### Messages

Messages contain a `definition` attribute (editable using a
[CodeMirror](http://codemirror.net) dialog).  This definition
attribute conforms to the [ROS Message Description
Specification](http://wiki.ros.org/msg).  Messages allow components to
interact using `Publishers` and `Subscribers`, through a
_non-blocking_, _one-to-many_ publish/subscribe interaction pattern.
Non-blocking means that when a component publishes a message, the
publish returns immediately, without waiting for any or all
subscribers to acknowledge that they have received the message.

Here is an example Message `definition`:

```c++
int32 X=123 
int32 Y=-123 
string FOO="this is a constant" 
string EXAMPLE="this is another constant" 
```

The `definition` is edited using the `CodeEditor` visualizer, as
described in the beginning of this sample's documentation.  Since a
`Message` has no other valid visualizers, when you double-click on a
message, it will automatically open into its definition to be
viewed/edited using the `CodeEditor` visualizer.

### Services

Services contain a `definition` attribute (editable using a
[CodeMirror](http://codemirror.net) dialog). This definition attribute
conforms to the [ROS Service Description
Specification](http://wiki.ros.org/srv).  Services allow components to
interact using `Clients` and `Servers`, through a _blocking_,
_one-to-one_ client/server interaction pattern.  Blocking means that
the component that issues the client call to the server must wait and
cannot execute other code until it receives the response from the
server.

Here is an example Service `definition`:

```c++
#request constants 
int8 FOO=1 
int8 BAR=2 
#request fields 
int8 foobar 
another_pkg/AnotherMessage msg 
--- 
#response constants 
uint32 SECRET=123456 
#response fields 
another_pkg/YetAnotherMessage val
CustomMessageDefinedInThisPackage value 
uint32 an_integer
```

The `definition` is edited using the `CodeEditor` visualizer, as
described in the beginning of this sample's documentation. Since a
`Service` has no other valid visualizers, when you double-click on a
message, it will automatically open into its definition to be
viewed/edited using the `CodeEditor` visualizer.

### Components

Components are single threaded actors which communicate with other
components using the publish/subscribe and client/server interaction
patterns. These interactions trigger operations to fire in the
components, where the operation is a function implemented by the user
inside the `operation` attribute of the relevant `subscriber` or
`server`.  The component can also have timer operations which fire
either sporadically or periodically and similarly have an `operation`
attribute in which the user specifies the c++ code to be run when the
operation executes.  These operations happen serially through the
operation queue and are not preemptable by other operations of that
component.  Inside these operations, `publisher` or `client` objects
can be used to trigger operations on components which have associated
and connected `servers` or `subscribers`.  These `publisher`,
`subscriber`, `client`, `server`, and `timer` objects are added by the
user and defined inside the component.

Components contain `forwards`, `members`, `definitions`,
`initialization`, and `destruction` attributes which provide an
interface for the user to add their own `C++ code` to the component.
Each of those attributes is (as previously) editable using the
`CodeEditor` visualizer.

* `Forwards` corresponds to code that comes _before the class
declaration_ in the generated `component header file`, e.g ```
#include <stdio.h> ``` or user-created structure or class definitions.

* `Members` corresponds to _private members and methods_ that the user
  wishes to add to the component in the generated `component header
  file`.

* `Definitions` corresponds to function definitions or other code that
  the user wishes to add to the generated `component source file`.

* `Initialization` corresponds to the code the user wishes to run when
  the component starts up to initialize members to specific values or
  begin the process of triggering other components in the system
  through publish or client interactions.

* `Destruction` allows the user to specify destruction of any objects
  they have allocated on the heap that need to be manually destructed
  during component destruction.

Additionally, components contain `User Configuration` and `User
Artifacts` attributes, which are editable as `JSON` code using the
`CodeEditor` visualizer.

* `User Configuration` is a way of specifying the options passed to
the component at run-time.  The configuration object will be stored as
a (possibly nested) dictionary within the component's config, at
`config["User Configuration"]`.  These data are used instead of
command line arguments to allow the user to send complex/nested data
structures as configuration to the component, allow multiple component
(instance) within a process to be configured with different values for
the same parameter, and to save developer time by automatically
parsing these data into usable structures.  For example, given the
following config: 

```json
{ 
  "logSensorData": true, 
  "logPeriod": 0.1,
  "logFields": 
  { 
    "time": "float", 
    "data": "int" 
  }, 
  "sensorOffsets": 
  [
     0.1, 0.2, 0.3 
  ]
} 
``` 

The user could access the configuration
structure using the following `c++` code anywhere within the
component: 

```c++ 
bool logData = config["User Configuration"]["logSensorData"].asBool(); 
float period = config["User Configuration"]["logPeriod"].asFloat(); 
std::string firstField = config["User Configuration"]["logFields"]["time"].asString();
std::string secondField = config["User Configuration"]["logFields"]["data"].asString(); 
float firstOffset = config["User Configuration"]["sensorOffsets"][0].asFloat(); 
float secondOffset = config["User Configuration"]["sensorOffsets"][1].asFloat();
```

The documentation for the generated objects can be found at
[jsoncpp](http://open-source-parsers.github.io/jsoncpp-docs/doxygen/),
which is the library used to parse the `JSON`.

* `User Artifacts` is a way for the user to specify any files that may
  be produced by the component so that the experiment / plotting
  infrastructure can manage and version them.  The attribute is
  specified as a `JSON array` of `strings` where each string is a
  filename of an output file.

Note that both the `User Artifacts` and `User Configuration` can be
overridden independently within any `Component Instance` in a
`Deployment`.

Inside the component, the user can define the interaction ports the
component supports (i.e. any `publisher`, `subscriber`, `client`, and
`server` objects), by dragging and dropping them into the component's
canvas.  The relevant `Message` or `Service` pointers for these
objects can be defined by either creating the port by dragging the
relevant `Message` or `Service` object from the `Tree Browser` into
the canvas and selecting the appropriate port type from the pop-up
dialog or by dragging the `Message` or `Service` object onto the
relevant `Pointer` of the already created port.  Alternatively, the
`Message` or `Service` object can be dragged on to the port in the
canvas.

For `Subscribers`, `Servers`, and `Timers`, you can edit the
`Operation` which gets executed on behalf of the object by double
clicking on the object to open a `CodeEditor` with the operation code.

#### Timers

Inside this aspect is where the user can specify the c++ code that
will execute upon the expiry of the relevant `timer`, or when relevant
data is received for a `subscriber` or `server`. The attributes for
the ports and timers can be specified in this aspect as well. These
attributes include the `period` of the `timer` or the `deadline` of
the subscriber operation, for instance.

#### Libraries

Also inside this aspect is where the user can select the `Set Editor`
visualizer, which allows the user to see or configure the _set_ of
**Libraries** that the component requires for
compilation/execution. The user can drag a `Source Library` or `System
Library` from the `Tree Browser` to into the `Libraries` Set Editor to
add the library as a requirement for the component.

#### Constraints

The user can drag in `constraints` from the `Part Browser` and name
them accordingly to specify that the component must be deployed onto a
`Host` which has a `Capability` with a name that matches the
constraint's name.

#### Example Receiver Component

This component acts as an example for an event-triggered component,
which only executes when it receives the appropriate `Message`
subscription operation or `Service` request.  The c++ code which
executes when these operations are triggered can be found in the
`Operation` of the respective `subscriber` and `server`.

# Systems

The `Systems` folder provides a place to group the different `System`
definitions on which you might like to run the software defined in the
`Software` model.  When _compiling_ the software using the
`SoftwareGenerator` plugin from the project's _root_ aspect, the
different system models are used to determine for which computer
`architectures` to compile the software.

## System

In a `System` model, you define `Hosts`, `Users`, `Networks`, and
`Links`:

### Users

The `Users` which have an associated **SSH Key** location and
**Directory**.  The user's **Directory** is where any processes
started during experiment deployment or compilation on behalf of the
user will be run and where any artifacts will be generated.

### Networks

A Network defines the **Subnet** of IP addresses available as well as
the **Netmask** which, together with the **Subnet** defines the range
of available IP addresses.

### Links

Links connect a _hosts_ `Interface` to a `Network` and has an
associated **IP** address that the interface will have on that
network.  The user can create links and assign the IP addresses by
clicking and dragging from the host's interface to a network.  The IP
address is displayed on the link and can be edited by clicking on the
Link and editing its IP property in the `Property Panel`.

### Hosts

The `Hosts` which are computers with a specified **OS**,
**Architecture**, **Device ID**, and **Device ID Command**.  The
**Device ID** allows the user to delineate between two different
devices (e.g. a BeagleBone Black and an NVIDIA Jetson TK1) which may
have the same _Architecture_ (e.g. `armv7l`), but may need to be
separated for binary/library incompatibility.  Because these devices
may have different subsystems, we allow the user to specify the
**Device ID Command** which is run on the host and should return a
string containing the specified _Device ID_.  Hosts can have any
number of `Interface` *children*, which are displayed as `ports` on
the `Host`.

Hosts can have two types of children: `Interfaces` and `Capabilities`.
The attributes of `Hosts` are described in the section regarding
`System` models.

#### Interfaces

Interfaces are used to specify the network interfaces available on the
host.  Each interface only has a **name**, e.g. `eth0`, specifying the
network interface as it would be seen on the actual hardware, for
instance by running `ifconfig` in _linux_.

#### Capabilities

Capabilities are the corresponding object to `Constraints`, which are
contained by `Components`.  A `Capability` corresponds to some
provision that the `Host` has that the software may need, for instance
`hasCamera`, or `hasGPIO`.  Components which have the correspondingly
named `Constraint` can only be mapped during deployment/execution to
hosts which have `Capabilities` of the same name.  Note that
`Capabilities` need not be unique within or between hosts.

#### Users

Hosts have a _Set_ of `Users` which specify the users which have valid
credentials on this host.  The set of users is viewed and edited by
selecting the `SetEditor` visualizer from within a _Host_.  You
specify that a user can access a host by dragging the user object
(which exists in the parent system model) from the tree browser into
the `Users` _SetEditor_ of the relevant host.

# Deployments

Deployments are an abstract grouping of **Instances** of `Components`
into processes (which are called, using ROS terminology, `Nodes`), and
further grouping processes into `Containers`, which are an abstract
definition of `Hosts`.  The actual mapping from `Containers` to
`Hosts` actually happens automatically in an `Experiment`.

## Deployment

A Deployment can have any number of `Containers`, which contain `Node`
processes and will execute on separate hardware from each other.  In
this way, a `Container` is an abstract notion of a `Host`.

### Container

A container can contain any number of `Nodes` (POSIX processes).
These nodes will all run in parallel on the `Host` to which this
`Container` is mapped during an `Experiment`.

#### Node

A node can contain any number of **Instances** of `Components` defined
in the `Software` model.  A node also has a **Priority** attribute
which specifies the linux scheduling priority of the node's process,
respectively.

> NOTE:: To create a `Component` _Instance_, you __never__ drag from
>the `Part Browser`, but must instead drag and drop a `Component` from
>this project's `Software` model into the canvas and select `Create
>Instance Here`.

By creating `Component Instances` in this way, the user can go into a
Component Instance and change attributes, e.g. a `Timer`'s **Period**
or **Priority**, which will not require a code re-compilation.
Further, any changes made to the original component in the software
model will automatically propagate to any `Component Instances` that
have been created from it.  Any changes made to the instance will
_override_ the original component's properties, but will not affect
the original component or any other component instances.  To make such
changes to timer or other port properties, simply double click on the
newly created **instance** and select the relevant port, after which
you may edit its attributes.  Once an attribute has been over-ridden,
any changes made to the original port attribute or component attribute
will not reflect in the instance.

Similarly, the component instance's `User Configuration` attribute can
be over-ridden here to provide instance-specific configuration
parameters, which are more powerful, complex configuration data than
command line arguments (see the `Component` documentation in the
`Software`).

Finally, each `Component Instance` corresponds to a separate thread of
the `Node`'s process.

# Experiments

Experiments are deployable instances of software mapped to hardware.
An experiment contains a pointer to a `Deployment` and a pointer to a
`System`.  In this way, the same conceptual grouping of component
instances into processes and containers can be easily redeployed onto
a different system by just swapping the experiment's `System` pointer.
The experiment will automatically ensure that the system's
capabilities satisfy the deployment's software's constraints.

## Experiment

An experiment maps a `Deployment`'s abstract grouping of component
instances into processes and finally into `Containers` to `Hosts` from
the selected `System`.

### RunExperiment Plugin

The mapping and experiment deployment is performed automatically by
the `RunExperiment` plugin based on which `Hosts` are reachable and
not busy (i.e. not running any other experiment or compilation
processes) from the selected system, and also ensures that the hosts
satisfy all of the constraints from all of the component instances in
a container's nodes.  If there are not enough hosts for the number of
containers or if the constraints of the software cannot be satisfied
by the available hosts, the `RunExperiment` plugin informs the user,
else the plugin finishes the deployment and saves the mapping into the
model for reference and showing to the user.

### StopExperiment Plugin

If an experiment is currently running (based on the existence of model
objects corresponding to the mapping of containers to hosts), the
`StopExperiment` plugin will stop all the associated experiment
processes and copy back all the components' generated logs.  The logs
are saved onto the server file system and their contents are also
copied into attributes of an automatically created `Results` object,
whose name will be the current time at which the experiment finished.
If the user opens the `Results` object and selects the `ResultsViz`
visualizer, any tracing logs that were recovered will be automatically
plotted in the canvas.
