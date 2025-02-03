<template>
  <div id="app" class="bg-gray-100 min-h-screen">
    <div class="container mx-auto px-4 py-6">
      <EmployeeTable />
    </div>
  </div>
</template>

<script>
import EmployeeTable from './components/EmployeeTable.vue';

export default {
  components: {
    EmployeeTable,
  },
};
</script>