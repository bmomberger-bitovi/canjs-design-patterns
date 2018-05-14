### Bound vs non-bound getters

js
```js
import DefineMap from 'can-define/map/';
import CanReflect from 'can-reflect';

const GetterExample = DefineMap.extend("GetterExample", {
  // getter runs every access
  get nonBoundGetter() {
    return Math.random();
  },
  // getter runs once, on binding
  //  that decision doesn't happen here, though.  look at init() below
  get boundGetter() {
    return Math.random();
  },
  init() {
    // bind the bound getter automatically
    this.on("boundGetter", () => {});
  }
})

const ge = new GetterExample();
  ge.nonBoundGetter; // => 0.7496542892900719
  ge.nonBoundGetter; // => 0.5319584356942422
  ge.boundGetter; // => 0.05192703745600569
  ge.boundGetter; // => 0.05192703745600569
}, 10);
```


### Force updates to bound getters

js
```js
import DefineMap from 'can-define/map/';
import CanReflect from 'can-reflect';

const UpdateablePromise = DefineMap.extend("UpdateablePromise", {
  myPromise: {
    // even though lastSetVal is not used here, its presence in the
    //  function signature changes the behavior of the getter when bound.
    get(lastSetVal) {
      return Promise.resolve(Math.random());
    }
  }
})

const up = new UpdateablePromise().on("myPromise", () => {})
setTimeout(() => {
  CanReflect.getKeyValue(up.myPromise, "value"); // => 0.7496542892900719
  CanReflect.getKeyValue(up.myPromise, "value"); // => 0.7496542892900719
  up.myPromise = 0
  setTimeout(() => {
    CanReflect.getKeyValue(up.myPromise, "value"); // => 0.05192703745600569
  })
}, 10);
```