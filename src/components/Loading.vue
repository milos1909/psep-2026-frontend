<script setup lang="ts">

</script>

<template>
  <div class="card loading-card text-center">
    <div class="card-body">
      <div class="spinner-border" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
      <h3>Loading data</h3>
      <p>Please wait while data is getting loaded!</p>
    </div>
  </div>
</template>

<style scoped>
  .loading-card {
    width: 100%;
    max-width: 400px;
    margin: 0 auto;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
</style>