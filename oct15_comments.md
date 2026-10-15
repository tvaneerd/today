
## Some more thoughts on comments


> Comments should say 'why' not 'what'  
> -- me and many others

> Comment Only What the Code Cannot Say  
> The challenge is not to write comments for what code does not say,
> but to write comments for what code *cannot* say.  
> -- Kevlin Henney (https://kevlinhenney.medium.com/comment-only-what-the-code-cannot-say-dfdb7b8595ac)

> My favourite comment word is _otherwise_  
> -- me (may14_otherwise.md)

The typical "comments should explain why not what" is about comments _inside a function_. ie inside the implementation.
Eg: "yes I can *see* that the function calls sort(), and I *know* what sort() does, but I don't know *why* it is important to call sort() right here right now".

Note, however, that comments on top of a function are an entirely different _kind_ of comment - the are more like classic "documentation".
Lately I have been seeing a lot of comments explainging _why_ a function _exists_. Functions are good things (typically) and mostly need more of them. 
You shouldn't need to argue a function's existence.

For function docs, they actually _should_ say 'what'.
ie What this function is, what it does.  Often very little why.
There might be a bit of 'why' when it isn't otherwise clear why things were structured a certain way - a strange set of params or naming, etc.

Which leads to my other comment rule:

>  "leave a comment when not following the expected pattern"

That is very much a 'why'.

A function existing is expected (or at least should be - more functions please).
A function with a strange name or param structure is _not_ expected, and needs some 'why'.
(eg a function named 'push_back' is expected, a function named 'push_not_back' is unexpected)
Unfortunately it takes a lot of context to decide what is expected and what isn't.
Both context within the programming language, but also within the team, the company, the industry, etc etc etc.

But the "not following the expected pattern" isn't just for function docs,
- it is also for implementation code: "normally you might iterate over all the items here, but..."
- it is also for variables: "typically we would use a vector, but this uses a std::list because..."
- it is also for ... everything.


Vector vs list is my canonical example - if you use a vector somewhere, it rarely needs explaining;
but if you instead use a std::list, it DEFINITELY needs explaining - list is almost never the best choice.
This is, at least on the technical level, mostly for performance reasons (even when you _think_ list might be more performant, I bet it isn't).

But because vector is so often _technically_ best, it is now simply "best" _because_ it is typical.

There could be *no* performance concerns, and I still say "just use vector" - precisely because everyone uses vector.

If I see std::list with no explanation, I'm left wondering - well, *is* there a reason?,
or maybe they just don't know that everyone in C++ uses vector.
Maybe they come from a different language where list is typical, maybe they learned it in school, etc...

Or maybe list is _vital_ in this situation - how do I know?

Leave a comment.

> _When choosing a container, remember vector is best;  
> leave a comment when choosing one from the rest!_  
> -- Postmodern Programming


---

P.S. Does "I'm using struct instead of class because there are no invariants to maintain" need to be said,
or is it a known, expected pattern. Again, context. Same question: is struct vs class a topic for another (to)day? And why is that the "same question"?


