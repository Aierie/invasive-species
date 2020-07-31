<template>
<div>
  <select class="[ fixed top-right ]" v-model="currentComponent">
    <option v-for="c in components" :key="c">{{c}}</option>
  </select>
  <component
    :is="currentComponent"
    :animals="animals"
    :years="years"
    :events="events"
  />
</div>
</template>

<script>
import TimelineFocusedLayout from '~/components/TimelineFocused';
import HistoryFocusedLayout from '~/components/HistoryFocused';

export default {
  components: {
    TimelineFocusedLayout,
    HistoryFocusedLayout
  },
  async asyncData(ctx) {
    const [{ body: years }, { body: animals }, { body: _events }] = await Promise.all([
      ctx.app.$content('/years').fetch(),
      ctx.app.$content('/animals').fetch(),
      ctx.app.$content('/events').fetch()
    ])

    const events = _events.map(o => ({...o, year:parseInt(o.year)}));

    return {
      animals,
      years,
      events
    }
  },
  data(){
    return {
      currentComponent: 'HistoryFocusedLayout',
      components: [
        "TimelineFocusedLayout",
        "HistoryFocusedLayout"
      ]
    }
  }
}
</script>

<style scoped lang="scss">
.fixed {
  position: fixed;
}

.top-right {
  top: 0;
  right: 0;
}
</style>
