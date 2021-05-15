
## My favourite comment word is _otherwise_.

One day I got an email from someone I hadn't heard from in years. We had worked together, but had both drifted off to other companies:

> Hi Tony,
> 
> How have you been?
> 
> I just wanted to thank you for impressing upon me the power of "otherwise <bad thing that happens>" in comments.
>
> I employ otherwise often, and for that added information people have told me that they find my comments especially informative.
>
> Sincerely,
> Eljay

(I'm going to guess that he is OK with me including his first name - but I will ask him...)

_Today_ a got a message from another past coworker:

> I often quote you:
> > My favourite comment word is "Otherwise"

> Have you written that advice down anywhere public that I could link to?

I guess the advice is a keeper.
And I miss past coworkers. :-(

So, I should probably write it down.

### A story:

Working at Adobe (that's where coworker #1 also worked), there was a bug.  It was aggravating. Hard to track down. And we were nearing a release - we needed this fixed.

I debugged and debugged and debugged.  Finally figured it out.  Code FAR FAR FAR away from the problem looked like this:

```cpp

// MUST do Blerg before Frobinate
blerg();
frobinate();
```

(real function names omitted to protect the guilty)


Here's the kicker - to fix the bug, I needed to change these lines of code - I needed to reverse the order.

But _obviously_ that would cause problems.  What problems?  No idea.  But problems. I mean, the comment said "MUST" so we'd better heed the warning.  Yet the comment gave me no clues - it was almost useless.

But I needed to fix the bug.

So I went spelunking into the version control history - in the days before "git blame", etc.  Manual binary search was the only way.

Who added this code!? WHY did they do it!?

Hours later, finally found the checkin!

And yes, the order was important - it was a bug fix right before a _previous_ release went out the door.

So further spelunking - into the _old_ bug database (we had switched to a newer one)...

And determined it was no longer a problem - the issue was no longer dependent on the order of the lines (thankfully! - order dependent code, yuck!), so I could change the order without reintroducing the original bug.

So I made the change to the order and checked in the code:


```cpp

// MUST do Frobinate before Blerg
frobinate();
blerg();
```

Hahahaha.  No.


```cpp

// must do Frobinate before Blerg
// **OTHERWISE** the thingamabob isn't initialized before the whatsit
// and this will cause problems if/when someone tries to do XXX directly
// instead of the usual Foobar Etc etc long explanation here
frobinate();
blerg();
```

(And again, the real fix is to eliminate the dependency, but I was under pressure to fix the bug without making a huge change days before release.)


### Moral of the Story

My favourite comment word is "otherwise".

I carried along this advice from my Adobe days to my BlackBerry days (where I worked with coworker #2) and continue to follow it now in my Christie days.


### P.S.

Who was the terrible coder that left the almost-useless original comment?

Me.


---

### See Also


https://twitter.com/tvaneerd/status/1393375496612847616

https://twitter.com/tvaneerd/status/1337025744309018624

https://twitter.com/tvaneerd/status/836059966784012290

https://twitter.com/tvaneerd/status/981606289792028673

https://kevlinhenney.medium.com/comment-only-what-the-code-cannot-say-dfdb7b8595ac

https://twitter.com/tvaneerd/status/858405681329778688

https://www.youtube.com/watch?v=JEFDtFrR7W0

