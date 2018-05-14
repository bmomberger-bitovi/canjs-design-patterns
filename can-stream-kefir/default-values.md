### Give a stream a value before it emits anything

```js
const myRealStream = this.toStream('myevent')

const myDefaultValueStream = Kefir.constant({}).concat(myRealStream)
```

This is especially useful if using `Kefir.combine()` with multiple streams, but only some of the component streams should gatekeep when the combined stream starts emitting.  An example of this is a stream that validates entered values, but only after the Submit button is clicked.  If the user hasn't entered anything in the form fields yet, the stream to validate wouldn't start when Submit is clicked.  Passing a concatted stream starting with a sensible default value will let the _other_ constituent streams dictate when the combined stream starts emitting.