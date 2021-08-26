Someone asked about namespacing on one of my talks.  https://youtu.be/truDO5f2OYI

And I replied:

**Don't over-namespace.**  I've got code with 3 or 4 levels of namespacing and it sucks. `Foo::Bar::Thing::Here::It::Is`... (Particularly because we don't use `using namespace...` and I think it is important to keep it that way.)

Titus (Titus Winters, watch his talks) is pretty strong that there should only be one level on namespacing.

Sean Parent (watch all his talks, twice) points out that if you have a name collision
there is a good chance you actually have some similarity and possibly a concept
or generic algorithm - `Foo::sort()` and `Bar::sort()` probably both actually sort, maybe there is something worth investigating.
(Although English is notorious for homonyms, so collisions don't always mean similarity.)

C++'s ADL (Argument Dependent Lookup, ie overload resolution) is a blessing and a curse.
If your function is well named and you are using strong types, it is probably uniquely identifiable by name + param types and doesn't need namespacing.

`draw(Surface, Circle)` is as unique as `Circle::draw(Surface)`.

`draw(Surface, Circle)` will not be confused with `draw(Cowboy, Gun)`.  At least not by the compiler, and probably not by the developer either, in context of other code.

"When to Namespace" should probably be a whole talk.  I'd do that talk if I had a better answer than "I know it when I see it".

**Subtle Notes**

In the above, Titus is referred to by just his rare first name, but Sean is denoted by his full name.  That is the essence of the namespacing problem.

**When to Definitely Namespace**

*Definitely* namespace your library that is being exposed to the world.  You can't know what conflicts you will hit in the wild, and you nor your users can easily fix them.

**Never use `using...`**

Never use `using namespace foo;` in a public header at global scope.  You don't want to force everyone that includes your header to be forced into using namespace foo.
(Modules could help here...)

I also don't recommend it at the top of a cpp file.  Cpp files get big (unfortunately).  A using directive at the top gets lost.  When I see `find(a,b)` I want to know whether that is your `find` or the stl's, and I shouldn't have to look to the top of the file to find out.
(And IDE's with instant function lookup don't work well when you aren't in the IDE, but in a code review tool, for example)

Very localized `using` is OK, and sometimes necessary:

```
void foo()
{
    using std::swap;
    swap(a,b); // uses std::swap unless the types of a,b have a better swap defined
}
```

**So I need to type `std::` ALL. THE. TIME.???**

Yes.  You'll get use to it.  Really.

As I mentioned above, when trying to understand code, it really helps to know whether `find(a,b)` is `your::find(a,b)` or `std::find(a,b)`.

---

"Never", "Definitely", "Always", etc are not true.  There are _always_ exceptions.
