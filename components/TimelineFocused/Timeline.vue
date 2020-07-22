<template>
  <div class="[ timeline ] [ padded ]">
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
                :key="animal.id"
                @click="$emit('select:animal', animal.id)"
              >
                {{ animal.name }}
              </div>
            </div>
          </div>
        </div>
        <div class="content">
          {{ timelineItem.description }}
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
  overflow-y: auto;
  overflow-x: visible;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

.padded {
  padding: 16px;
  padding-top: calc(16px + 0.75rem);
}

::-webkit-scrollbar {
  width: 0px;
  background: transparent; /* make scrollbar transparent */
}

.overview {
  position: relative;
  /* the line of the timeline */
  border-left: 1px dashed black;
  padding-bottom: 12px;

  > * {
    transform: translateY(-1.5rem);
  }

  /* the marker on the timeline */
  .header {
    width: 100%;
    height: 3rem;
    position: relative;
    display: flex;
    align-items: baseline;

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
      height: 100%;
      display: flex;
      align-items: center;
      margin-left: 2rem;
    }

    .year {
      width: 5ch;
    }

    .animals {
      display: flex;
      margin-left: 12px;
    }

    .animal {
      width: 3rem;
      height: 3rem;
      border-radius: 50%;
      background-color: black;
      color: transparent;
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
    margin-left: 2rem;
    font-size: 0.75rem;
  }
}
</style>
