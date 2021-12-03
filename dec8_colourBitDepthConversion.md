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
