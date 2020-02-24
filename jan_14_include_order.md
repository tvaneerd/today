```
#include "this.h"
#include "mine.h"
#include "ours.h"

#include <theirs.h>
#include <stdheaders>
```

Foo.cpp should include Foo.h first.
If `class Foo` uses `std::vector` but Foo.h forgot to include `<vector>`, then you will find out when trying to compile Foo.cpp.
If Foo.cpp instead did:

```
#include <vector>
#include "Foo.h"
```

Then you wouldn't notice that Foo.h was missing `#include <vector>`.

But some later user would notice :-(

NOTE: precompiled headers defeat this :-( - which is one more reason I dislike PCHs.
