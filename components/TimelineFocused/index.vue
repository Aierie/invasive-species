<template>
  <main class="[ main ]">
    <div class="[ content-grid long ]">
      <Timeline
        :timeline-items="timelineItems"
        class="grid-nav"
        @select:animal="selectedAnimalId = $event"
      ></Timeline>
      <Description :animal="selectedAnimal" class="grid-text"></Description>
      <div class="[ image ] [ grid-image ]">
        {{ selectedAnimal.image }}
      </div>
      <Map class="grid-map" region="none"></Map>
    </div>
  </main>
</template>

<script>
import Timeline from './Timeline'
import Description from './Description'
import Map from '../Map'

export default {
  components: {
    Timeline,
    Description,
    Map,
  },
  props: {
    animals: {
      type: Array,
      default: () => []
    },
    years: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      selectedAnimalId: 'brumby'
    }
  },
  computed: {
    timelineItems() {
      const animalsIntroducedByYear = {}
      for (const animal of this.animals) {
        const year = animal.introducedYear
        animalsIntroducedByYear[year] = animalsIntroducedByYear[year] || []
        animalsIntroducedByYear[year].push({
          id: animal.id,
          name: animal.name
        })
      }

      const getDescription = year => {
        const o = this.years.find(el => window.parseInt(el.year) === window.parseInt(year));
        return o ? o.description : "";
      };

      return Object.keys(animalsIntroducedByYear).reduce(
        (prev, year) => [
          ...prev,
          {
            year: window.parseInt(year),
            animals: animalsIntroducedByYear[year],
            description: getDescription(year),
          },
        ],
        []
      )
    },
    selectedAnimal(){
      return this.animals.find((a) => a.id === this.selectedAnimalId) || {};
    }
  }
}
</script>

<style scoped lang="scss">
.main {
  width: 100vw;
  height: 100vh;
  min-width: 1024px;
}

// temp block
.image {
  width: 100%;
  height: 100%;
  background-color: gray;
}

.content-grid {
  width: 100%;
  height: 100%;
  display: grid;
  grid-template-rows: 1fr 1fr;
  grid-template-columns: 280px 1fr 400px;
  grid-template-areas:
    'nav text image'
    'nav text map';
  gap: 24px;

  .grid-nav {
    grid-area: nav;
  }

  .grid-text {
    grid-area: text;
  }

  .grid-image {
    grid-area: image;
  }

  .grid-map {
    grid-area: map;
  }
}
</style>
