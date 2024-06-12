<template>
  <div class="bg-white relative border rounded-lg">

    <div class="flex items-center justify-between">
        <!-- Search bar -->
        <SearchForm @search="handleSearch" />

        <div class="flex items-center justify-end text-sm font-semibold">
            <!-- Radio buttons -->
            <FilterRadios @filter="handleFilterRadios" />

            <!-- List of filters for statuses -->
            <FilterDropdown @filter="handleCheckboxFilter" :items="items" />
        </div>
    </div>


    <table class="w-full text-sm text-left text-gray-500">
      <thead class="text-xs text-gray-700 uppercase bg-gray-50">
        <tr>
          <th class="px-4 py-3">ID</th>
          <th class="px-4 py-3">Assigned To</th>
          <th class="px-4 py-3">Status</th>
          <th class="px-4 py-3">Title</th>
          <th class="px-4 py-3">Due At</th>
          <th class="px-4 py-3">
            <span class="sr-only">Actions</span>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in filteredItems" :key="item.id" class="border-b" v-if="filteredItems.length > 0">
          <td class="px-4 py-3 font-medium text-gray-900">{{ item.id }}</td>
          <td class="px-4 py-3 font-medium text-gray-900">
            {{ item.user.name }}
          </td>
          <td class="px-4 py-3">
            <span :class="statusTextColor(item)">{{ item.status }}</span>
          </td>
          <td class="px-4 py-3">{{ item.title }}</td>
          <td class="px-4 py-3">{{ item.due_at }}</td>
          <td class="px-4 py-3 flex items-center justify-end">
            <a href="#" class="text-indigo-500 hover:underline">Details</a>
          </td>
          
        </tr>
        <tr v-else>
          <td class="px-4 py-3">Sorry, there is no data</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import SearchForm from './SearchForm.vue';
import FilterDropdown from './FilterDropdown.vue';
import FilterRadios from './FilterRadios.vue';

const searchFilter = ref('')
const searchFilterRad = ref('')
const statusFilter = ref([])

const props = defineProps({
  items: Array,
  required: true,
});

const filteredItems = computed(() => {

    let items = props.items;

    switch(searchFilterRad.value) {
    case 'today':
        items = items.filter(item => {
            const itemDate = resetTime(new Date(item.due_at));
            const today = resetTime(new Date());
            return itemDate.getTime() === today.getTime();
        });
        break;
    case 'past':
        items = items.filter(item => {
            const itemDate = resetTime(new Date(item.due_at));
            const today = resetTime(new Date());
            return itemDate.getTime() < today.getTime();
        });
        break;
    // Add other cases if necessary
    }
    if(statusFilter.value.length)
      items = items.filter(item=> statusFilter.value.includes(item.status));

    if(searchFilter.value !== "") {
       items = items.filter(item => 
            item.title.toLowerCase().includes(searchFilter.value) ||
            item.user.name.toLowerCase().includes(searchFilter.value)
        )

        // console.log(items)
    }
    return items
})

const handleSearch = (search) => {
    // console.log(search.toLowerCase())
    searchFilter.value = search.toLowerCase()

    console.log(searchFilter.value)
}

const handleFilterRadios = (filter) => {
    // console.log(filter)
    searchFilterRad.value = filter
}

const handleCheckboxFilter = (filter) => {
   
    if(statusFilter.value.includes(filter)) {
         return statusFilter.value.splice(statusFilter.value.indexOf(filter), 1)
    }
    return statusFilter.value.push(filter)

    
}

// Define a computed property that returns a function to get the status text and background color
const statusTextColor = computed(() => item => {
  const baseClasses = "inline-flex items-center font-medium rounded-md text-xs px-2 py-1 capitalize ring-1 ring-inset ring-opacity-25";
  const darkModeClasses = "dark:bg-opacity-10 dark:ring-opacity-25";

  if (item.status === 'Pending') {
    return `${baseClasses} text-red-600 bg-red-50 dark:bg-red-400 dark:text-red-400 ring-red-600 dark:ring-red-400 ${darkModeClasses}`;
  } else if (item.status === 'In Progress') {
    return `${baseClasses} text-yellow-500 bg-yellow-50 dark:bg-yellow-400 dark:text-yellow-400 ring-yellow-500 dark:ring-yellow-400 ${darkModeClasses}`;
  } else if (item.status === 'Completed') {
    return `${baseClasses} text-green-700 bg-green-50 dark:bg-green-400 dark:text-green-400 ring-green-700 dark:ring-green-400 ${darkModeClasses}`;
  } else {
    return baseClasses;
  }
});

// Date helper
const resetTime = (date) => {
    const newDate = new Date(date);
    newDate.setHours(0, 0, 0, 0);
    return newDate;
};


</script>

