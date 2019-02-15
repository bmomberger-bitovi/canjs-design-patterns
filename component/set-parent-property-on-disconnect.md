# Problem

Bindings are torn down before the beforeremove event and the disconnected callback.  So if we want to update a parent
property when a child component leaves the DOM, merely trying to set a bound property as the child components is
tearing down will not work.

```js
// parent
Component.extend({
  tag: "parent-element",
  view: `<child-component prop:bind="prop" /> Child is {{prop}}`,
  viewModel: {
    prop: {
      default: "disconnected"
    }
  }
});

// child
Component.extend({
  tag: "child-component",
  view: `Hey!`,
  viewModel: {
    prop: {},
    connectedCallback() {
      this.prop = "connected";
      
      return () => {
        this.prop = "disconnected"; // Whoops! this doesn't propagate to the parent
      };
    }
  },
  events: {
    "{element} beforeremove": function(el, ev) {
      this.viewModel.prop = "disconnected"; // Nope! This doesn't propagate either.
    }
});
```

# Solution 1: Pass parent viewModel as property.
This solution is easy, but not very hygienic, because the structure of the parent viewmodel 
has to be known or directed by the child viewmodel.  Also, each instance of a child component
that needs to use this pattern must have its own property or set of properties on the parent.

```js
// parent
Component.extend({
  tag: "parent-element",
  view: `<child-component parentVM:from="scope.vm" /> Child is {{prop}}`,
  viewModel: {
    prop: {
      default: "disconnected"
    }
  }
});

// child
Component.extend({
  tag: "child-component",
  view: `Hey!`,
  viewModel: {
    parenVM: {},
    connectedCallback() {
      this.parentVM.prop = "connected";
      
      return () => {
        this.parentVM.prop = "disconnected"; // No problem here
      };
    }
  },
});
```

# Solution 2: Pass an observable object property.
This solution is almost as easy as passing the parent view model. It doesn't even require a two-way
binding. In addition, you can create a definiton or a type class for what properties need to be
accounted for in the data interchange between child and parent.

```js
// parent
Component.extend({
  tag: "parent-element",
  view: `<child-component prop:from="prop" /> Child is {{prop.connection}}`,
  viewModel: {
    prop: {
      // Type: <object> is shorthand for DefineMap extended with these definitions
      Type: {
        connection: "string"
      }
      default: () => ({ connection: "disconnected" })
    }
  }
});

// child
Component.extend({
  tag: "child-component",
  view: `Hey!`,
  viewModel: {
    // You do have to be careful not to re-initialize the child reference to the
    //  object through a type conversion.  When in doubt, leave it out.
    prop: {},
    connectedCallback() {
      this.prop.connection = "connected";
      
      return () => {
        this.prop.connection = "disconnected";
      };
    }
  },
});
```

# Solution 3: Pass the property as a compute

If you're using the pass-as-compute syntax in can-stache-bindings (`"~prop"`) then the property itself
becomes the instrument of binding.  This gives you all the declaractive syntax of binding a regular
property between parent and child, but it does require you to call the property as a function on the
child side.

```js
// parent
Component.extend({
  tag: "parent-element",
  view: `<child-component prop:from="~prop" /> Child is {{prop.connection}}`,
  viewModel: {
    prop: {
      default: "disconnected"
    }
  }
});

// child
Component.extend({
  tag: "child-component",
  view: `Hey!`,
  viewModel: {
    // If you try to read the compute's value in a getter,
    //  the getter will re-run, causing a locking loop.
    // You can avoid that by wrapping the getting of the value in
    // ObservationRecorder.ignore, like this:
    // 	get(setVal){
    //    let foo;
    //    ObservationRecorder.ignore(() => {
    //      foo = setVal();
    //    })();
    //	  setVal(foo + 1);
    //    return setVal;
    // }
    // In this case, if you need to do something to a compute
    //  when it's added to the view model, I recommend using
    //  a setter instead of a getter.  Setters avoid this problem.
    // Or if you just need to set the initial value once,
    //  you can do it in init() or connectedCallback, as
    //  done below
    prop: {},
    connectedCallback() {
      this.prop("connected");
      
      return () => {
        this.prop("disconnected");
      };
    }
  },
});
```
