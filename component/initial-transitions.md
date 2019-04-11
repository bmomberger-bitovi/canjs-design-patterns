### Fade in a newly created component

```js
import CanComponent from 'can-component';
import $ from 'jquery';

CanComponent.extend({
  tag: 'fade-in',
  events: {
    // For CanJS 4 and later, this can instead be done in the viewModel
    //  as part of connectedCallback()
    inserted: (el) => {
      setTimeout(() => {
        $(el).addClass("in");
      });
    },
  }
});
```

```less

fade-in {
  opacity: 0;
  transition-property: opacity;
  transition-duration: 1s;

  &.in {
    opacity: 1;
  }
}

```
