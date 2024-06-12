<template>
    <form class="py-3 px-4 flex items-center">
        <label class="sr-only">Search</label>
        <div class="relative w-full">
            <input @input="search" type="text" placeholder="Search" 
                   class="bg-gray-50 border border-gray-300 text-gray-900 rounded-lg py-2 px-3 focus:outline-none focus:ring-2 focus:ring-gray-300 focus:border-gray-400">
        </div>
    </form>
    
</template>

<script setup>
const emit = defineEmits(['search'])
const search = (e) => {
    emit('search', e.target.value)
}
</script>