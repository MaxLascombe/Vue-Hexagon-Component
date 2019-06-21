<template>

  <div :id="id" ref="hex" :class="{'hex': true, 'selected': selected, 'foreground': foreground, 'midground': midground}" :style="{left: leftPos, top: topPos, '--trans-spd': animationSpeedS, '--r': radius, '--title-popup-scale': 'scale('+titlePopup+')', '--popup': popup, '--popup-scale': popupScale}">

    <svg :style="{width: radius*2+'px', height: radius*2+'px'}">
      <polygon :points="hexCoords" :style="{fill:fill, stroke:stroke, 'stroke-width': strokeWidth}" @click="select" />
      Sorry, your browser does not support inline SVG.
    </svg>

    <div class="hex-title" @click="select">
      <slot name="hex-title">Default Title</slot>
    </div>

    <div class="hex-body">
      <div class="hex-body-content">
        <div class="left-shape-inside"></div>
        <div class="right-shape-inside"></div>
        <slot name="hex-body">Default Body</slot>
      </div>
    </div>

  </div>

</template>

<script>
export default {
  name: 'Hex',
  props: {
    id: String,
    col: Number,
    row: Number,
    fillColor: String,
    strokeColor: String
  },
  data() {
    return {
      defaultCol: 0,
      defaultRow: 0,
      selected: false,
      midground: false,
      foreground: false
    }
  },
  computed: {
    getCol: function () { return (this.hasPos) ? this.col : this.defaultCol; },
    getRow: function () { return (this.hasPos) ? this.row : this.defaultRow; },
    radius: function () { return this.$parent.hexRadius; },
    popup: function () { return this.$parent.popupScale; },
    titlePopup: function () { return this.$parent.titlePopup; },
    gutter: function () { return this.$parent.gutter; },
    strokeWidth: function () { return this.$parent.strokeWidth; },
    animationSpeedMS: function () { return this.$parent.animationSpeed; },
    hasPos: function () { return (this.col != undefined && this.row != undefined); },
    fill: function () { return (this.fillColor == undefined) ? this.$parent.fillColor : this.fillColor; },
    stroke: function () { return (this.strokeColor == undefined) ? this.$parent.strokeColor : this.strokeColor; },
    colsFlipped: function () { return (this.$parent.flipColumns) ? 1 : 0; },
    topPos: function () {
      var r = this.radius;
      var h = Math.sqrt(r*r-(r/2)*(r/2));
      var oddColumnGutterDisplacement = ((this.getCol+this.colsFlipped)%2)*this.gutter/2;
      if (this.selected)
        return '50%';
      else
        return (this.getCol+this.colsFlipped)%2*h + (2*h)*this.getRow + oddColumnGutterDisplacement + this.getRow*this.gutter + "px";
    },
    leftPos: function () {
      if (this.selected)
        return '50%';
      else
        return this.getCol*this.radius*1.5 + this.getCol*this.gutter*Math.cos(Math.PI/6) + "px";
    },
    animationSpeedS: function () {
      return parseFloat(this.animationSpeedMS)/1000 + "s";
    },
    hexCoords: function () {
      var r = this.radius;
      var h = Math.sqrt(r*r-(r/2)*(r/2));
      var s = Math.round(Math.sqrt(2)*this.strokeWidth/2);

      var TL = (Math.round(r/2)+s) + ',' + (Math.round(r-h)+s);
      var TR = (Math.round(3*r/2)-s) + ',' + (Math.round(r-h)+s);
      var R = (2*r-s) + ',' + r;
      var BR = (Math.round(3*r/2)-s) + ',' + (Math.round(r+h)-s);
      var BL = (Math.round(r/2)+s) + ',' + (Math.round(r+h)-s);
      var L = s + ',' + r;

      return TL+' '+TR+' '+R+' '+BR+' '+BL+' '+L;
    },
    popupScale: function () {
      return 'scale(' + this.popup + ')';
    }
  },
  methods: {
    toggleSelected: function () {
      if (this.selected) {
        this.unselect();
      } else {
        this.select();
      }
    },
    select: function () {
      this.$parent.unselectAll();
      this.$parent.oneIsSelected = true;
      this.foreground = true;
      this.selected = true;
    },
    unselect: function () {
      if (this.selected)
        this.$parent.oneIsSelected = false;
      this.foreground = false;
      this.midground = true;
      setTimeout(() => this.midground = false, this.animationSpeedMS);
      this.selected = false;
    },
    isSelected: function () {
      return this.selected;
    }
  }
}

</script>

<style scoped>

  .hex, .hex > div, .hex > svg { position: absolute; }

  .hex {
    pointer-events: none;
    width: calc(var(--r)*2px); height: calc(var(--r)*2px);
    overflow: hidden;
    transition-property: left, top, transform, width, height, opacity; transition-duration: var(--trans-spd); }

  .hex > svg {
    pointer-events: none;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    transition: transform var(--trans-spd); }

  div.hex:not(.selected) { opacity: var(--bg-op); cursor: pointer; }

  .selected {
    transform: translate(-50%, -50%);
    width: calc(var(--r)*var(--popup)*2px); height: calc(var(--r)*var(--popup)*2px);
    opacity: var(--sel-op); }

  .selected > svg { transform: translate(-50%, -50%) var(--popup-scale); }

  .selected .hex-title {
    top: 30%;
    transform: translate(-50%, -50%) var(--title-popup-scale); }
  .selected .hex-body {
    height: 200px;
    visibility: visible;
    opacity: 1; }
  .midground, .midground .hex-title, .midground .hex-body { z-index: 2; }
  .foreground, .foreground .hex-title, .foreground .hex-body { z-index: 3; }

  .hex-title {
    pointer-events: auto;
    top: 50%; left: 50%;
    transform-origin: top;
    transform: translate(-50%, -50%);
    width: calc(var(--r)*1.5px);
    transition-property: top, transform; transition-duration: var(--trans-spd); }
  .hex-body {
    top: 50%; left: 50%;
    transform: translate(-50%, 0);
    overflow: hidden;
    width: calc(100% - 80px);
    height: 200px;
    visibility: hidden;
    opacity: 0;
    transition-property: visibility, opacity, top, max-width; transition-duration: var(--trans-spd); }
  .hex-body-content {
    pointer-events: auto;
    overflow: hidden;
    width: calc(var(--r)*var(--popup)*2px - 80px);
  }

  .left-shape-inside {
    pointer-events: none;
    float: left;
    width: 120px; height: 200px;
    shape-outside: polygon(0 0, 0 200px, 120px 200px);
  }

  .right-shape-inside {
    pointer-events: none;
    float: right;
    width: 120px; height: 200px;
    shape-outside: polygon(120px 0, 120px 200px, 0 200px);
  }

  /* clickable */
  polygon, .hex-title { pointer-events: auto; }

  /*@supports (transition-property: opacity) {
    .hex { background-color: red; }
  }*/

</style>
