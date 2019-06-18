<template>

  <div :id="id" ref="hex" :class="{'hex': true, 'selected': selected, 'foreground': foreground, 'midground': midground}" :style="{left: left_pos, top: top_pos, '--trans-spd': animation_speed_s, '--r': radius, '--title-popup-scale': 'scale('+title_popup+')', '--popup': popup, '--popup-scale': popup_scale}">

    <svg :style="{width: radius*2+'px', height: radius*2+'px'}">
      <polygon :points="hex_coords" :style="{fill:fill, stroke:stroke, 'stroke-width': stroke_width}" @click="select" />
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
    fill_color: String,
    stroke_color: String
  },
  data() {
    return {
      default_col: 0,
      default_row: 0,
      selected: false,
      midground: false,
      foreground: false
    }
  },
  computed: {
    get_col: function () { return (this.has_pos) ? this.col : this.default_col; },
    get_row: function () { return (this.has_pos) ? this.row : this.default_row; },
    radius: function () { return this.$parent.hex_radius; },
    popup: function () { return this.$parent.popup_scale; },
    title_popup: function () { return this.$parent.title_popup; },
    gutter: function () { return this.$parent.gutter; },
    stroke_width: function () { return this.$parent.stroke_width; },
    animation_speed_ms: function () { return this.$parent.animation_speed; },
    has_pos: function () { return (this.col != undefined && this.row != undefined); },
    fill: function () { return (this.fill_color == undefined) ? this.$parent.fill_color : this.fill_color; },
    stroke: function () { return (this.stroke_color == undefined) ? this.$parent.stroke_color : this.stroke_color; },
    top_pos: function () {
      var r = this.radius;
      var h = Math.sqrt(r*r-(r/2)*(r/2));
      var odd_column_gutter_displacement = (this.get_col%2)*this.gutter/2;
      if (this.selected)
        return '50%';
      else
        return this.get_col%2*h + (2*h)*this.get_row + odd_column_gutter_displacement + this.get_row*this.gutter + "px";
    },
    left_pos: function () {
      if (this.selected)
        return '50%';
      else
        return this.get_col*this.radius*1.5 + this.get_col*this.gutter*Math.cos(Math.PI/6) + "px";
    },
    animation_speed_s: function () {
      return parseFloat(this.animation_speed_ms)/1000 + "s";
    },
    hex_coords: function () {
      var r = this.radius;
      var h = Math.sqrt(r*r-(r/2)*(r/2));
      var s = Math.round(Math.sqrt(2)*this.stroke_width/2);

      var TL = (Math.round(r/2)+s) + ',' + (Math.round(r-h)+s);
      var TR = (Math.round(3*r/2)-s) + ',' + (Math.round(r-h)+s);
      var R = (2*r-s) + ',' + r;
      var BR = (Math.round(3*r/2)-s) + ',' + (Math.round(r+h)-s);
      var BL = (Math.round(r/2)+s) + ',' + (Math.round(r+h)-s);
      var L = s + ',' + r;

      return TL+' '+TR+' '+R+' '+BR+' '+BL+' '+L;
    },
    popup_scale: function () {
      return 'scale(' + this.popup + ')';
    }
  },
  methods: {
    toggle_selected: function () {
      if (this.selected) {
        this.unselect();
      } else {
        this.select();
      }
    },
    select: function () {
      this.$parent.unselect_all();
      this.$parent.one_is_selected = true;
      this.foreground = true;
      this.selected = true;
    },
    unselect: function () {
      if (this.selected)
        this.$parent.one_is_selected = false;
      this.foreground = false;
      this.midground = true;
      setTimeout(() => this.midground = false, this.animation_speed_ms);
      this.selected = false;
    },
    is_selected: function () {
      return this.selected;
    }
  },
  mounted () {
    this.$refs.hex.addEventListener("click", function(e) { e.stopPropagation(); });
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
    transform: translate(-50%);
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

</style>
