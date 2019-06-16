<template>
  <div :id="id" class="hex-container" :style="{height: height, width: width, '--unselected-opacity': unselected_opacity}">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: 'HexContainer',
  props: {
    id: String,
    cols: { default: 1, type: Number },
    rows: { default: 1, type: Number },
    hex_radius: { default: 100, type: Number },
    popup_scale: { default: 2, type: Number },
    title_popup: { default: 1.5, type: Number },
    fill_color: { default: 'white', type: String },
    stroke_color: { default: 'black', type: String },
    stroke_width: { default: 5, type: Number },
    gutter: { default: 10, type: Number },
    animation_speed: { default: 500, type: Number }
  },
  computed: {
    get_hexs: function () {
      var children = this.$children;
      for (var i = 0; i < children.length; i++)
        if (children[i].$options._componentTag != "Hex")
          children.splice(i, 1); i--;
      return children;
    },
    width: function () {
      var col_space = this.hex_radius*(2 + (this.cols-1)*1.5);
      var gutter_space = (this.cols-1)*this.gutter*Math.cos(Math.PI/6);
      return col_space + gutter_space + "px";
    },
    height: function () {
      var r = this.hex_radius;
      var h = Math.sqrt(r*r-(r/2)*(r/2)); // distance from center of hex to middle of edge
      var row_space = Math.round(2*h*this.rows+2*r-h);
      var gutter_space = (this.rows-0.5)*this.gutter;
      return row_space + gutter_space + "px";
    },
    unselected_opacity: function () {
      return (this.one_is_selected()) ? 0.4 : 0.8;
    }
  },
  methods: {
    unselect_all: function () {
      var hexs = this.get_hexs;
      for (var i = 0; i < hexs.length; i++)
        if (hexs[i].is_selected())
          hexs[i].unselect();
    },
    position_hexs: function () {
      var hexs = this.get_hexs;
      var need_pos = [];
      var used = new Array(this.cols*this.rows);
      for (var i = 0; i < hexs.length; i++) {
        var hex = hexs[i];
        if (hex.has_pos) {
          used[hex.col + hex.row*this.cols] = 1;
        } else {
          need_pos.push(hex);
        }
      }
      var pos;
      for (i = 0, pos = 0; i < need_pos.length; i++, pos++) {
        while (used[pos] != undefined) pos++;
        hex = need_pos[i];
        hex.default_col = pos%this.cols;
        hex.default_row = Math.floor(pos/this.cols);
      }
    },
    one_is_selected: function () {
      console.log(this.get_hexs);
      /*for (var i = 0; i < this.get_hexs; i++) {
        console.log(i);
        if (hexs[i].is_selected()) {
          return true;
        }
      }
      return false;*/
    }
  },
  mounted () {
    this.position_hexs();
  }
}
</script>

<style scoped>
  .hex-container { display: inline-block; position: relative; }
</style>
