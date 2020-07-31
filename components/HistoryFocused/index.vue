<template>
  <div class="container" style="margin-top: 160px;">
    <Timeline
      v-for="(items, animal, index) in itemsByAnimal"
      :key="animal"
      :data="items"
      :min-x="0"
      :max-x="800"
      :min-value="1750"
      :max-value="2000"
      :label-height="24"
      :item-size="16"
      :base-color="colors[index]"
      :active="true"
    />
  </div>
</template>

<script>
import Timeline from "./Timeline";
export default {
  provide: {
    measure(text){
      let canvas = document.getElementById("measure")
      if (!canvas) {
        canvas = document.createElement('canvas');
        canvas.setAttribute("id", "measure");
        document.body.appendChild(canvas);
      }
      const font = "10px monospace";
      const ctx = canvas.getContext("2d");
      ctx.font = font;
      console.log(ctx.font);
      return ctx.measureText(text).width;
    }
  },
  components: {
    Timeline,
  },
  props: {
    animals: {
      type: Array,
      default: () => [],
    },
    events: {
      type: Array,
      default: () => [],
    },
  },
  async mounted() {
    console.log(await this.$content("/events").fetch());
  },
  data() {
    return {
      selectedAnimalId: "brumby",
      items: [
        {
          title: "t1",
          description:
            "Lorem lorem Lorem lorem Lorem lorem Lorem lorem Lorem lorem",
          year: 150,
        },
        {
          title: "t2",
          description:
            "Lorem lorem Lorem lorem Lorem lorem Lorem lorem Lorem lorem",
          year: 170,
        },
      ],
      items2: [
        {
          title: "t1",
          description:
            "Lorem lorem Lorem lorem Lorem lorem Lorem lorem Lorem lorem",
          year: 150,
        },
        {
          title: "t2",
          description:
            "Lorem lorem Lorem lorem Lorem lorem Lorem lorem Lorem lorem",
          year: 170,
        },
      ],
      // hsl colors [h, s, l]
      colors: [
        [84, 96.3, 21],
        [272, 87.3, 43.1],
        [25, 80.8, 32.7],
        [234, 79.4, 60],
        [1, 64.9, 48],
        [187, 98.3, 23.3],
        [330, 70.3, 35.7],
        [219, 49.2, 47.8],
        [47, 75.3, 15.9],
        [225, 100, 27.3],
        [303, 37.7, 20.8],
      ],
    };
  },
  computed: {
    itemsByAnimal(){
      const byAnimal = {}
      for (let item of this.events) {
        byAnimal[item.animal] = byAnimal[item.animal] || [];
        byAnimal[item.animal].push(item);
      }

      return byAnimal
    }
  }
};
</script>

<style>
.container {
  font-family: monospace;
}
</style>