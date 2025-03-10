There is a dichotomy that is often confused, when designing types and APIs.

1.  A _type_ should be a *strong* as it can be, giving all the guarantees that it reasonably can (for a given set of trade-offs).
2. However, a _function_ should take types as *weak* as they can be (for given trade-offs).

This is why Stepanov had Regular including TotallyOrdered - types, if orderable at all, should be totally ordered;
yet the same Stepanov had STL algorithms only requiring StrictWeakOrdering.


Similarly, we should return something specific like string (or char * if/when safe) 
But our functions should take string_view whenever they can, because they should take the *weakest* guarantees that the reasonably can.
