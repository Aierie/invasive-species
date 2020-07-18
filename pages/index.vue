<template>
  <main class="[ main ]">
    <div class="[ content-grid long ] [ fixed ]">
      <Timeline
        :timeline-items="timelineItems"
        class="grid-nav"
        @select:animal="selectedAnimalId = $event"
      ></Timeline>
      <Description :animal="selectedAnimal" class="grid-text"></Description>
      <div class="[ image ] [ grid-image ]">
        {{ selectedAnimal.image }}
      </div>
      <Map class="grid-map"></Map>
    </div>
  </main>
</template>

<script>
import Timeline from '~/components/Timeline'
import Description from '~/components/Description'
import Map from '~/components/Map'

export default {
  components: {
    Timeline,
    Description,
    Map,
  },
  async fetch() {
    /* eslint-disable*/
    const [{body: years}, animals] = await Promise.all([
      this.$content('/years').fetch(),
      this.$content('/animals').fetch()
    ])

    this.animals = animals.body;

    const animalsIntroducedByYear = {}
    for (const animal of this.animals) {
      const year = animal.introducedYear
      animalsIntroducedByYear[year] = animalsIntroducedByYear[year] || []
      animalsIntroducedByYear[year].push(animal.id)
    }

    const getDescription = year => {
      const o = years.find(el => parseInt(el.year) === parseInt(year));
      return o ? o.description : "";
    };
    this.timelineItems = Object.keys(animalsIntroducedByYear).reduce(
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
    this.selectedAnimalId = this.animals[0].id;
  },
  data() {
    return {
      animals: [],
      timelineItems: [],
      selectedAnimalId: ''
    }
  },
  computed: {
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
}

.image {
  width: 100%;
  height: 100%;
  background-color: gray;
}

.content-grid {
  width: 100vw;
  height: 100vh;
  display: grid;
  grid-template-rows: 1fr 1fr 1fr;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-areas:
    'nav text text none'
    'nav text text none'
    'nav gap image map';
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

// .fixed {
//   position: fixed;
// }
</style>
