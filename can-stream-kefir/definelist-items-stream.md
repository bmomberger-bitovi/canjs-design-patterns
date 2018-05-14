### Streams for items in a DefineList from bound staches

This is specific to CanJS 3.x -- for CanJS 4.x it is probably better to listen to "patches" on the list.

JS
```js
import DefineMap from 'can-define/map/';
import DefineList from 'can-define/list/';
import canDefineStream from 'can-define-stream-kefir';

const StreamingListViewModel = DefineMap.extend({
  baseList: {
    value() {
      return new DefineList();
    }
  },
  listChanges: {
    stream() {
      // the "add" event reliably returns items when set from a stache binding
      return this.toStream(".baseList add");
    }
  }
})
canDefineStream(StreamingListViewModel);
```

Stache
```mustache
<can-import from="can-stache-bindings" />

<input type="text" value:bind="baseList[0]" />
<input type="text" value:bind="baseList[1]" />
<input type="text" value:bind="baseList[2]" />
```