Colour is often stored as 3 component values, r, g, b.  Often 8 bits per channel.  But sometimes 16, or 32 bits per channel, and sometimes floats.

Let's convert between these representations!

I recall from somewhere that up-converting from 8bits to 16bits (and similarly to 32bits) can use this interesting "approximation" (which I probably learned from Graphics Gems years and years ago)

```cpp
unsigned short component8To16(unsigned char c)
{
    return (c << 8) | c;
}
unsigned long component8To32(unsigned char c)
{
    return (c << 24) | (c << 16) | (c << 8) | c;
}
```

Let's do the math

```
let x be a number in 0 to 255, want corresponding y in 0 to 65535

y = x/255*65535
= x*65535/255
= x*(256^2 - 1)/255
= x*(256 + 1)(256 - 1)/255
= x*(256 + 1)
= x*256 + x
= x<<8 | x
```

QED

who knew difference of squares would come in handy

0 to 256^4-1 left as an exercise...

----

#### And let's convert back

So how do we convert back from, say, 16bit (0 to 65535) to 8bit (0 to 255)?

In particular, should we _round_ the values?

ie `65279` = `(254<<8)|255` which is the value before `65280` = `255<<8`,  
should `65279` truncate down to `254` or round up to `255`?

Even though `255` is the "closest" mapping, it turns out we should _not_ round.

To map `[0,65535]` into `[0,255]` we want to form 256 equal "buckets" of values in `[0, 65535]`.  Those buckets need to be

0 | 1 | 2 | ... | 254 | 255 
---|---|---|---|---|---
[0, 255] | [256, 511] | [512, 767] | ... | [65024, 65279] | [65280, 65535] 

If we were to round, we'd have 

0 | 1 | 2 | ... | 254 | 255 
---|---|---|---|---|---
[0, 127] | [128, 383] | [384, 639] | ... | [64896, 65151] | [65152, 65535] 

Note that the first bucket is half the size as the rest, and the last bucket is 1.5 times the size of the rest. (Think of the last bucket as the 255 bucket ([65152,65407]) plus the half-sized 256 bucket ([65408,65535]) but 256 is clamped to 255).

So this means we do *not* want to round, and converting from 16bit to 8bit is just truncating (shifting):

```cpp
unsigned char component16To8(unsigned short c)
{
    return (unsigned char)(c >> 8);
}
unsigned char component32To8(unsigned long c)
{
    return (unsigned char)(c >> 24);
}
```

------

Hey Tony, what about floating point?  We round in that case, right?  
Right?

Yes. Yes we do.

```cpp
float component8ToF(unsigned char c)
{
    return c/255.0f;
}
float component16ToF(unsigned short c)
{
    return c/65535.0f;
}
float component32ToF(unsigned long c)
{
    return c/(float)(0xffffFFFFUL);
}

unsigned char componentFTo8(float c)
{
    return (unsigned char)(c*255 + 0.5f);
}
unsigned short componentFTo16(float c)
{
    return (unsigned short)(c*65535 + 0.5f);
}
unsigned long componentFTo32(float c)
{
    return (unsigned long)(c*0xffffFFFFUL + 0.5f);
}
```

Why, you ask?  Why round float to 8 but not 16 to 8?

¯\\\_(ツ)_/¯ 

Well, mostly because of round-tripping:

#### Round-tripping

Much of the above is motivated by round-tripping.
It would be ideal if converting values back and forth between representations would preserve values (modulo bit resolution). ie

`assert(168 == component16To8(component8To16(168)));`

If `component16To8()` was to round, we would get `168`  --8To16-->  `(168<<8)|168`  --16To8-->  `169`. ie

`assert(168 == 169);`

Similarly, for floating point we want:

`assert(168 == componentFTo8(component8ToF(168)));`

And only rounding gives us that.
