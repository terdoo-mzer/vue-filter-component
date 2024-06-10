<template>
  <div class="p-8 bg-gray-100 min-h-screen">
      <DataTable :items="items" />
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import DataTable from './components/DataTable.vue';


const items = ref([])

onMounted(async () => {
  const response = await fetch('http://localhost:5000/tasks')
  items.value = await response.json()
})
</script>

<style scoped>

</style>