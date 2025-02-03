<template>
  <div class="container mx-auto px-4 py-6">
    <h1 class="text-3xl font-bold mb-6">员工管理</h1>

    <!-- 搜索框 -->
    <div class="mb-6">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="搜索员工..."
        class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:shadow-outline"
      />
    </div>

    <!-- 添加员工表单 -->
    <div class="mb-6">
      <button
        @click="showAddForm = !showAddForm"
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        {{ showAddForm ? '隐藏添加表单' : '添加员工' }}
      </button>
    </div>

    <div v-if="showAddForm" class="mb-6 bg-white shadow-md rounded-lg p-6">
      <form @submit.prevent="addEmployee">
        <div class="mb-4">
          <label for="name" class="block text-gray-700 font-bold mb-2">姓名</label>
          <input
            type="text"
            id="name"
            v-model="newEmployee.name"
            class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:shadow-outline"
          />
        </div>
        <div class="mb-4">
          <label for="gender" class="block text-gray-700 font-bold mb-2">性别</label>
          <select
            id="gender"
            v-model="newEmployee.gender"
            class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:shadow-outline"
          >
            <option value="男">男</option>
            <option value="女">女</option>
          </select>
        </div>
        <div class="mb-4">
          <label for="age" class="block text-gray-700 font-bold mb-2">年龄</label>
          <input
            type="number"
            id="age"
            v-model="newEmployee.age"
            class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:shadow-outline"
          />
        </div>
        <div class="mb-4">
          <label for="hireDate" class="block text-gray-700 font-bold mb-2">入职日期</label>
          <input
            type="date"
            id="hireDate"
            v-model="newEmployee.hireDate"
            class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:shadow-outline"
          />
        </div>
        <div class="mb-4">
          <label for="contact" class="block text-gray-700 font-bold mb-2">联系方式</label>
          <input
            type="text"
            id="contact"
            v-model="newEmployee.contact"
            class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:shadow-outline"
          />
        </div>
        <div class="mb-4">
          <label for="address" class="block text-gray-700 font-bold mb-2">家庭住址</label>
          <input
            type="text"
            id="address"
            v-model="newEmployee.address"
            class="w-full px-4 py-2 border rounded-lg focus:outline-none focus:shadow-outline"
          />
        </div>
        <button
          type="submit"
          class="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600"
        >
          添加员工
        </button>
      </form>
    </div>

    <!-- 员工表格 -->
    <div class="overflow-x-auto">
      <table class="min-w-full divide-y divide-gray-200">
        <thead>
          <tr>
            <th
              v-for="column in columns"
              :key="column"
              class="px-6 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
            >
              {{ column }}
            </th>
            <th class="px-6 py-3 bg-gray-50">操作</th>
          </tr>
        </thead>
        <tbody class="bg-white divide-y divide-gray-200">
          <tr v-for="employee in paginatedEmployees" :key="employee.id">
            <td class="px-6 py-4 whitespace-nowrap">{{ employee.name }}</td>
            <td class="px-6 py-4 whitespace-nowrap">{{ employee.gender }}</td>
            <td class="px-6 py-4 whitespace-nowrap">{{ employee.age }}</td>
            <td class="px-6 py-4 whitespace-nowrap">{{ employee.hireDate }}</td>
            <td class="px-6 py-4 whitespace-nowrap">{{ employee.contact }}</td>
            <td class="px-6 py-4 whitespace-nowrap">{{ employee.address }}</td>
            <td class="px-6 py-4 whitespace-nowrap">
              <button
                @click="editEmployee(employee)"
                class="text-blue-500 hover:text-blue-700 mr-2"
              >
                编辑
              </button>
              <button
                @click="deleteEmployee(employee.id)"
                class="text-red-500 hover:text-red-700"
              >
                删除
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- 分页 -->
    <div class="flex items-center justify-between mt-6">
      <button
        @click="prevPage"
        :disabled="currentPage === 1"
        class="px-4 py-2 bg-gray-200 hover:bg-gray-300 rounded-md"
      >
        上一页
      </button>
      <span class="text-gray-600">
        第 {{ currentPage }} 页 / 共 {{ totalPages }} 页
      </span>
      <button
        @click="nextPage"
        :disabled="currentPage === totalPages"
        class="px-4 py-2 bg-gray-200 hover:bg-gray-300 rounded-md"
      >
        下一页
      </button>
    </div>
  </div>
</template>

<script>
import employees from '../data/employees.json';

export default {
  data() {
    return {
      employees,
      columns: ['姓名', '性别', '年龄', '入职日期', '联系方式', '家庭住址'],
      searchQuery: '',
      currentPage: 1,
      itemsPerPage: 5,
      showAddForm: false,
      newEmployee: {
        name: '',
        gender: '男',
        age: '',
        hireDate: '',
        contact: '',
        address: '',
      },
    };
  },
  computed: {
    filteredEmployees() {
      if (!this.searchQuery) return this.employees;
      return this.employees.filter(employee =>
        employee.name.includes(this.searchQuery)
      );
    },
    paginatedEmployees() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.filteredEmployees.slice(startIndex, endIndex);
    },
    totalPages() {
      return Math.ceil(this.filteredEmployees.length / this.itemsPerPage);
    },
  },
  methods: {
    addEmployee() {
      if (!this.newEmployee.name || !this.newEmployee.contact || !this.newEmployee.address) {
        alert('请填写完整信息');
        return;
      }
      const newId = this.employees.length + 1;
      this.employees.push({
        id: newId,
        ...this.newEmployee,
      });
      this.newEmployee = {
        name: '',
        gender: '男',
        age: '',
        hireDate: '',
        contact: '',
        address: '',
      };
      this.showAddForm = false;
    },
    editEmployee(employee) {
      alert(`编辑：${employee.name}`);
      // 这里可以实现编辑逻辑
    },
    deleteEmployee(id) {
      this.employees = this.employees.filter(employee => employee.id !== id);
      alert('删除成功');
    },
    prevPage() {
      if (this.currentPage > 1) this.currentPage--;
    },
    nextPage() {
      if (this.currentPage < this.totalPages) this.currentPage++;
    },
  },
};
</script>