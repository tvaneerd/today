### typedefs are bad

    typedef std::vector<Pose> PoseLayout;
    
The good:
- giving a concept/idea a name in the code - a PoseLayout is a layout of Poses.
- that raises the level of abstraction
- it is one step towards a type - ie maybe later PoseLayout becomes a class, as needed

The bad:
- it isn't actually a separate type from `std::vector<Pose>`, it is more like a `#define`
- this means some code might still use `std::vector<Pose>` and it isn't obvious that it means the same thing
- also means that you can't forward declare the name (ie `class PoseLayout;` doesn't work)
- it hides information, but doesn't really
- it requires a reviewer to look up PoseLayout, which would be true even if it was a class, but what did the lookup buy in this case?
- it exposes ALL functions of the underlying type.  For example, did you want `.reserve(10)` to be part of a layout?
(Maybe, maybe not, but was it even considered?)
- exposing all functions means it will be harder to make it a type later
- it also encourages scattered helper functions.  Where is `findPose()`?  Is it written multiple times? Or just inlined for-loops all over the place?

Better:

    struct PoseLayout
    {
        std::vector<Pose> poses;
    };
    
- makes a real type
- a place to deliniate what a PoseLayout does and doesn't do
- you can change things. ie as it turns out, `std::map<ChannelId, PlanarViewport>` was better than a vector with the `ChannelId` inside each `Pose`
- it is a natural place for future PoseLayout code to live

Another example:

    typedef int FooHandle;
    
Do you want FooHandles to be subtracted from each other?

### typedefs are Good

    using ChannelId = StrongId<std::string, struct ChannelTag>;

- a ChannelId really is just a StrongId over a string, unique across Channels.  No more, no less.
- (you still can't forward declare it, unfortunately)
- they are necessary:

    enable_if<condition, int, double>::type // that's a typedef

---

Bonus! Naming also came up:


### Naming is hard

"Extent" can mean many things.  Once we use it, it can only mean one thing.

