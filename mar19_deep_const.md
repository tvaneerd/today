Let's say we have a `ProjectorManager`, and you can get a `Projector` via

    Projector * ProjectorManager::getProjector(ProjectorId);

Now, what if you only have a `ProjectorManager const &`, can you still get a `Projector *`,
or should you only be able to get a `Projector const *`?


**Enter the idea of _deep-const_ (also sometimes called _logical-const_, and _const-propagation_).**

To make `ProjectorManager` have deep-constness, we can add the const version of `getProjector`:

    class ProjectorManager
    {
       ...
    public:
       Projector * ProjectorManager::getProjector(ProjectorId);
       // const version to make it deep-const:
       Projector const * ProjectorManager::getProjector(ProjectorId) const;
       ...
    };

So now when you call `getProjector(id)` on a _const_ `ProjectorManager`, it will call the const version of getProjector(),
and you will get back a `Projector const *`.

#### Why? When?

So why do you want to do that, and (somewhat equivalently) when do you want to do that?

_If in doubt, deep-const._

Typically you want deep-const.  If you only have _const_ access to the ProjectorManager,
you probably shouldn't be able to change *anything* about the state of the projectors.
If you hand out a `const ProjectorManager` you would probably be surprised if anything changed because of it.

Similarly, if you have a `Document` class, any `Subpart` you might hand out should be handed out with const access from a const Document.

Or just look at the STL.  A `const std::vector<T>` only gives you `const_iterators`, which in turn give you `T const &` access to the values in the vector.

#### So when do you _not_ want deep-const?

Sometimes, when you don't own the items in question. A classroom has a list of children, but it doesn't _own_ them.
A child can change their hair, but the class list hasn't changed.  A `const ClassList` might only protect the list itself - you can't add or remove from the list, but maybe the children can still change.

You might actually find that `ProjectorManager` only wants to protect the list, not the projectors in the list,
so maybe `Projector * getProjector(ProjectorId) const;` would be OK.  The list doesn't change.
(In particular, since a Projector is, in some sense, `volatile` - because its state changes outside the program, maybe allowing non-const accesses to Projectors isn't a big deal either.)

But, as mentioned, _If in doubt, deep-const_, so in our codebase, we only hand out `Projector const *` from `const` containers of `Projectors`.

## const and ==

In particular, _const must cover ==_.

If your class has an `==` operator, then make sure whatever is checked within `==`, is protected by `const`.

A tricky case is `std::span<T>`, which is a class that encapsulates to common pair of pointer-and-length....



