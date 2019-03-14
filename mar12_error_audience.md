Whenever a function detects "disappointment" (ie the inability to succeed in its primary purpose),
it probably needs to communicate this disappointment to someone(s).  Who might be the intended audience for that communication:

F - the developer that wrote the Function (ie bad internal state detected - somehow let F know they F'd up)  
D - the Developer that called the function (ie "hey Dev, you passed null, but I told you never to do that")  
C - the Code that Called the function (ie "sorry the file cannot be found - *deal with it*")  
U - the User (msg box "Could not open cats.png, no can haz cats")  

(I'll let readers reorder those and form their own acronym)

Note that F may not be the same as D, particularly if you are writing libraries and APIs that are used by others.
So it may make sense to have a different way of reporting internal errors than how you report caller errors. (An OS might have internal logging that is separate from application logging, for example.)

**Note, importantly, the difference between D and C** - the _developer_ that wrote the code, and the _code_ itself.
If you detect that the Developer called the function wrong, there's not much sense telling the Code. It's wrong.
You want to tell the Developer.

You tell the Code via return codes and/or exceptions.  (The Code then likely handles it somehow, which often includs telling the User)
You tell the Developer via asserts, logs, and now/soon contracts. (And then crashing or some kind of "Panic-Save" tells the User.  Crashing is a rude and crude form of User communication, but it is communication nonetheless.)

**Typically you want each of these communication methods to be different**

ie exceptions for this, contracts for that, etc.  (Although F and D can have the same communication channels if they are the same group of people, ie the devs of the entire application.)

Now, unfortunately, it is the function (and thus F - the Function author) that decides who and thus how to tell.
Yet the function author doesn't always know enough context - 
Is "file not found" a user error (because the user typed it in) or a Developer error,
because the Developer hard-coded the wrong config-file name?  The fileOpen() function can't tell.

Nonetheless, until/unless we change error reporting (ie adding some kind of injection), it is up to the Function author to decide, by guessing.
Typically based on expected use.
