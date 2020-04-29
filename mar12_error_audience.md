Whenever a function detects "disappointment" (ie the inability to succeed in its primary purpose),
it probably needs to communicate this disappointment to someone(s).  Let's look at who might be the **intended audience** for that communication:

**F** - the developer that wrote the **F**unction (ie bad internal state detected - somehow let F know they F'd up)  
**D** - the **D**eveloper that called the function (ie "hey Dev, you passed null, but I told you never to do that")  
**C** - the **C**ode that **C**alled the function (ie "sorry the file cannot be found - *deal with it*")  
**U** - the end **U**ser (msg box "Could not open cats.png, no can haz cats")  

(I'll let readers reorder those and form their own acronym)

Note that **F** may not be the same as **D**, particularly if you are writing libraries and APIs that are used by others.
So it may make sense to have a different way of reporting internal errors than how you report caller errors. (An OS might have internal logging for the OS devs that is separate from application logging for the app devs, for example.)

**Note, importantly, the difference between D and C** - the _developer_ that wrote the code, and the _code_ itself.
If you detect that the **D**eveloper called the function wrong, there's not much sense telling the **C**alling **C**ode. It's wrong.
You want to tell the **D**eveloper.

You tell the **C**aling **C**ode via return codes and/or exceptions.  (The **C**alling **C**ode then likely handles it somehow, which often includs telling the User)
You tell the **D**eveloper via asserts, logs, and now/soon contracts. (And then crashing or some kind of "Panic-Save" tells the **U**ser.  Crashing is a rude and crude form of **U**ser communication, but it is communication nonetheless.)

**Typically you want each of these communication methods to be different**

ie exceptions for this, contracts for that, etc.  (Although **F** and **D** can have the same communication channels if they are the same group of people, ie the devs of the entire application.)

Now, unfortunately, it is typically the function (and thus **F** - the **F**unction author) that decides who and thus how to tell.
Yet the **F**unction author doesn't always know enough context - 
Is "file not found" a **U**ser error (because the **U**ser typed it in) or a **D**eveloper error,
because the **D**eveloper hard-coded the wrong config-file name?  The fileOpen() function can't tell.

Nonetheless, until/unless we fundamentally change error reporting (ie adding some kind of injection), it is up to the **F**unction author to decide how to report the error, by guessing the audience (intentionally or not).
Typically based on expected use.
