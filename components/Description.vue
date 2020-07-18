<template>
  <div class="[ colored-full ]">
    <h2>
      {{ animal.name }}(<i>{{ animal.species }}</i
      >)
    </h2>
    <p>Threat level: {{ animal.threatLevel }}</p>
    <p>From: {{ animal.introducedFrom }}</p>
    <p>Year: {{ animal.introducedYear }}</p>
    <p>Reason: {{ animal.introducedReason }}</p>
  </div>
</template>

<script>
export default {
  props: {
    animal: {
      type: Object,
      default: () => ({}),
    },
  },
}
</script>

<style scoped>
.colored-full {
  width: 100%;
  height: 100%;
  background-color: cyan;
}
</style>
