<template>

  <div :id="id" :class="{'hex': true, 'selected': selected, 'foreground': foreground, 'midground': midground}" :style="{left: left_pos, top: top_pos, '--animation-speed-s': animation_speed_s, '--radius': radius, '--title-popup': title_popup, '--title-popup-scale': 'scale('+title_popup+')', '--popup': popup, '--popup-scale': popup_scale}">

    <svg :style="{width: radius*2+'px', height: radius*2+'px'}">
      <polygon :points="hex_coords" :style="{fill:fill, stroke:stroke, 'stroke-width': stroke_width}" v-on:click="toggle_selected" />
      Sorry, your browser does not support inline SVG.
    </svg>

    <div class="hex-title" v-on:click="toggle_selected">
      <slot name="hex-title">Default Title</slot>
    </div>

    <div class="hex-body">
      <slot name="hex-body">Default Body</slot>
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
    popup_scale: function () { return this.$parent.popup_scale; },
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
    popup: function () {
      return 'scale(' + this.popup_scale + ')';
    }
  },
  methods: {
    toggle_selected: function () {
      if (this.selected) {
        this.foreground = false;
        this.midground = true;
        setTimeout(() => this.midground = false, this.animation_speed_ms); // change z-index only after movement is done
      } else {
        this.$parent.unselect_all();
        this.foreground = true;
      }
      this.selected = !this.selected;
    },
    unselect: function () {
      this.foreground = false;
      this.midground = true;
      setTimeout(() => this.midground = false, this.animation_speed_ms);
      this.selected = false;
    },
    is_selected: function () {
      return this.selected;
    }
  }
}
</script>

<style scoped>

  .hex, .hex > div, .hex > svg { position: absolute; }

  .hex {
    width: calc(var(--radius)*2px); height: calc(var(--radius)*2px);
    transition-property: left, top, transform, height; transition-duration: var(--animation-speed-s); }

  .hex > svg {
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    transition: transform var(--animation-speed-s); }

  div.hex:not(.selected) { opacity: var(--unselected-opacity); }

  .selected {
    transform: translate(-50%, -50%);
    height: calc(var(--radius)*var(--title-popup)*2px);
    opacity: 1.0;  }

  .selected > svg { transform: translate(-50%, -50%) var(--popup); }

  .selected .hex-title {
    top: 15%;
    transform: translate(-50%, 0);
    width: 100%; }
  .selected .hex-body {
    top: 50%;
    width: calc(var(--radius)*var(--popup-scale)*1px);
    max-height: 190px;
    visibility: visible;
    opacity: 1; }
  .midground, .midground .hex-title, .midground .hex-body { z-index: 2; }
  .foreground, .foreground .hex-title, .foreground .hex-body { z-index: 3; }

  .hex-title {
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    width: 75%;
    transition-property: top, transform, width, font-size; transition-duration: var(--animation-speed-s); }
  .hex-body {
    top: 0; left: 50%;
    transform: translate(-50%);
    overflow: scroll;
    width: calc(var(--radius)*1px);
    max-height: 100px;
    visibility: hidden;
    opacity: 0;
    transition-property: visibility, opacity, top, max-width, max-height; transition-duration: var(--animation-speed-s); }

  /* clickable */
  polygon, .hex-title { cursor: pointer; }

</style>
