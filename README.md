# Vue Hexagon Component

## Usage

```html
<div id="app">

  <HexContainer :cols=4 :rows=3>

    <Hex>
      <div slot="hex-title">About HexComponent</div>
      <div slot="hex-body">HexComponent is built using Vue.</div>
    </Hex>

    <Hex>
      <div slot="hex-title">Who are we?</div>
      <div slot="hex-body">Who are you?</div>
    </Hex>

  </HexContainer>

</div>

<script>
import HexContainer from '{path-to-folder}/Vue-Hexagon-Component/HexContainer.vue'
import Hex from '{path-to-folder}/Vue-Hexagon-Component/Hex.vue'

new Vue({
  el: '#app',
  components: {
    HexContainer,
    Hex
  }
});
</script>
```

## Features

### *HexContainer* Attributes

* __:cols__ Sets the number of columns in the container. Value is used to compute total width of component in page. (Default: 1)

* __:hex_radius__ Sets the radius of the hexagons (half of width). (Default: 100px)

* __:popup_scale__ Sets the ratio by which selected radius grows. (Default: 2)

* __:rows__ Sets the number of rows in the container. Value is used to compute total height of component in page. (Accepts half-integers as well.) (Default: 1)
