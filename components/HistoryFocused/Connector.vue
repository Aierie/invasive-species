<template>
    <svg :view-box.camel="viewBox" :style="style">
      <line 
         x1="0"
         y1="0"
         :x2="toPoint.x"
         :y2="toPoint.y"
         :stroke="color"
         :stroke-width="strokeWidth"
      ></line>
    </svg>
</template>

<script>
export default {
    props: {
    startX: {
      type: Number,
      required: true
    },
    startY: {
      type: Number,
      required: true
    },
    endX: {
      type: Number,
      required: true
    },
    endY: {
      type: Number,
      required: true
    },
    color: {
      type: String,
      default: "black"
    },
    strokeWidth: {
      type: Number,
      default: 1
    }
  },
  computed: {
    width() {
      return Math.max(Math.abs(this.startX - this.endX), 2);
    },
    height() {
      return Math.max(Math.abs(this.startY - this.endY), 2);
    },
    style() {
      return {
        width: `${this.width * 2}px`,
        height: `${this.height * 2}px`,
        transform: "translate(-50%, -50%)",
        pointerEvents: "none",
        position: "absolute",
        overflow: "visible",
        top: this.startY,
        left: this.startX
      };
    },
    viewBox() {
      return `${-this.width} ${-this.height} ${this.width * 2} ${
        this.height * 2
      }`;
    },
    toPoint() {
      return {
        x: this.endX - this.startX,
        y: this.endY - this.startY
      };
    }
  }
}
</script>