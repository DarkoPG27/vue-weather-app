<script setup>
import AsyncCityView from "@/components/AsyncCityView.vue";
import CityViewSkeleton from "@/components/CityViewSkeleton.vue";
</script>

<template>
  <div>
    <Suspense>
      <!-- component with nested async dependencies -->
      <AsyncCityView />
      <!-- loading state via #fallback slot -->
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>
