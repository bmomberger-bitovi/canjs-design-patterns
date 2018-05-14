### Add jQuery-UI components to elements in Stache

```stache

<input type="text" on:inserted="datepicker(scope.element)"></input>
```

```js
import DefineMap from 'can-define/map/';
import $ from 'jquery';
import 'jquery-ui/datepicker/';

const ViewModel = DefineMap.extend({
  datepicker (el) {
    $(el).datepicker({});
  }
});

```

### Alternate style (use Stache helpers)

```stache

<input type="text" {{datepicker}}></input>
```

```js
import DefineMap from 'can-define/map/';
import $ from 'jquery';
import 'jquery-ui/datepicker/';
import stache from 'can-stache';

stache.registerHelper("datepicker", function(opts) {
  return el => {
    $(el).datepicker({});
  }
});

// also can do helpers in components and other stache renderings directly

stache('<input type="text" {{datepicker}}></input>')({}, { datepicker: function (opts) { ... } });

```

### inserted event vs helpers

The inserted event waits for the item to be inserted into the DOM.  Helpers are run when rendering the element, before it is inserted.  This is important if other DOM content is relevant (e.g. if the parents of an element affect how it's rendered); always use `on:inserted` in these cases.

As a matter of style, viewmodel functions are considered preferable, if the functionality is local to the component.  Local helpers are discouraged.

Any number of helpers can be run on an element, but only one inserted event can be attached with "on:" bindings. [Ed. note.  Is this true? maybe not.]