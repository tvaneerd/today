**Before move semantics:**

```cpp
// turns a colour image into 3 monochrome images one for each of R, G, B
void separateRGB(std::vector<image_t> & channels, image_t const & colourImage);
```
    
**After move semantics**

```cpp
// turns a colour image into 3 monochrome images one for each of R, G, B
std::vector<image_t> separateRGB(image_t const & colourImage);
```

The second is better.

Why?
----

- trust me :-)
- The first is ambiguous - should/does it _clear_ the vector before adding the new images?  Or does it add new images to whatever is already in the vector?
(sure docs can explain the behaviour, but that is extra burden to the author, the callers, and the later code readers/maintainers/debuggers)
- inputs vs outputs is more clear
- who modifies what is more clear (ie in this case, no inputs were modified)
- ie _No inputs were harmed in the making of this output_
- more importantly - the second avoids useless/unwanted states:

```cpp
ColorBlend blend; // I never want this to be empty/incomplete/invalid, but here we are
over_time_more_code();
gets_added_here();
separateRGB(blend.channels, colourImage); // blend.channels is... public?
```
vs
```cpp
std::vector<image_t> channels = separateRGB(colourImage);
ColorBlend blend(channels);
```    
vs
```cpp
ColorBlend blend(separateRGB(colourImage)); // temp vector doesn't even exist
```


(This ties into how you do exception handling, but that is another story.  Well, one with the same punch-line: make unwanted states non-existent.)


One reason why the first is better
-------

- you can reuse the vector in the first one.
ie imagine a loop over lots of colour images (of the same size???) - you could maybe reuse the vector, and even reuse the images in the vector.
But I suspect reusing/overwriting the images already in the vector might surprise callers (they might have thought it just added to the vector instead of reusing?).
I would only write the "reuse the buffer" version once you write the loop that needs it.  (At that point you could easily refactor from the second version, and have both versions.)



