There is a fundamental, unavoidable problem trying to code up a threadsafe listener pattern.

The scenario:

hmmm, I was about to show code, but it is too easy to get lost in the details.

The players:

- some sort of Listener type - could be an interface, could be `std::function`, whatever
- an "event" is when something happens and you want things to know, so you call every Listener to inform them
- a "notification" is calling one listener - ie "notify the listener" means call it about the event (maybe you pass in an Event object or not, doesn't matter)
- ListenerList - a list (ie vector, whatever) to keep track of the Listeners
- Threadsafe: Listeners can be added/removed from the list at any time, from any thread
- Threadsafe: notifications can be happen from any thread (sometimes there is a dedicated thread, or the thread where the "event" happened, etc)

So, where's the problem(s)

Well, first, the "easy" problems, like making sure the list stays stable even though it is being iterated over (during notification) and modified (add/remove listeners) from multiple threads concurrently.

This can mostly be handled by a lock around the list.

But then you can't (easily) modify the list *inside the notification*.
ie a Listener that wants to remove itself (or a remove a sibling) during its notification.

But _that_ can be handled by copying the list before making the calls,
or noticing that removeListener() is being called from the same thread that is calling the listeners, etc,
or basically, just being _really careful_ about how you iterate and modify the list (lock-free, etc etc)

OK, but here's the hard problem, broken down into two scenarios, and basically, you need to pick which you want to solve:

***Scenario A***

One thread wants to remove a listener, meanwhile another thread is calling *that very listener*

Thread A: removeListener(listener); delete listener;  
Thread B: listener->notify();  

Once that notify() has started, it is too late - you can't remove or delete the listener.  You want notify() vs remove to be atomic, but it just isn't.

OK, so put a lock around the notify:

Thread A: removeListener(listener) { blocks on listener-lock if during notify }; delete listener;  
Thread B: about to call listener. Grab listener-lock. Call listener. Unlock.  

OK, great, listener will never be called "unexpectantly". Either notify or remove, but not at same time.

***Scenario B***

But what if the callers also, for whatever reason, have a lock, like Applications often do:

Thread A: Grab some lock M; removeListener(listener) { maybe block on listener-lock }; delete listener  
Thread B: grab listener-lock (let's say successfully), call listener->notify();  
listener->notify(): try to grab lock M  

DEADLOCK.

And here is the problem.  There is no clean way out of this, there are only compromises.

- make listeners "stable", ie raw function pointers, things that can't be deleted, etc. And make sure they are prepared to be called after being "removed". Or just don't allow removal!
- tell listeners not to grab locks.  Easier said than done - if your listener calls foo(), does foo() grab a lock? Will it tomorrow?  Basically, listeners should just set a flag, and return - keep listeners simple! Or similar.
- tell callers not to hold a lock when removing a listener. Easier said than done - your bar() function removes a listener, but do you know whether callers of bar(), callers before you in the stack, hold locks or not?

The Qt solution:

Qt handles this (ie signals/slots) by using a global event queue. And each QObject knows what thread it "lives" in, so is notified from that thread.
So, listener->notify() becomes notify(listener) and doesn't actually call the listener code - it puts a item in the queue of the thread the listener lives on, which is picked up by the listener later.
(This is very similar to the above suggestion of "tell listeners not to hold a lock ... just set a flag and return", where, in the Qt case, "set a flag" is post to event queue)

But if you don't have global queues, or similar, there is no escape.


See also:

I talk I did at C++Now/Boostcon, on this very problem: https://youtu.be/RVvVQpIy6zc

