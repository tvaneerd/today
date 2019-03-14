    std::vector<Extents> channelsToExtents(IChannelManager const & channels);

OK, that function converts all the channels in the ChannelManager into some other format.  But so does this function:

    std::vector<Extents> channelsToExtents(std::vector<Channel> const & channels);

What's the difference?

- the first one can access and do a bunch of other things (whatever the IChannelManager interface offers).
Imagine if IChannelManager wasn't `const`! (Which, in the first version, it wasn't)
- the second one is more flexible - it could operate on just _some_ of the channels (ie the selected ones, the small ones, whatever subset you wanted to make)
- the first has a higher likelihood to grow from a simple function into a complicated function
(because IChannelManager is more complicated than just a list of channels,
so you are "infecting" the function with unnecessary complication.  A future "fix" for IChannelManager might bleed into here)
- ...
- etc
- ...
- the second is easier to test:

`IChannelManager` needs to be mocked (and it turns out that is not simple, because it has other things that need setup and mocking)

whereas the vector version can just be called and tested like a normal function: _given these inputs, does it produce these outputs_.


The moral of the story:

### How to Mock

Don't.

(at least when you don't need to)
