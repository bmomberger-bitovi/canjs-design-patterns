# Promise Properties in DefineMap

### Promise values in stache

js
```js
import DefineMap from 'can-define/map/';

DefineMap.extend("PromiseValues", {
  get myValue() {
    return Promise.resolve("a value");
  }
})
```

stache
```mustache
{{myValue.value}}
```

### Promise values in stache without .value everywhere

js
```js
import DefineMap from 'can-define/map/';

DefineMap.extend("PromiseValues", {
  myValue: {
    get(val, resolve) {
      this.myValuePromise.then(resolve);
    }
  }
  get myValuePromise() {
    return Promise.resolve("a value");
  }
})
```

stache
```mustache
{{myValue}}
```