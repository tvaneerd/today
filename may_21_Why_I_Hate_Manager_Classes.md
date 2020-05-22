### Why I Hate Managers

No, not people managers (those are a love/hate - try to work somewhere with good management!),
but *manager classes*, Like `ProjectorManager` or `SceneManager` or `CardManager`, `DogManager`,... whatever.

A typical `DogManager` class has:

1. a list (ie vector) of Dogs that it manages (owns)
1. a bunch of functions that it can perform on those Dogs

Therefore, hate them.  
Q.E.D.

Oh, I may have skipped a step in my proof: **Single Responsibility Principle**.
`DogManager` has two responsibilities (above), it should only have one.

Which one?  Neither.

Throw away the Manager, and just have a bunch of (free) functions that act on containers of Dogs/Cards/Scenes/...

(Which container?
You could make the functions templatized on iterators, but you probably don't need to - just assume std::vector.
Even the standard agrees with me: https://eel.is/c++draft/sequence.reqmts#2)

Once separated, any piece of code can "manage" a bunch of Projectors - by just using std::vector - 
and any code can call functions to turn on/off all (or some) of the projectors, to walk all (or some) of the Dogs, etc.

Also, Managers tend to turn into Singletons.
You _could_ have 2 DogManagers for different parts of your DogWalker app, _but you don't_.
Once you have one DogManager, you feel compelled to put all Dog-stuff in DogManager, because, well, it is the thing that deals with Dogs.
Eventually your whole app is DogManager.

And at _some_ point, DogManager gained **state** (beyond just the list of Dogs).
Because some feature needed state
(or worse, there was state carried between two or three functions, and instead of it being passed between those functions,
it was stuffed into DogManager, because it was right there!).

Don't get me started about temporary state inside classes - often (but not always) a bad sign.

And don't get me started about Singletons.

-----

P.S. we don't need the "appeal to authority" of Single Responsibility Principle here.  Read the above and just skip over that part.
It just happens to be an example of SRP, but you can decide to hate managers without ever hearing about SRP.




