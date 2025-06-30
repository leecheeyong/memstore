<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-50 to-blue-50">
    <!-- Header -->
    <header class="bg-white/80 backdrop-blur-md shadow-sm border-b border-gray-200/50 sticky top-0 z-40">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center py-6">
          <div class="flex items-center">
            <div class="p-2 bg-gradient-to-r from-blue-600 to-purple-600 rounded-xl mr-3">
              <i class='bx bx-chip text-2xl text-white'></i>
            </div>
            <div>
              <h1 class="text-2xl font-bold bg-gradient-to-r from-gray-900 to-gray-600 bg-clip-text text-transparent">
                MemStore
              </h1>
              <p class="text-sm text-gray-500">Manage your electronic components</p>
            </div>
          </div>
          <button
            @click="openAddModal"
            class="bg-gradient-to-r from-blue-600 to-blue-700 hover:from-blue-700 hover:to-blue-800 text-white px-6 py-3 rounded-xl flex items-center transition-all duration-200 shadow-lg hover:shadow-xl transform hover:-translate-y-0.5"
          >
            <i class='bx bx-plus mr-2 text-lg'></i>
            Add Component
          </button>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <!-- Stats Cards -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
        <div class="bg-white/70 backdrop-blur-sm rounded-2xl shadow-lg p-6 border border-white/20 hover:shadow-xl transition-all duration-300">
          <div class="flex items-center">
            <div class="p-4 bg-gradient-to-r from-blue-500 to-blue-600 rounded-2xl shadow-lg">
              <i class='bx bx-package text-2xl text-white'></i>
            </div>
            <div class="ml-4">
              <p class="text-sm font-medium text-gray-600">Total Components</p>
              <p class="text-3xl font-bold text-gray-900">{{ components.length }}</p>
            </div>
          </div>
        </div>
        <div class="bg-white/70 backdrop-blur-sm rounded-2xl shadow-lg p-6 border border-white/20 hover:shadow-xl transition-all duration-300">
          <div class="flex items-center">
            <div class="p-4 bg-gradient-to-r from-emerald-500 to-emerald-600 rounded-2xl shadow-lg">
              <i class='bx bx-bar-chart-alt-2 text-2xl text-white'></i>
            </div>
            <div class="ml-4">
              <p class="text-sm font-medium text-gray-600">Total Quantity</p>
              <p class="text-3xl font-bold text-gray-900">{{ totalQuantity }}</p>
            </div>
          </div>
        </div>
        <div class="bg-white/70 backdrop-blur-sm rounded-2xl shadow-lg p-6 border border-white/20 hover:shadow-xl transition-all duration-300">
          <div class="flex items-center">
            <div class="p-4 bg-gradient-to-r from-purple-500 to-purple-600 rounded-2xl shadow-lg">
              <i class='bx bx-dollar text-2xl text-white'></i>
            </div>
            <div class="ml-4">
              <p class="text-sm font-medium text-gray-600">Total Value</p>
              <p class="text-3xl font-bold text-gray-900">${{ totalValue.toFixed(2) }}</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Search and Filter -->
      <div class="bg-white/70 backdrop-blur-sm rounded-2xl shadow-lg p-6 mb-8 border border-white/20">
        <div class="flex flex-col sm:flex-row gap-4">
          <div class="flex-1">
            <div class="relative">
              <i class='bx bx-search absolute left-4 top-1/2 transform -translate-y-1/2 text-gray-400 text-lg'></i>
              <input
                v-model="searchQuery"
                type="text"
                placeholder="Search components..."
                class="w-full pl-12 pr-4 py-3 bg-white/50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200 placeholder-gray-400"
              >
            </div>
          </div>
          <select
            v-model="selectedCategory"
            class="px-4 py-3 bg-white/50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200"
          >
            <option value="">All Categories</option>
            <option v-for="category in categories" :key="category" :value="category">{{ category }}</option>
          </select>
        </div>
      </div>

      <!-- Components Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        <div
          v-for="component in filteredComponents"
          :key="component.id"
          class="group bg-white/70 backdrop-blur-sm rounded-2xl shadow-lg border border-white/20 overflow-hidden hover:shadow-2xl hover:-translate-y-1 transition-all duration-300"
        >
          <!-- Component Image -->
          <div class="h-48 bg-gradient-to-br from-gray-100 to-gray-200 flex items-center justify-center relative overflow-hidden">
            <img
              v-if="component.photo"
              :src="component.photo"
              :alt="component.name"
              class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
            >
            <div v-else class="flex flex-col items-center justify-center text-gray-400">
              <i class='bx bx-chip text-6xl mb-2'></i>
              <span class="text-sm font-medium">No Image</span>
            </div>
            
            <!-- Quantity Badge -->
            <div class="absolute top-3 right-3 bg-black/20 backdrop-blur-sm text-white px-2 py-1 rounded-lg text-xs font-medium">
              {{ component.quantity }} units
            </div>
          </div>

          <!-- Component Info -->
          <div class="p-6">
            <div class="flex justify-between items-start mb-3">
              <h3 class="text-lg font-bold text-gray-900 truncate pr-2">{{ component.name }}</h3>
              <div class="flex space-x-1 opacity-0 group-hover:opacity-100 transition-opacity duration-200">
                <button
                  @click="editComponent(component)"
                  class="p-2 text-gray-400 hover:text-blue-600 hover:bg-blue-50 rounded-lg transition-all duration-200"
                >
                  <i class='bx bx-edit text-lg'></i>
                </button>
                <button
                  @click="deleteComponent(component.id)"
                  class="p-2 text-gray-400 hover:text-red-600 hover:bg-red-50 rounded-lg transition-all duration-200"
                >
                  <i class='bx bx-trash text-lg'></i>
                </button>
              </div>
            </div>
            
            <div class="space-y-3">
              <div class="flex items-center text-sm text-gray-600">
                <div class="w-8 h-8 bg-blue-100 rounded-lg flex items-center justify-center mr-3">
                  <i class='bx bx-category text-blue-600'></i>
                </div>
                <span class="font-medium">{{ component.category }}</span>
              </div>
              <div class="flex items-center text-sm text-gray-600">
                <div class="w-8 h-8 bg-green-100 rounded-lg flex items-center justify-center mr-3">
                  <i class='bx bx-dollar text-green-600'></i>
                </div>
                <span class="font-medium">${{ component.cost.toFixed(2) }}</span>
              </div>
              <div class="flex items-center text-sm text-gray-600">
                <div class="w-8 h-8 bg-purple-100 rounded-lg flex items-center justify-center mr-3">
                  <i class='bx bx-map text-purple-600'></i>
                </div>
                <span class="font-medium">{{ component.location }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Empty State -->
      <div v-if="filteredComponents.length === 0" class="text-center py-16">
        <div class="w-24 h-24 bg-gradient-to-r from-gray-200 to-gray-300 rounded-full flex items-center justify-center mx-auto mb-6">
          <i class='bx bx-package text-4xl text-gray-400'></i>
        </div>
        <h3 class="text-xl font-bold text-gray-900 mb-2">No components found</h3>
        <p class="text-gray-500 mb-6 max-w-md mx-auto">
          {{ searchQuery || selectedCategory ? 'Try adjusting your search or filter criteria.' : 'Get started by adding your first electronic component to your inventory.' }}
        </p>
        <button
          v-if="!searchQuery && !selectedCategory"
          @click="openAddModal"
          class="bg-gradient-to-r from-blue-600 to-blue-700 hover:from-blue-700 hover:to-blue-800 text-white px-6 py-3 rounded-xl inline-flex items-center transition-all duration-200 shadow-lg hover:shadow-xl transform hover:-translate-y-0.5"
        >
          <i class='bx bx-plus mr-2'></i>
          Add Your First Component
        </button>
      </div>
    </main>

    <!-- Modal -->
    <Teleport to="body">
      <div v-if="showModal" class="fixed inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center p-4 z-50">
        <div class="bg-white rounded-2xl shadow-2xl max-w-md w-full max-h-[90vh] overflow-y-auto border border-gray-200">
          <div class="p-6">
            <div class="flex justify-between items-center mb-6">
              <div>
                <h2 class="text-2xl font-bold text-gray-900">
                  {{ editingComponent ? 'Edit Component' : 'Add New Component' }}
                </h2>
                <p class="text-sm text-gray-500 mt-1">
                  {{ editingComponent ? 'Update component information' : 'Fill in the component details' }}
                </p>
              </div>
              <button
                @click="closeModal"
                class="p-2 text-gray-400 hover:text-gray-600 hover:bg-gray-100 rounded-lg transition-all duration-200"
              >
                <i class='bx bx-x text-2xl'></i>
              </button>
            </div>

            <form @submit.prevent="saveComponent" class="space-y-5">
              <div>
                <label class="block text-sm font-semibold text-gray-700 mb-2">Component Name</label>
                <input
                  v-model="form.name"
                  type="text"
                  required
                  class="w-full px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200"
                  placeholder="e.g., Arduino Uno R3"
                >
              </div>

              <div>
                <label class="block text-sm font-semibold text-gray-700 mb-2">Photo URL</label>
                <input
                  v-model="form.photo"
                  type="url"
                  class="w-full px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200"
                  placeholder="https://example.com/image.jpg"
                >
                <p class="text-xs text-gray-500 mt-1">Optional: Add a photo URL for visual reference</p>
              </div>

              <div>
                <label class="block text-sm font-semibold text-gray-700 mb-2">Category</label>
                <select
                  v-model="form.category"
                  required
                  class="w-full px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200"
                >
                  <option value="">Select a category</option>
                  <option v-for="category in categories" :key="category" :value="category">{{ category }}</option>
                </select>
              </div>

              <div class="grid grid-cols-2 gap-4">
                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">Quantity</label>
                  <input
                    v-model.number="form.quantity"
                    type="number"
                    min="0"
                    required
                    class="w-full px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200"
                    placeholder="0"
                  >
                </div>

                <div>
                  <label class="block text-sm font-semibold text-gray-700 mb-2">Cost ($)</label>
                  <input
                    v-model.number="form.cost"
                    type="number"
                    min="0"
                    step="0.01"
                    required
                    class="w-full px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200"
                    placeholder="0.00"
                  >
                </div>
              </div>

              <div>
                <label class="block text-sm font-semibold text-gray-700 mb-2">Storage Location</label>
                <input
                  v-model="form.location"
                  type="text"
                  required
                  class="w-full px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-transparent transition-all duration-200"
                  placeholder="e.g., Drawer A1, Shelf B2, Box R1"
                >
              </div>

              <div class="flex space-x-3 pt-6">
                <button
                  type="submit"
                  class="flex-1 bg-gradient-to-r from-blue-600 to-blue-700 hover:from-blue-700 hover:to-blue-800 text-white py-3 px-4 rounded-xl font-semibold transition-all duration-200 shadow-lg hover:shadow-xl"
                >
                  {{ editingComponent ? 'Update Component' : 'Add Component' }}
                </button>
                <button
                  type="button"
                  @click="closeModal"
                  class="flex-1 bg-gray-100 hover:bg-gray-200 text-gray-700 py-3 px-4 rounded-xl font-semibold transition-all duration-200"
                >
                  Cancel
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </Teleport>
  </div>
</template>

<script setup>
import { ref, reactive, computed } from 'vue'

const components = ref([

])

const categories = ref([
  'Microcontrollers',
  'Resistors',
  'Capacitors',
  'LEDs',
  'Sensors',
  'ICs',
  'Transistors',
  'Diodes',
  'Connectors',
  'Modules',
  'Tools',
  'Other'
])

const showModal = ref(false)
const editingComponent = ref(null)
const searchQuery = ref('')
const selectedCategory = ref('')

const form = reactive({
  name: '',
  photo: '',
  category: '',
  quantity: 0,
  cost: 0,
  location: ''
})

const filteredComponents = computed(() => {
  return components.value.filter(component => {
    const matchesSearch = component.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
                        component.category.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
                        component.location.toLowerCase().includes(searchQuery.value.toLowerCase())
    const matchesCategory = !selectedCategory.value || component.category === selectedCategory.value
    return matchesSearch && matchesCategory
  })
})

const totalQuantity = computed(() => {
  return components.value.reduce((total, component) => total + component.quantity, 0)
})

const totalValue = computed(() => {
  return components.value.reduce((total, component) => total + (component.quantity * component.cost), 0)
})

const openAddModal = () => {
  editingComponent.value = null
  resetForm()
  showModal.value = true
}

const editComponent = (component) => {
  editingComponent.value = component
  Object.assign(form, component)
  showModal.value = true
}

const closeModal = () => {
  showModal.value = false
  editingComponent.value = null
  resetForm()
}

const resetForm = () => {
  Object.assign(form, {
    name: '',
    photo: '',
    category: '',
    quantity: 0,
    cost: 0,
    location: ''
  })
}

const saveComponent = () => {
  if (editingComponent.value) {
    const index = components.value.findIndex(c => c.id === editingComponent.value.id)
    if (index !== -1) {
      components.value[index] = { ...form, id: editingComponent.value.id }
    }
  } else {
    const newComponent = {
      ...form,
      id: Date.now() 
    }
    components.value.push(newComponent)
  }
  closeModal()
}

const deleteComponent = (id) => {
  if (confirm('Are you sure you want to delete this component? This action cannot be undone.')) {
    components.value = components.value.filter(c => c.id !== id)
  }
}
</script>