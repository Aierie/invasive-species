<template>
  <div
    :class="[
      'timeline', 
      {active}
    ]"
    :style="{
      '--layers':layers, 
      '--item-size': `${itemSize}px`, 
      '--h': baseColor[0],
      '--s': `${baseColor[1]}%`,
      '--l': `${baseColor[2]}%`,
    }"
  >
    <template v-for="(item, index) in items">
      <div
        :key="`marker-${index}`"
        :class="{'marker': true, 'hovered-item': hoverIndex === index}"
        :style="translateX(item.marker)"
        @mouseenter="hoverIndex=index"
        @mouseleave="hoverIndex=null"
      ></div>
      <Connector
        :class="{'connector':true, 'hovered-item':hoverIndex === index}"
        :key="`connector-${index}`"
        :start-x="item.marker + itemSize / 2"
        :start-y="itemSize / 2"
        :end-x="item.label.x"
        :end-y="item.label.y + labelHeight / 2"
        :stroke-width="2"
        :color="connectorColor(baseColor, index === hoverIndex)"
      />
      <div
        :key="`tooltip-${index}`"
        :class="{'tooltip': true, 'hovered-item': hoverIndex === index}"
        :style="{ ...translateX(item.label.x), ...bottom(-item.label.y)}"
      >
        <div class="title">
          {{item.data.year}}
          <br />
          {{item.data.title}}
        </div>
        <div class="description">{{item.data.description}}</div>
      </div>
    </template>
    <div
      class="spanned"
      :style="{...translateX(items[0].marker), ...width(items[items.length - 1].marker - items[0].marker) }"
    ></div>
  </div>
</template>

<script>
import labella from "labella";
import Connector from "./Connector";

export default {
  inject: ["measure"],
  components: {
    Connector,
  },
  props: {
    data: {
      type: Array,
      default: () => [],
    },
    minX: {
      type: Number,
      required: true,
    },
    maxX: {
      type: Number,
      required: true,
    },
    minValue: {
      type: Number,
      required: true,
    },
    maxValue: {
      type: Number,
      required: true,
    },
    labelHeight: {
      type: Number,
      required: true,
    },
    itemSize: {
      type: Number,
      required: true,
    },
    active: {
      type: Boolean,
      default: false,
    },
    baseColor: {
      type: Array,
      default: () => [0, 0, 0],
    },
  },
  data() {
    return {
      hoverIndex: null,
    };
  },
  computed: {
    timelineItemPositions() {
      return this.data.map(this.getPos);
    },
    timelineLabelPositions() {
      return this.getLabelPositions(
        this.timelineItemPositions.map(
          (p, i) => new labella.Node(p, this.measure(this.data[i].title))
        )
      );
    },
    items() {
      const res = [];
      for (let i = 0; i < this.data.length; i++) {
        res.push({
          marker: this.timelineItemPositions[i],
          label: this.timelineLabelPositions[i],
          data: this.data[i],
        });
      }
      return res.sort((a, b) => a.marker - b.marker);
    },
    layers() {
      return (
        Math.max(...this.timelineLabelPositions.map((el) => el.layerIndex)) + 1
      );
    },
  },
  methods: {
    connectorColor(baseColor, emphasis) {
      return emphasis
        ? `hsl(${baseColor[0]},${baseColor[1]}%,${baseColor[2] + 30}%)`
        : `hsla(${baseColor[0]},15%,10%, 0.4)`;
    },
    translateX(value) {
      return { transform: `translateX(${value}px)` };
    },
    bottom(value) {
      return { bottom: `${value}px` };
    },
    width(value) {
      return { width: `${value}px` };
    },
    getPos(item) {
      const val = item.year;
      return (
        this.minX +
        ((val - this.minValue) / (this.maxValue - this.minValue)) *
          (this.maxX - this.minX)
      );
    },
    getLabelPositions(nodes) {
      const force = new labella.Force({
        minPos: this.minX,
        maxPos: this.maxX,
        algorithm: "overlap",
      })
        .nodes(nodes)
        .compute();

      // TODO: provide and inject
      const renderer = new labella.Renderer({
        layerGap: this.labelHeight / 3,
        nodeHeight: this.labelHeight,
        direction: "up",
      });

      return renderer.layout(force.nodes());
    },
    log() {
      console.log("event");
    },
  },
};
</script>

<style lang="scss" scoped>
.timeline {
  --item-size: 24px;
  --padding: 4px;
  --layers: 1;
  --label-height: 36px;
  position: relative;
  left: 50px;
  width: 100%;
  height: var(--item-size);
  margin-top: 12px;
  background-color: #eee;

  .connector,
  .tooltip {
    display: none;
    transition: opacity 0.5s;
    opacity: 0;
  }

  .marker,
  .tooltip {
    z-index: 2;
  }

  .connector {
    z-index: 1;
  }

  &.active {
    margin-top: calc(var(--layers) * var(--label-height) + 12px);
    .connector,
    .tooltip {
      display: block;
      opacity: 1;
    }
  }
}

.marker {
  position: absolute;
  width: var(--item-size);
  height: var(--item-size);
  border-radius: 50%;
  background-color: hsla(var(--h), 30%, calc(var(--l) + 30%), 0.6);
  z-index: 1;

  &.hovered-item {
    background-color: hsla(var(--h), var(--s), calc(var(--l) + 30%), 0.8);
  }
}

.tooltip {
  display: block;
  position: absolute;
  overflow: hidden;
  background-color: hsl(var(--h), 30%, calc(var(--l) + 30%));
  color: hsl(var(--h), 5%, 35%);
  border-radius: 4px;

  .title {
    display: block;
    font-weight: bold;
    font-size: 10px;
  }

  .description {
    width: 0px;
    max-width: 0px;
    font-size: 10px;
    height: 0px;
    max-height: 0px;
  }

  &.hovered-item {
    background-color: hsla(var(--h), var(--s), calc(var(--l) + 30%), 0.95);
    color: hsl(var(--h), 20%, 10%);
    z-index: 5;
    .description {
      width: 30ch;
      max-width: 30ch;
      height: min-content;
      max-height: min-content;
    }
  }
}

.spanned {
  position: absolute;
  top: calc(var(--item-size) / 2);
  transform: translateY(-50%);
  display: block;
  height: 2px;
  background-color: hsl(var(--h), calc(var(--s) - 30%), calc(var(--l) + 35%));
  z-index: 0;
}
</style>