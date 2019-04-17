# Use Stache inside of EJS

## Problem

You may want to incrementally upgrade an old CanJS 2.x application using EJS as the templating language, into a modern one using Stache.  But trying to convert every template at once prevents effective testing, so it's better to upgrade templates to Stache slowly, over time.  That means that your EJS templates will have to import and render Stache sub-templates.  How?

## Solution

First, register Stache as a template renderer in can-legacy-view-helpers.  This code is copied from CanJS 2.3's stache engine;
in 3.x and later it is it no longer part of can-stache, so you have to add it into your app manually.

```js
import view from "can-legacy-view-helpers/view";
import stache from "can-stache";

var escMap = {
  '\n': "\\n",
  '\r': "\\r",
  '\u2028': "\\u2028",
  '\u2029': "\\u2029"
};
var esc = function(string){
  return ('' + string).replace(/["'\\\n\r\u2028\u2029]/g, function (character) {
    if("'\"\\".indexOf(character) >= 0) {
      return "\\"+character;
    } else  {
      return escMap[character];
    }
  });
};

view.register({
  suffix: "stache",

  contentType: "x-stache-template",

  // Returns a `function` that renders the view.
  fragRenderer: function(id, text) {
      return stache(text);
  },
  script: function (id, src) {
      return "can.stache(\""+esc(src)+"\")";
  }
});
```

Then, when you want to call a Stache renderer from EJS, use a skinny arrow function to hook up to the parent element.

```ejs
<%= (el)->el.appendChild(can.view.render('test.stache', someDataFromScope)); %>
```

H/T to @WooFerPPK for figuring this out.