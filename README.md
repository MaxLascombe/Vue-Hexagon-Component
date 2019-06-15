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
import { Hex, HexContainer } from '{path to components folder}/Vue-Hexagon-Component'

new Vue({
  el: '#app',
  components: {
    HexContainer,
    Hex
  }
});
</script>
```
