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
import { HexContainer, Hex } from '{path-to-folder}/Vue-Hexagon-Component/'

export default {
  name: 'app',
  components: {
    HexContainer,
    Hex
  }
}
</script>
```

## Features

### *HexContainer* Attributes

* __:animation_speed__ Speed of popup animation (ms). *(Default: 500)*

* __:cols__ Number of columns in the container. Value is used to compute total width of component in page. *(Default: 1)*

* __fill_color__ Color of hexagon background. *(Default: 'white')*

* __:gutter__ Padding between hexagons (px). *(Default: 10)*

* __:hex_opacity__ Base opacity of hexagons. *(Default: 1)*

* __:hex_opacity_back__ Opacity of background hexagons when one is selected. *(Default: 0.5)*

* __:hex_opacity_selected__ Opacity of selected hexagon. *(Default: 1)*

* __:hex_radius__ Radius of the hexagons (i.e. half of width) (px). *(Default: 100)*

* __id__ HexContainer element id.

* __:popup_scale__ Ratio by which selected hexagon's radius grows. *(Default: 2)*

* __:rows__ Number of rows in the container. Value is used to compute total height of component in page. (Note: accepts half-integers.) *(Default: 1)*

* __stroke_color__ Color of hexagon border. *(Default: 'black')*

* __:stroke_width__ Width of hexagon border (px). *(Default: 5)*

* __:title_popup__ Ratio by which selected hexagon's title grows. *(Default: 1.5)*

### *Hex* Attributes

* __id__ Hex element id.

* __:col__ Column in which Hex should be placed. *(For* Hex*s where __:col__ and __:row__ are not specified, the* Hex*s fill the available spaces starting in the top left corner.)*

* __fill_color__ Color of hexagon background. *(Default: HexContainer's __:fill_color__)*

* __:row__ Row in which Hex should be placed. *(See __:col__ for default behaviour.)*

* __stroke_color__ Color of hexagon border. *(Default: HexContainer's __:stroke_color__)*
