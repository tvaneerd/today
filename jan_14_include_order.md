```
#include "this.h"
#include "mine.h"
#include "ours.h"

#include <theirs.h>
#include <stdheaders>
```

`#include "this.h"` - Foo.cpp should include Foo.h first.  
`#include "mine.h"` - include closely related headers next  
`#include "ours.h"` - working outwards through our codebase  

Note those all used `""`, not `<>`

Then include 3rd party headers, like Boost, Qt, etc, and the std headers go last

```
#include <theirs.h>
#include <stdheaders>
```

### But Why

Imagine `class Foo` uses `std::vector` ... BUT `Foo.h` forgot to include `<vector>` :O

So `Foo.h` looks like this:
```
class Foo
{
   std::vector<int> stuff;
};
```

And `Foo.cpp` looks like this:

```
#include <vector>
#include "Foo.h"

Foo foo;
```

insert everything is fine gif here


What happens when `Bar.cpp` tries to include and use `Foo`?

```
#include "Bar.h"
#include "Foo.h"  // WHAT!?!?! THIS DOESN'T COMPILE?!?!?
```

It doesn't compile, because the compiler doesn't (yet) know what a std::vector is when it sees `std::vector<int> stuff;` in the definition of class Foo.


If Foo.cpp instead did:

```
#include "Foo.h" // VERY FIRST INCLUDE

Foo foo;
```

Then _the author of Foo_ would notice right away that anyone trying `#include "Foo.h"` would fail.

Then the author would immediately go and fix `Foo.h` to include `<vector>` and whatever other headers it needs.


---
NOTE: precompiled headers defeat this :-( - which is one more reason I dislike PCHs.
