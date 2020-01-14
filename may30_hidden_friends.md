## Hidden Friend Functions

### TL;DR

_Hidden friend functions are current best practice for defining operators on your classes._

---- 

CPPIBAA - C++ Is Bad At Abbreviations  
CPPIBAN - C++ Is Bad At Naming

Note: the "hidden" in _hidden friend functions_ has little/nothing to do with _data hiding_ using private member variables.
Just like C++ overuses keywords (like `static`) to mean different things, it also overuses terminology to mean different things.

### So What _are_ hidden friend functions

Hidden friend functions are friend functions that are _defined_ (not just declared) inside the class:


<table>
<tr>
<th>
Hidden
</th>
<th>
Not Hidden
</th>
</tr>
<tr>
<td  valign="top">

<pre lang="cpp">

namespace N
{

class C
{
    int x;
    int y;
        
public:
    // this is a hidden friend function
    friend operator<(C const & a, C const & b)
    {
        return std::tie(a.x, a.y)
            < std::tie(b.x, b.y);
    }
};








} // namespace N
</pre>
</td>
<td  valign="top">

<pre lang="cpp">

namespace N
{

class C
{
    int x;
    int y;
        
public:
    // this is NOT a hidden friend function
    friend operator<(C const & a, C const & b);




};
    
// definition outside class makes it not hidden
inline C::operator<(C Const & a, C const & b)
{
   return std::tie(a.x, a.y)
        < std::tie(b.x, b.y);
}

} // namespace N
</pre>
</td>
</tr>
</table>

OK, so now I know what a _hidden friend function_ is. But...

### What's the difference (relative to  non-hidden friend functions)?

A friend function (hidden or not) is injected into the enclosing namespace (ie `N` above). (It is _not_ in the scope of `C`.)
However, this does NOT make it available for qualified and unqualified lookup
(ie lookup when the compiler tries to figure out what function/operator is being called).
The declaration only makes the name available for ADL (argument dependent lookup - ie lookup based on the arguments).

If the _definition_ is placed _outside_ the class, _then_ it becomes available for qualified/unqualified lookup.

So if the definition is _inside_ the class, it is _only_ available via ADL and not qualified/unqualified lookup.

OK, but...

### What's the point?

When a compiler sees `a + b` it needs to find the right `+` operator.
It does that by building up a list of all _available_ `+` operators.
There can be a lot of them.  For the `<<` operator, a _lot_ of them.
From that list of potential _candidates_, it picks the best match.

Hidden friend functions are _found less_.  Or, better yet, _considered less_.  They do not show up in the list of candidates as often.
This makes compile times faster, and makes compiler errors shorter (compilers often show the entire list of candidates when it can't determined the best match).

#### What about the cases where the hidden friends are _not_ found, but you wanted them to be found?

No you didn't.

C++ name lookup is the wild west.  Asking the compiler to "do the right thing" is great, until it isn't.
Some day in the future it will pick the "wrong" candidate, and you will be left scratching your head.


----

See also, http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1601r0.pdf by Walter E. Brown and Daniel Sunderland
