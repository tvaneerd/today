I'm not actually a huge fan of m_ for member variables, because it is a crutch for functions that are too long:

"I can't tell which variables are from my class and which are local and which are global, so m_ tells me they are from my class".

None of them should be global (and maybe globals are what needs a marker like globalbadbadbadMyVariable).  And if your function is small, the locals are obvious.

Similarly for many of the other "hungarian notation" prefixes.  Type info?  Small classes, small functions.  Encoding units?  Use the type system.
