<template>
  <div :id="id" class="hex-container" :style="{height: height, width: width, '--bg-op': unselectedOpacity, '--sel-op': hexOpacitySelect }">
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
    hexOpacity: { default: 1, type: Number },
    hexOpacityBack: { default: 0.5, type: Number },
    hexOpacitySelect: { default: 1, type: Number },
    hexRadius: { default: 100, type: Number },
    popupScale: { default: 2, type: Number },
    titlePopup: { default: 1.5, type: Number },
    fillColor: { default: 'white', type: String },
    strokeColor: { default: 'black', type: String },
    strokeWidth: { default: 5, type: Number },
    gutter: { default: 10, type: Number },
    animationSpeed: { default: 500, type: Number }
  },
  data() { return { oneIsSelected: false } },
  computed: {
    getHexs: function () {
      var children = this.$children;
      for (var i = 0; i < children.length; i++)
        if (children[i].$options._componentTag != "Hex")
          children.splice(i, 1); i--;
      return children;
    },
    width: function () {
      var colSpace = this.hexRadius*(2 + (this.cols-1)*1.5);
      var gutterSpace = (this.cols-1)*this.gutter*Math.cos(Math.PI/6);
      return colSpace + gutterSpace + "px";
    },
    height: function () {
      var r = this.hexRadius;
      var h = Math.sqrt(r*r-(r/2)*(r/2)); // distance from center of hex to middle of edge
      var rowSpace = Math.round(2*h*this.rows+2*r-h);
      var gutterSpace = (this.rows-0.5)*this.gutter;
      return rowSpace + gutterSpace + "px";
    },
    unselectedOpacity: function () {
      if (this.oneIsSelected)
          return this.hexOpacityBack;
      else
        return this.hexOpacity;
    }
  },
  methods: {
    unselectAll: function () {
      this.oneIsSelected = false;
      var hexs = this.getHexs;
      for (var i = 0; i < hexs.length; i++)
        if (hexs[i].isSelected())
          hexs[i].unselect();
    },
    positionHexs: function () {
      var hexs = this.getHexs;
      var needPos = [];
      var used = [];
      for (var i = 0; i < hexs.length; i++) {
        var hex = hexs[i];
        if (hex.hasPos)
          used[hex.col + hex.row*this.cols] = 1;
        else
          needPos.push(hex);
      }
      var pos;
      for (i = 0, pos = 0; i < needPos.length; i++, pos++) {
        while (used[pos] != undefined) pos++;
        hex = needPos[i];
        hex.defaultCol = pos%this.cols;
        hex.defaultRow = Math.floor(pos/this.cols);
      }
    }
  },
  mounted () {
    this.positionHexs();
    document.addEventListener("click", this.unselectAll);
  }
}
</script>

<style scoped>
  .hex-container { display: inline-block; position: relative; }
</style>
