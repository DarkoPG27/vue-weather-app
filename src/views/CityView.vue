<script setup>
import AsyncCityView from "@/components/AsyncCityView.vue";
</script>

<template>
  <div>
    <Suspense>
      <!-- component with nested async dependencies -->
      <AsyncCityView />
      <!-- loading state via #fallback slot -->
      <template #fallback>
        <p>Loading...</p>
      </template>
    </Suspense>
  </div>
</template>
