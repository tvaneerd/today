## When to use `std::move()`

### When to use `std::move()` on _move-only_ types

When it doesn't compile without it.

Say, `image` is a move-only image class:

<table>
<tr>
<th>
Code
</th>
<th>
std::move() needed?
</th>
</tr>

<tr>
<td>

gatherImage = std::move(camera->capturePicture());

</td>
<td>

**NO**

</td>
</tr>

<tr>
<td  valign="top">

functionTakesImage(std::move(camera->capturePicture()));

</td>
<td>

**NO**

</td>
</tr>


<tr>
<td>

image_t image;  
...  
return std::move(image);  

</td>
<td>

**NO**

</td>
</tr>

<tr>
<td>

functionTakesImage(std::move(gatherImage));

</td>
<td>

**Yes!**
</td>
</tr>
</table>



What's the difference?  In the last case the image _had a name_, which, importantly,
means you might refer to it later, so, to be sure about your intent, you need `std::move()`.

In the other cases, the image did not have a name (or, in the return case, had a name that was going out of scope),
so there was no (or, well, very little beyond convoluted code) way to reference the image after the move,
so moving is "safe" here, and the default.

#### For experts

temporaries, _xvalues_, _prvalues_, blah blah blah.

#### Basically...

_Use `std::move()` on move-only types when it doesn't compile without it._

Assume you don't need `std::move()`.  Just write like you were using an int.
Add the `std::move()` when it doesn't compile, and _then_ look at what the compiler is trying to tell you,
and whether that thing is OK to be moved (ie consider what happens to the *moved-from* object afterward - is that OK?).

## Yeah, but what about std::vector?

What about types that are not move-only?

Well, now that we have a feel for when you would use `std::move()` on move-only objects, apply that mostly to copyable objects.
Use `std::move()` when you don't need the data over _here_ any more, and want it over _there_.  Same general rules:

- consider what happens to the *moved-from* object afterward - is that OK?
- when you return a local vector from a function, you don't need `std::move()` - the compiler will do a better job without it.
