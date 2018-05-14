### Make a step-by-step-wizard in a component.

Stache
```mustache
<div class="panel">
{{#switch currentPanel}}
  {{#case "intro"}}
    This is a panel!
  {{/case}}
  {{#case "body"}}
    A panel will remove itself when it's not current...
  {{/case}}
  {{#case "end"}}
    ...and show itself when current!
  {{/case}}
  {{#default}}
    Uh oh!  This isn't a valid panel.
  {{/default}}
{{/switch}}
</div>

<button on:click="prev()" disabled:from="not(hasPrev)">Move previous</button>
<button on:click="next()" disabled:from="not(hasNext)">Move next</button>
```

JS
```js
import DefineMap from 'can-define/map/';


const ViewModel = DefineMap.extend({
  panels: {
    value: () {
      return ["intro", "body", "end"]; // This is programmable!  Make conditional panel lists if you want.
    }
  },
  currentPanelIndex: {
    set(val) {
      if(val < 0) {
        val = 0;
      }
      if(val >= this.panels.length) {
        val = this.panels.length - 1;
      }
      return val;
    },
    value() {
      return 0;
    }
  },
  currentPanel: {
    get() {
      return this.panels[this.currentPanelIndex];
    }
  },
  next() {
    // can validate here before setting currentPanelIndex
    this.currentPanelIndex++;
  },
  prev() {
    this.currentPanelIndex--;
  },
  get hasNext() {
    return this.currentPanelIndex < this.panels.length - 1;
  },
  get hasPrev() {
    return this.currentPanelIndex > 0;
  }
})

```