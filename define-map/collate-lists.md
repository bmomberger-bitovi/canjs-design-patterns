### Organizing data in a DefineList property

Use a getter when you want to make a transformation of a list, especially when the list is retrieved through `can-connect`:

```js
import DefineMap from 'can-define/map/'
import MyModel from '~/models/my-model';

DefineMap.extend("ListCollator", {
  get listOfItems(lastSet, resolve) {
    return MyModel.getList({}).then(resolve);
  },

  get collatedList() {
    return this.listOfItems && this.listOfItems.reduce((a, b) => {
      let collationList = a.get(b.collationProperty);
      if(!collationList) {
        collationList = [];
        a.set(b.collationProperty, collationList);
      }
      collationList.push(b);
      return a;
    }, new DefineMap());
  }
})
```

The getter automatically updates `collatedList` when `listOfItems` changes, like if the `real-time` behavior of `can-connect` inserts or removes an element.
