_"Programmers expect numbers to work like numbers"_ - Tony Van Eerd

_"Nicely put"_ - Bjarne Stroustrup

-----

`int` and `unsigned int` both _attempt_ to model the mathematical numbers (Integers and Whole Numbers, respectively), and both, due to the finite limits of computers, fall infinitely short of their goal, each modelling 0% of the full range of the items they attempt model.

But `unsigned int` somehow does it even worse than `int` does.

Or more preceisly, `unsigned int` perfectly models 100% of a set of Integers modulo some power of 2, but everyone forgets that and thinks `unsigned int` models "numbers".

```cpp

bool is_sorted(std::vector<int> const & v)
{
    for (int i = 0; i < v.size() - 1; i++)
        if (v[i] > v[i+1})
            return false;
    return true;
}
```

```cpp

// about to draw a line starting at x, of the given length,
// but first adjust x and length as necessary to not go outside the bounds of the image
void clampLine(image_t & img, int & x, int & length)
{
    if (length < 0) 
    {
        length = -length;
        x = x - length;
    }
    if (x < 0) 
    {
        length += x;
        x = 0;
    }
    if (x + length > img.width)
    {
        length = img.width - x;
        if (length < 0)
            length = 0;
    }
}
```

![image](https://github.com/tvaneerd/today/assets/3800335/0f98dc13-7c41-43cc-a560-bf16d1c114ba)

There be bugs in the above functions.

`unsigned int` is more how the underlying hardware probably works (math mod some power of 2), so it is important to know, but it doesn't work how numbers typically work.


Neither does `int`, once you get big enough numbers.

HOWEVER, `unsigned int` puts the edge cases right near the most common computer number ever: 0.

(Note: if the language made implicit conversion to unsigned an error, or some other similar but impossible because it would break the world change, then unsigned might be fine)

---

See also Bjarne Stroustrup, Herb Sutter, Andrei Alexandrescu, Sean Parent, Scott Meyers, Chandler Carruth, Michael Wong, and Stephan T. Lavavej: https://youtube.com/watch?v=Puio5dly9N8

9:50,  
41:08,  
1:02:50 short answer: “sorry”  

---

_"Only a Sith deals in absolutes"_ - Obi Wan Kenobi, on signed vs unsigned



