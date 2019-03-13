Whenever a function detects "disappointment" (ie the inability to succeed in its primary purpose),
it probably needs to communicate this disappointment to someone(s).  Who is the audience:

F - the developer that wrote the Function (ie "whoops! this should never happen, but it did - internal state error")  
D - the Developer that called the function (ie "you passed null, but I told you never to do that")  
C - the Code that Called the function (ie "sorry the file cannot be found")  
U - the user (msg box etc)  

(I'll let readers reorder those and form their own acronym)

Note that F may not be the same as D, particularly if you are writing libraries and APIs.
So it may make sense to have a different way of reporting internal errors than how you report caller errors.

**Note, importantly, the difference between D and C** - the _developer_ that wrote the code, and the _code_ itself.
If you detect that the Developer called the function wrong, there's not much sense telling the Code. It's wrong.
You want to tell the Developer.

You tell the Code via return codes and/or exceptions.  (The Code then likely tells the User)
You tell the Developer via asserts, logs, and now/soon contracts. (And then crashing or some kind of "Panic-Save" tells the User)

Now, unfortunately, it is the function (and thus F - the Function author) that decides who and thus how to tell.
Yet the function author doesn't know.
Is "file not found" a user error (because the user typed it in) or a Developer error,
because the Developer hard-coded the wrong config-file name?  The fileOpen() function can't tell.

Nonetheless, until/unless we change error reporting, it is up to the Function author to decide, by guessing.
Typically based on expected use.
