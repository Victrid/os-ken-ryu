**********************
OS-Ken application API
**********************

OS-Ken application programming model
====================================

Threads, events, and event queues
---------------------------------

OS-Ken applications are single-threaded entities which implement
various functionalities in OS-Ken. Events are messages between them.

OS-Ken applications send asynchronous events to each other.
Besides that, there are some OS-Ken-internal event sources which
are not OS-Ken applications. One of the examples of such event sources
is the OpenFlow controller.
While an event can currently contain arbitrary python objects,
it's discouraged to pass complex objects (eg. unpickleable objects)
between OS-Ken applications.

Each OS-Ken application has a receive queue for events.
The queue is FIFO and preserves the order of events.
Each OS-Ken application has a thread for event processing.
The thread keeps draining the receive queue by dequeueing an event
and calling the appropritate event handler for the event type.
Because the event handler is called in the context of
the event processing thread, it should be careful when blocking.
While an event handler is blocked, no further events for
the OS-Ken application will be processed.

There are kinds of events which are used to implement synchronous
inter-application calls between OS-Ken applications.
While such requests use the same machinery as ordinary
events, their replies are put on a queue dedicated to the transaction
to avoid deadlock.

While threads and queues are currently implemented with eventlet/greenlet,
a direct use of them in a OS-Ken application is strongly discouraged.

Contexts
--------
Contexts are ordinary python objects shared among OS-Ken applications.
The use of contexts is discouraged for new code.

Create a OS-Ken application
===========================
A OS-Ken application is a python module which defines a subclass of
ryu.base.app_manager.RyuApp.
If two or more such classes are defined in a module, the first one
(by name order) will be picked by app_manager.
An OS-Ken application is singleton: only a single instance of a given OS-Ken
application is supported.

Observe events
==============
A OS-Ken application can register itself to listen for specific events
using ryu.controller.handler.set_ev_cls decorator.

Generate events
===============
A OS-Ken application can raise events by calling appropriate
ryu.base.app_manager.RyuApp's methods like send_event or
send_event_to_observers.

Event classes
=============
An event class describes a OS-Ken event generated in the system.
By convention, event class names are prefixed by "Event".
Events are generated either by the core part of OS-Ken or OS-Ken applications.
A OS-Ken application can register its interest for a specific type of
event by providing a handler method using the
ryu.controller.handler.set_ev_cls decorator.

OpenFlow event classes
----------------------
ryu.controller.ofp_event module exports event classes which describe
receptions of OpenFlow messages from connected switches.
By convention, they are named as ryu.controller.ofp_event.EventOFPxxxx
where xxxx is the name of the corresponding OpenFlow message.
For example, EventOFPPacketIn for the packet-in message.
The OpenFlow controller part of OS-Ken automatically decodes OpenFlow messages
received from switches and send these events to OS-Ken applications which
expressed an interest using ryu.controller.handler.set_ev_cls.
OpenFlow event classes are subclasses of the following class.

.. autoclass:: ryu.controller.ofp_event.EventOFPMsgBase

See :ref:`ofproto_ref` for more info about OpenFlow messages.

ryu.base.app_manager.RyuApp
================================

See :ref:`api_ref`.

ryu.controller.handler.set_ev_cls
====================================

.. autofunction:: ryu.controller.handler.set_ev_cls

ryu.controller.controller.Datapath
=====================================

.. autoclass:: ryu.controller.controller.Datapath

ryu.controller.event.EventBase
=================================

.. autoclass:: ryu.controller.event.EventBase

ryu.controller.event.EventRequestBase
========================================

.. autoclass:: ryu.controller.event.EventRequestBase

ryu.controller.event.EventReplyBase
======================================

.. autoclass:: ryu.controller.event.EventReplyBase

ryu.controller.ofp_event.EventOFPStateChange
===============================================

.. autoclass:: ryu.controller.ofp_event.EventOFPStateChange

ryu.controller.ofp_event.EventOFPPortStateChange
===================================================

.. autoclass:: ryu.controller.ofp_event.EventOFPPortStateChange

ryu.controller.dpset.EventDP
===============================

.. autoclass:: ryu.controller.dpset.EventDP

ryu.controller.dpset.EventPortAdd
====================================

.. autoclass:: ryu.controller.dpset.EventPortAdd

ryu.controller.dpset.EventPortDelete
=======================================

.. autoclass:: ryu.controller.dpset.EventPortDelete

ryu.controller.dpset.EventPortModify
=======================================

.. autoclass:: ryu.controller.dpset.EventPortModify

ryu.controller.network.EventNetworkPort
==========================================

.. autoclass:: ryu.controller.network.EventNetworkPort

ryu.controller.network.EventNetworkDel
=========================================

.. autoclass:: ryu.controller.network.EventNetworkDel

ryu.controller.network.EventMacAddress
=========================================

.. autoclass:: ryu.controller.network.EventMacAddress

ryu.controller.tunnels.EventTunnelKeyAdd
===========================================

.. autoclass:: ryu.controller.tunnels.EventTunnelKeyAdd

ryu.controller.tunnels.EventTunnelKeyDel
===========================================

.. autoclass:: ryu.controller.tunnels.EventTunnelKeyDel

ryu.controller.tunnels.EventTunnelPort
=========================================

.. autoclass:: ryu.controller.tunnels.EventTunnelPort
