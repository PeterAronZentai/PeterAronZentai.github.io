---
layout: post
title:  "Yield nicer code in JSX"
date:   2016-08-06 22:19:55 +0100
categories: jsx iterators yield
---

Rendering a list of items in JSX is usually done with `Array.map`

```javascript
class MyComponent extends React.Component
  render() {
    return <div>
      { store.items.map(item => <span>{item}</span>) }
    </div>
  }
}
```

While the above approach in fact works, it has too issues:

- Does not support `for...of` and the ES6 iteration protocol
- Handling conditional display is awkward, for example
  - Do not render `item` if `item.visible` is `false`
  - Display items until their price field totals at 100
  - Display only the fist 10 items, the first 3 with some different markup

Whatever solution you choose you'll probably end up dynamically constructing
an array of result elements and return that from JSX.
One clean and fun way for that is `spreading out generator functions`:

```javascript

class MyComponent extends React.Component

  renderItems*(maxCount, maxSumPrice) {
    let count = 0
    let sumPrice = 0
    for(product of store.items) {
      sumPrice += product.price
      if (count++ < maxCount && sumPrice < maxSumPrice) {
        if (count < 4) {
          yield <ProductTile product={product} />
        } else {
          yield <ProductLink product={product} />
        }
      }
    }
  }

  render() {
    return <div>
      {[...this.renderItems(10, 100)]}
    </div>
  }
}
```




