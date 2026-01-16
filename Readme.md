
# css-transform

  CSS manipulations built on node-css.

## Example

style.css:

```css
@keyframes round {
  from { border-radius: 5px }
  to { border-radius: 10px }
}

.title {
  font-size: 18px;
  padding: 5px;
}

.close {
  position: absolute;
  top: 5px;
  right: 5px;
}
```

example.js:

```js
var transform = require('css-transform')
  , fs = require('fs')
  , read = fs.readFileSync;

var vendors = ['-webkit-', '-moz-'];

var css = transform(read('examples/sink.css', 'utf8'))
  .prefix('border-radius', vendors)
  .prefix('@keyframes', vendors)
  .prefix('#dialog')
  .toString();

console.log(css);
```

stdout:

```css
@keyframes round { 
  from { border-radius: 5px; } 
  to { border-radius: 10px; } 
}
@-moz-keyframes round { 
  from { border-radius: 5px; -moz-border-radius: 5px; } 
  to { border-radius: 10px; -moz-border-radius: 10px; } 
}
@-webkit-keyframes round { 
  from { border-radius: 5px; -webkit-border-radius: 5px; } 
  to { border-radius: 10px; -webkit-border-radius: 10px; } 
}
#dialog .title {font-size: 18px; padding: 5px;}
#dialog .close {position: absolute; top: 5px; right: 5px;}
```

## API

### .use(fn)

  Use the given plugin `fn`.

  A "plugin" is simply a function when is passed the `Stylesheet` instance,
  and make invoke one or more of the other API methods. The following example
  uses a closure to pass the `vendors` option, returning the plugin `fn` which
  maps transition vendor prefixes.

```js
function transitions(vendors) {
  return function(style){
    style.map(function(prop, val) {
      var self = this;
      if ('transition' != prop) return val;
      vendors.forEach(function(vendor){
        var p = vendor + prop;
        var v = val.replace('transform', vendor + 'transform');
        self.addProperty(p, v);
      });
      this.addProperty(prop, val);
      return;
    });
  }
}
```

### .prefix(string)

  Prefix selectors with `string`.

```js
.prefix('#dialog')
```

### .prefix(property, prefixes)

  Apply vendor `prefixes` array to occurrences of `property`.

```js
.prefix('border-radius', ['-webkit-', '-moz-'])
```

### .prefixes("@keyframes", prefixes)

  Apply vendor `prefixes` array to __@keyframes__.

```js
.prefix('@keyframes', ['-webkit-', '-moz-'])
```

### .mapSelectors(callback)

  Map selector strings using the given `callback`.

```js
.mapSelectors(function(sel){
  return '#dialog ' + sel;
})
```

### .map(callback)

  Map property values with `callback`.

```js
.map(function(prop, val){
  if (0 == val.indexOf('linear-gradient')) return '-webkit-' + val;
  return val;
})
```































