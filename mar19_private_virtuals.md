_I had no idea you could override a private function.  I assumed that since you couldn't call it, then you couldn't override it..._

Turns out `private` and `virtual` are orthogonal.  It can be really useful.
Virtual functions form a relationship between base and derived classes.
This need not be the same as the relationship between client code and the base interface.

And in fact probably often (always?) _should_ be separate.

One common "code smell" example is cases where the base <-> derived interface includes comments like
_"make sure derived::foo() calls base::foo() first"_.
Whenever you see that, you should think "how can I enforce that better than with a comment".
The answer is typically make `base::foo()` (that clients call) _non-virtual_,
have it do whatever needs to be done first,
and then have it call a virtual base <-> derived interface function (ie `vfoo()`) for the derived part.



<table>
<tr>
<th>
Whoops
</th>
<th>
Non-virtual Idiom
</th>
</tr>
<tr>
<td  valign="top">

<pre lang="cpp">

class Base
{
public:
    // Clients call this when
    // they need to resize blah blah.
    // Note: if you override this,
    // make sure you call Base::resize() first!
    virtual void resize()
    {
        stuff();
        thatMust();
        happen();
        
        
    }
    
    
    
};

class Derived : public Base
{
    // whoops, where's the call to Base::resize?
    void resize() override
    {
        // measure twice, cut once
        measure();
        measure();
        cut();
    }
};

</pre>
</td>
<td  valign="top">

<pre lang="cpp">

class Base
{
public:
    // Clients call this when
    // they need to resize blah blah.


    void resize()  // NON VIRTUAL!
    {
        stuff();
        thatMust();
        happen();
        
        onResize(); // virtual
    }
    
private:
    void onResize() { }
};

class Derived : public Base
{

    void onResize() override
    {
        // measure twice, cut once
        measure();
        measure();
        cut();
    }
};


</pre>
</td>
</tr>
</table>

Clients should not call `onResize()`, therefore it can be, and should be, private
(or maybe protected, but protected is often a code smell as well, ... but that's another story).

---

This pattern is sometimes called the "Non-virtual idiom" and I learned it from Herb Sutter's _Guru of the Week_ http://www.gotw.ca/publications/mill18.htm

I guess the original pattern is Template Method Pattern https://en.wikipedia.org/wiki/Template_method_pattern (note the "template" in the name does not refer to C++ templates).  Although most other languages don't allow overriding private methods, and/or having non-virtual methods, so I find there is extra control/strictness in C++'s version.
