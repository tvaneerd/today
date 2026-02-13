### TLDR

Mark file-local functions and variables as `static` even if they are in an anonymous namespace.

<table>
<tr>
<th>
Sad
</th>
<th>
Better
</th>
</tr>
<tr>
<td  valign="top">

```cpp
namespace
{
struct Foo { int x = 17; }
int func() { return 23; }
int object;
}
```

</td>
<td  valign="top">

```cpp
namespace
{
struct Foo { int x = 17; }
static int func() { return 23; }  // static!
static int object; // static!
}
```

</td>
</tr>
</table>


### Explanation

Consider this code:
```cpp
...
...
...
...
...

int func() { return 23; }
...
...
...
...
...

int g()
{
    int x = func();
    ...
}
```

Let's say I need to change `func()`. I really think it should return `17`.  How many places will be affected by that change?  Looking at the code, it appears `func()` could be used *anywhere* in your codebase, any cpp file.  This could be a big change.  (Also do you really trust your tooling/IDE to find everywhere this `func()` is being called? I've been burned too many times...)

But oh wait - maybe `func()` is local to this file!  That would really make my day easier, and I would only have to worry about changes within this file (which I can probably understand) instead of across the whole codebase, which, well, no one understands.

Well, is it?  Is `func()` local to this file?

We can't tell without scrolling up.  Up up up. Look waaaay up.

If we find an anonymous namespace, then YAY! this is going to be an easy change.  Oh wait, did we do that correctly? Is `func()` _inside_ that namespace, or did I scroll too fast and miss a `}` somewhere...

Alternatively:

```cpp
...
...
...
...
...

static int func() { return 23; }
...
...
...
...
...

int g()
{
    int x = func();
    ...
}
```
Is `func()` local to this file.
Yes.  No question. `func()` is `static`.  Feel confident making your change. Have a good day.

**The End.**


So... I shouldn't put `func()` in an anonymous namespace then?

Obvious "why not both" situation here.

You can still put `func()` in an anonymous namespace.  But you should also mark it `static`.  That `static` is a nice *local* indicator about that function.

And you still need anonymous namespaces for things like types. So they aren't going away.

As for global variables, the same logic applies - I want to look at them and know they aren't extern'd (I know, not common) outside this file, without scrolling.

#### P.S.

Once upon a time, a non-static `func()` (or variable) inside an anonymous namespace _was still an exported/mangled symbol_. It was never _found_ by another cpp file, because it was mangled with a unique garble of stuff to represent the anonymous namespace.  But it was still there taking up space (and very very very slightly making linking slower).

Anyhow that no longer happens.
