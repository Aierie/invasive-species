<template>
  <div class="timeline">
    <div
      v-for="(timelineItem, index) in timelineItems"
      :key="index"
      class="overview"
    >
      <div class="header">
        <div class="summary-images">
          <div class="year">
            {{ timelineItem.year }}
          </div>
          <div class="animals">
            <div
              v-for="animal in timelineItem.animals"
              :key="animal"
              @click="$emit('select:animal', animal)"
            >
              {{ animal }}
            </div>
          </div>
        </div>
      </div>
      <div class="content">
        <div class="description">
          {{ timelineItem.description }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    timelineItems: {
      type: Array,
      default: () => [],
    },
    selectedAnimal: {
      type: String,
      default: null,
    },
  },
  computed: {
    selectedYear() {
      return 'lol'
    },
  },
}
</script>

<style scoped lang="scss">
.timeline {
  max-width: 260px;
  margin: 20px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

.overview {
  display: flex;
  position: relative;
  flex-direction: column;
  padding-bottom: 16px;
  /* the line of the timeline */
  border-left: 1px dashed black;

  /* the marker on the timeline */
  .header {
    width: 100%;
    position: relative;
    display: flex;
    align-items: baseline;
    transform: translateY(-0.5rem);

    &:before {
      content: '';
      display: block;
      position: absolute;
      top: 50%;
      transform: translate(-52%, -50%);
      width: 1rem;
      height: 1rem;
      border-radius: 50%;
      background: #aaa;
      transition: 0.2s;
    }

    .summary-images {
      display: flex;
      align-items: baseline;
      margin-left: 2rem;
    }

    .year {
      width: 5ch;
    }

    .animals {
      display: flex;
      margin-left: 12px;
    }

    .animals > * + * {
      margin-left: 10px;
    }
  }

  &.active .header:before {
    background: #8a8;
  }

  &:last-child {
    border: none;
  }

  .content {
    display: flex;
    flex-direction: column;
    margin-left: 2rem;
    transform: translateY(-0.5rem);
  }

  .description {
    margin-top: 0.5rem;
    font-size: 0.75rem;
  }

  // &:not(.active) .description {
  //   max-height: 0px;
  //   overflow: hidden;
  //   transition: 1s;
  // }
}
</style>
