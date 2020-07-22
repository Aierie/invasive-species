<template>
  <TimelineFocusedLayout
    :animals="animals"
    :years="years"
  />
</template>

<script>
import TimelineFocusedLayout from '~/components/TimelineFocused'

export default {
  components: {
    TimelineFocusedLayout
  },
  async asyncData(ctx) {
    const [{ body: years }, { body: animals }] = await Promise.all([
      ctx.app.$content('/years').fetch(),
      ctx.app.$content('/animals').fetch()
    ])

    return {
      animals,
      years
    }
  }
}
</script>

<style scoped lang="scss">
</style>
