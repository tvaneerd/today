Let's say you need to write some bytes somewhere (to disk, to network, etc), and the only function you have on hand is:

    void writeBytes(Dest dest, void * bytes, int len);

(Why don't you have more versatility? Is there buffering beneath that call? Do we care? (The answer is at least "maybe" or always "it depends".)
Lots of questions, but let's assume this is what we have to work with.)

If we want (and we probably do) a better interface, we can write our own helper functions:

```
template <typename T>
void write(Dest dest, T const & t)
{
   writeBytes(dest, &t, sizeof(t);
}
```

But in my case, I was writing a file format (which needed specific sized items with hard-coded values in its header),
and I wanted to be specific about types, and I didn't want to accidentally do

    write(dest, 17);
    
when I really meant

    write<short>(dest, 17);
    
so instead I had:

```
void writeByte(Dest dest, unsigned char v)
{
    writeBytes(dest, &v, 1);
}
void writeShort(Dest dest, short v)
{
    static_assert(sizeof(short) == 2, "short must be 2 bytes");
    writeBytes(dest, &v, 2);
}
void writeInt(Dest dest, int v)
{
    static_assert(sizeof(int) == 4, "int must be 4 bytes");
    writeBytes(dest, &v, 4);
}
```

I also wanted those to be specific because I might need to handle big-endian versus little-endian (as binary file formats tend to be in a fixed endian format).
So those above functions might have byte-swapping in them (maybe with constexpr-ifs to branch on the endianness of the code being compiled).

Which is another reason the templatized `write<T>` might not be a good idea (as you wouldn't know what bytes to swap - C++ reflection would be great here...)

One other thing I needed was to write a bunch of zeroes.  It is common to have some unused or must-be-zero parts in a file format header (ie look at bitmap or png files, etc)

```
void writeZero(Dest dest, int count)
{
    //...???
}
```

And that function is what I really want to talk about.

The most straightforward solution:

```
void writeZero(Dest dest, int count)
{
    while (count-- > 0)
      writeByte(dest, 0);
}
```

So the question is, what - if anything - is wrong with this code?

Well, if you measure the performance, and it is sufficient, then nothing.  But the old C programmer inside me worries about the function call overhead for each byte.

So let's "optimize" it (in quotes because premature optimization and because we are actually just guessing)

```
void writeZero(Dest dest, int count)
{
    constexpr int blockSize = 64;
    static unsigned char zeroes[blockSize]; // statics are automatically 0
    
    while (count >= blockSize) {
      writeBytes(dest, zeroes, blockSize);
      count -= blockSize;
    }
    // leftovers
    writeBytes(dest, zero, count);
}
```

What - if anything - is wrong with this code?

Well, I'm not a big fan of having 2 calls to `writeBytes()`.  Is it a big deal? Probably not, and almost definitely not if writeZero() stays small.
But if the code grows (as code does) then you run the risk of the two calls to `writeBytes()` to get separated by some distance,
and some day only one of the two calls gets updated, and the other doesn't... leading to bugs.

(Really Tony? Why do you think this super simple function would ever grow larger? HAHAHAHAHA. My sweet summer child.
OK, yes, it is hard to imagine for this function, but I've never seen a function that couldn't get bigger.  Imagine Dest changes, write() becomes
a member function.  Maybe Dest starts doing buffers, and requires explicit flush() calls - or better/worse, doesn't _require_ flush() calls but
for certain situations they would be helpful.  Maybe someone tries to add the logic for when flush() should be called (ie every 1024 bytes, and
for some reason that code ends up here instead of inside writeBytes()...  And that's just off the top of my head.  The real answer would probably be much worse.)

OK, so

```
void writeZero(Dest dest, int count)
{
    constexpr int blockSize = 64;
    static unsigned char zeroes[blockSize]; // statics are automatically 0

    while (count > 0) {
      writeBytes(dest, zeroes, std::min(count, blockSize));
      count -= blockSize;
    }
}
```

I like the look of this overall.  The std::min clearly says (and enforces) "can't write more than blockSize".  And only one call to writeBytes().
But there are multiple branches on count (one hidden within min).  You would think they could somehow be combined.

To be continued - but note, there is no elegant solution here, just variants that all choose one sacrifice over others.


