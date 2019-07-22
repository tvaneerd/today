
### Const Means...

- don't touch my stuff
- I won't touch your stuff
- this doesn't change any stuff

---


#### Don't touch my stuff

    // here's access to my rect, look, but don't modify it
    Rect const & viewOfRect();

#### I won't touch your stuff

    // give me your rect, I promise to only look at it, not modify it
    void lookRect(Rect const & rc);


#### This doesn't change any stuff

    struct Rect
    {
       int area() const;  // this doesn't change Rect
       ...
    };


---

**Const means** 

Since most bugs are caused by state that has changed, `const` means

**_the bug is probably not here. Go look somewhere else._**
