_I think Sean Parent was the first to introduce me to the terms intrinsics and extrinsics (I had the notions, but it was still vague at the time), and the Rectangle example is his (IIRC!)_

Most people make a rectangle something like this:

    struct Rect
    {
        int x, y, w, h;
    };

In math we learned that a rectangle has just a width and a height. Being a square-cornered quadrilateral with a width and height is what makes a rectangle a rectangle. Two rectangles that have the same width and the same height are the same.

_So what's up with x and y?_

x and y are the _position_ of the rectangle. 

**Wow.**

.  
.  
.  
.  
  

OK, so that probably isn't revolutionary. 

But here's the thing: the position of the rectangle is not an *intrinsic* property of the rectangle.
It is an *extrinsic* property. In fact it is a relation between the rectangle and a coordinate space or other objects or a layout.
It doesn't belong as part of (just) the rectangle. 

So big deal? Maybe. Sometimes. 

Maybe the class is just named poorly.
Maybe it should be called a `RectangularRegion`, or `RectRegion`.
More accurate names, but there is still a relationship, with an implicit other object.

Similarly, 

    class Channel
    {
       ChannelId id;
       RectRegion buonds;
       ProjectorStuff projector;
       Etc etc;
    };

Not only is the channel bounds relative to a layout, but `ChannelId` is also relative to... something. 

`ChannelId` suggests a few things
- that there is a map somewhere that returns a channel given an id
- that there are further relationships afoot - other objects that store the id and are somehow 'linked' to the channel
- IDs are basically pointers, and can dangle, etc
- that channel is an _object_, not a _value_. >dun-dun<


There is no right answer here, accept to keep an eye out for extrinsics in your data types.
They are often better held by the object that owns the relationship. 

ie the channel list<sup id="a1">[1](#f1)</sup> can hold the channels with the IDs "beside" the channels.

A `Layout` might hold the positions of the `Shapes`.
OK it might still be a list<sup id="a1">[1](#f1)</sup> of objects with position and other info together in each item,
but `Item` could hold position and 'object intrinsics' as separate fields.

    class Item
    {
        Shape shape; // intrinsics
        // extrinsics
        Position pos;
        AlignmentRelationships alignment;
        Etc etc;
    };

Isn't that the same end result as putting position inside the object?
Well, like all things C++, _it depends_. Note that when you copy/paste an object in a layout program,
you typically do _not_ want to copy the position (you give the new object a different position).
Because it is not intrinsic. What else isn't?

Also, polymorphism - maybe position was buried deep in the base class `Shape`.
Instead it can be outside the polymorphic part. Possibly good, possibly not.

Take away:

**Keep an eye out for _extrinsics_ in your data structures.  They can reveal hidden relationships,
possibly leading to a better understanding of your overall code structure and relationships, possibly revealing better data structures to use going forward.**


---

<b id="f1">1.</b>  When I say "list", I mean vector.

