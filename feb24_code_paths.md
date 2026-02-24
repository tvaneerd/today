_Your code should branch the way it looks like it is branching._

or more likely:

_You code should look like it is branching the way the it is branching._

<table>
<tr>
<th>
Branch and Join
</th>
<th>
Branch
</th>
</tr>
<tr>
<td  valign="top">

<pre lang="cpp">

std::optional&lt;Item> tryGet(
   std::variant&lt;Item, Other> const & src, Key key)
{
    if (std::holds_alternative&lt;Other>(src))
    {
        auto const & other = std::get&lt;Other>(src);
        if (other.hasKey(key))
            return tryGetFrom(other.getValue(key));
    }
    else
    {
        return std::get&lt;Item>(src);
    }

    return std::nullopt;
}
</pre>
</td>
<td  valign="top">

<pre lang="cpp">

std::optional&lt;Item> tryGet(
   std::variant&lt;Item, Other> const & src, Key key)
{
    if (std::holds_alternative&lt;Other>(src))
    {
        auto const & other = std::get&lt;Other>(src);
        if (other.hasKey(key))
            return tryGetFrom(other.getValue(key));

        return std::nullopt;
    }
    else
    {
        return std::get&lt;Item>(src);
    }
}
</pre>
</td>
</tr>
</table>

The code is exactly the same.  By "same", I mean it is isomorphic.  It is "as-if" - you can't tell the difference when running the code.  Same branches, same results.

(To be clear, the only difference is that the `return nullopt;` is either inside the top if block, or it is at the end of the function - nothing else is changed.
 And regardless of either place you put the return, you can only get to it is via the top branch.)
 
But the code _looks different_.

The code on the left looks like it forks into two paths, and then the paths rejoin/remerge at the end of the function. But they don't "rejoin". The else branch never rejoins.

The code on the right _looks more like_ what the branching really is - two paths that separate and never reunite.

So: keep your code honest about its branching.

### Keep Going(?)

Since the if block doesn't come back, you don't need to say `else`, you can "save" a level of indentation on the else case:


<table>
<tr>
<th>
Branch
</th>
<th>
Branch, or Not
</th>
</tr>
<tr>
<td  valign="top">

<pre lang="cpp">

std::optional&lt;Item> tryGet(
   std::variant&lt;Item, Other> const & src, Key key)
{
    if (std::holds_alternative&lt;Other>(src))
    {
        auto const & other = std::get&lt;Other>(src);
        if (other.hasKey(key))
            return tryGetFrom(other.getValue(key));

        return std::nullopt;
    }
    else
    {
        return std::get&lt;Item>(src);
    }
}
</pre>
</td>
<td  valign="top">

<pre lang="cpp">

std::optional&lt;Item> tryGet(
   std::variant&lt;Item, Other> const & src, Key key)
{
    if (std::holds_alternative&lt;Other>(src))
    {
        auto const & other = std::get&lt;Other>(src);
        if (other.hasKey(key))
            return tryGetFrom(other.getValue(key));

        return std::nullopt;
    }

    return std::get&lt;Item>(src);
}

</pre>
</td>
</tr>
</table>

Should you?

Well, this is C++, so the answer is (always) "It depends".

The branching no longer looks like two "equal" alternatives.  Instead it looks like "and exception" and a "typical", or maybe a "typical" and a "fallback".  It is hard to tell.
Maybe we should flip it all:

```
if (std::holds_alternative<Item>(src))
    return std::get<Item>(src);

auto const & other = std::get<Other>(src);
if (other.hasKey(key))
    return tryGetFrom(other.getValue(key));

return std::nullopt;
```

Wow. That looks like 3 alternatives. Is that really what is going on??  (to be continued)

I'm all for "early returns" when it is check for errors, get outta here.  Or whenever the alternative is nesting nesting nesting.

But - _without more context_ in this case we have a `variant` and we assume/guess that each alternative of the variant is "normal"
and so the code should look like equal paths - ie _with equal indenting_ - that separate and do not reunite (tear emoji).

** To be continued **

In particular:
- this example does actually look like *unequal* alternatives - it looks like "the answer is right here" vs "sometimes over there"
- find a better example? Or _another_ example?
- just a `variant<A,B>` example.  Or how about `variant<SourceA, SourceB>` (neither holds answer directly), Or `variant<SourceNew, SourceOld>` - one is favoured
- mention std::visit of course

- but the main thing was that return at the end shouldn't be there. There is no reuniting.
- but if code in the `if` and the `else` both get complicated, then the "catch all" return nullopt at the bottom makes sense.

- its like interpretive dance: the form has meaning
- its like symbolism in art: know the tropes, the patterns, communicate better

- show ALL the examples.  Where each form makes sense.  IT DEPENDS.
