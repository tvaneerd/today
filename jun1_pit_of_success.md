**The Pit of Success** is a term coined by Rico Mariani at Microsoft Research in 2003.

https://medium.com/@ricomariani/the-pit-of-success-cfefc6cb64c8  
https://blogs.msdn.microsoft.com/brada/2003/10/02/the-pit-of-success/  
https://blog.codinghorror.com/falling-into-the-pit-of-success/  

> _**a well-designed system makes it easy to do the right things and annoying (but not impossible) to do the wrong things**_  
(Jeff Atwood)


ie you should just naturally "fall into" doing the right thing

How do we make pit-of-success APIs?

> _**unique_ptr paves the road to the pit of success**_  
(@tvaneerd)

`Image * Camera::takePicture()`

who owns the pointer?  Will it get deleted, or will it leak?

`unique_ptr<Image> Camera::takePicture()`

You can still leak the Image, but it is annoying (although not impossible) to do so.

TODO: more examples of good APIs


---

In fact, the definition of good code is code that encourages future code to be good - ie code that causes future code to fall into the pit of success.

Good code:
- works
- influences future code to be good

It is a recursive definition.  The more times you iterate over code and it continues to work, is a sign that the original code was good.  Bad code stops working (or increases in bugs) in less iterations.
