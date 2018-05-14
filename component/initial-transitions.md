### Fade in a newly created component

```js
import CanComponent from 'can-component';
import $ from 'jquery';

CanComponent.extend({
  tag: 'fade-in',
  events: {
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