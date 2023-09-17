Qt's QImage class takes a pixel buffer in its constructor:

`QImage(unsigned char * buffer, int width, int height, QImage::Format format, etc etc)`

This buffer is SHARED - for the lifetime of the QImage that buffer needs to be valid.

How do you convert between my image class and Qt's?

```
QImage toImage(image_t const & image)
{
    return QImage(image.readableData(), image.width, image.height, toQImageFormat(image.channelsPerPixel()));
}
```

example use:

```
void copyToClipboard(image_t const & image)
{
    QImage qimg = toQImage(image);
    QApplication::clipboard()->setImage(qimg);
}
```

This CRASHES!
Well, not exactly. The program crashes way later, on shutdown!
Because on shutdown, Qt _finally_ decides to move the image into the clipboard. While running it basically just leaves a reference in the clipboard, telling Windows "if anyone asks for the pixels, call me".

But by shutdown, the original image_t - which owns the underlying buffer - is probably long gone.

How to fix this? What is the real bug??

Well, the sharing of the buffer was unexpected (unless you read the QImage docs, where it is clearly stated, doh!  But wait - what does QClipboard::setImage(QImage) say - it says "Copies the image into the clipboard". Hmmm, what does "copy" even mean in that context. Most Qt objects are copy-on-write, so this "copy" also just references the original buffer...).

So let's not share the buffer.  Well, except you can imagine cases where sharing is really useful - why copy when you don't need to.
ie if the QImage is just temporary, _not_ copying would be best.

What to do?

Well, definitely for copyToClipboard, we should make a copy:

```
void copyToClipboard(image_t const & image)
{
    QImage qimg = toQImage(image);
    // qimg shares the buffer with image - we need to separate that, no idea how long image will live
    // *futhermore*, and more importantly, the _clipboard_ shares the buffer with qimg and the input image_t,
    // and the clipboard's qimage may need to live until the end of the program 
    QImage copy = qimg.copy(); // this actually copies pixels into new buffer
    QApplication::clipboard()->setImage(qimg);
}
```

That works, but ... ugh.

The ugh is that copyToClipboard() "just happens to know" that `toQImage(image_t)` works that way. Does toQImage() make that clear at all? (NO, it in fact had no documentation)
Will the next use of toQImage know to do the same thing?

So the fix isn't in the right place.

Let's just fix toQImage:

```
QImage toImage(image_t const & image)
{
    QImage qimg(image.readableData(), image.width, image.height, toQImageFormat(image.channelsPerPixel()));
    // by default QImage shares the input buffer (ie shares with image_t) but we don't know how long the image_t will live, so make a copy
    return qimg.copy(); 
}
```

But what about those times we _want_ to share pixel buffers?

We could:
- take a second param: toQImage(image_t, bool_or_enum makeCopy)
- make a new function: toQImage() and toQImageCopy()
- make a new function: toSharedQImage() and toQImage()

Note the subtle difference between the second and third options - both separate the two tasks, but the important part is who gets the "good" name - toQImage().
Unless you really need the performance (in which case maybe fix your whole pipeline to not convert between image types), give the good name to the safer option.

Final code:

```
// DANGER: the return value SHARES its buffer with the input image!!!
QImage toSharedQImage(image_t const & image)
{
    return QImage(image.readableData(), image.width, image.height, toQImageFormat(image.channelsPerPixel()));
}

QImage toQImage(image_t const & image)
{
    // make a copy so we no longer share the pixel buffer with image_t, as that better matches typical (ie Regular concept) expectations
    return toSharedQImage(image).copy();
}

void copyToClipboard(image_t const & image)
{
    QImage qimg = toQImage(image);
    QApplication::clipboard()->setImage(qimg);
}
```
