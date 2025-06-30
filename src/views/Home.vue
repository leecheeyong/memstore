<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <header class="bg-white border-b border-gray-100">
      <div class="max-w-6xl mx-auto px-6 py-8">
        <div class="flex justify-between items-center">
          <div class="flex items-center space-x-4">
            <div class="w-10 h-10 bg-black rounded-lg flex items-center justify-center">
              <i class='bx bx-chip text-white text-xl'></i>
            </div>
            <div>
              <h1 class="text-2xl font-semibold text-gray-900">MemStore</h1>
              <p class="text-sm text-gray-500">Component inventory</p>
            </div>
          </div>
          <button
            @click="openAddModal"
            class="bg-black text-white px-6 py-2.5 rounded-lg hover:bg-gray-800 transition-colors duration-200 text-sm font-medium"
          >
            Add Component
          </button>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-6xl mx-auto px-6 py-12">
      <!-- Quick Stats -->
      <div class="grid grid-cols-3 gap-8 mb-16">
        <div class="text-center">
          <div class="text-3xl font-light text-gray-900 mb-1">{{ components.length }}</div>
          <div class="text-sm text-gray-500">Components</div>
        </div>
        <div class="text-center">
          <div class="text-3xl font-light text-gray-900 mb-1">{{ totalQuantity }}</div>
          <div class="text-sm text-gray-500">Total Units</div>
        </div>
        <div class="text-center">
          <div class="text-3xl font-light text-gray-900 mb-1">${{ totalValue.toFixed(0) }}</div>
          <div class="text-sm text-gray-500">Total Value</div>
        </div>
      </div>

      <!-- Search and View Toggle -->
      <div class="flex justify-between items-center mb-12">
        <div class="relative max-w-md">
          <input
            v-model="searchQuery"
            type="text"
            placeholder="Search components..."
            class="w-full px-4 py-3 bg-white border border-gray-200 rounded-lg focus:outline-none focus:border-gray-400 transition-colors duration-200 text-sm"
          >
          <i class='bx bx-search absolute right-4 top-1/2 transform -translate-y-1/2 text-gray-400'></i>
        </div>
        
        <!-- View Toggle -->
        <div class="flex space-x-1 bg-gray-100 rounded-lg p-1">
          <button
            @click="currentView = 'grid'"
            :class="[
              'px-3 py-2 rounded-md text-sm font-medium transition-colors duration-200',
              currentView === 'grid' 
                ? 'bg-white text-gray-900 shadow-sm' 
                : 'text-gray-600 hover:text-gray-900'
            ]"
          >
            <i class='bx bx-grid-alt mr-1'></i>
            Grid
          </button>
          <button
            @click="currentView = 'list'"
            :class="[
              'px-3 py-2 rounded-md text-sm font-medium transition-colors duration-200',
              currentView === 'list' 
                ? 'bg-white text-gray-900 shadow-sm' 
                : 'text-gray-600 hover:text-gray-900'
            ]"
          >
            <i class='bx bx-list-ul mr-1'></i>
            List
          </button>
        </div>
      </div>

      <!-- Grid View -->
      <div v-if="currentView === 'grid'" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        <div
          v-for="component in filteredComponents"
          :key="component.id"
          class="group bg-white rounded-xl border border-gray-100 overflow-hidden hover:border-gray-200 transition-all duration-300"
        >
          <!-- Component Image -->
          <div class="aspect-square bg-gray-50 flex items-center justify-center relative">
            <img
              v-if="component.photo"
              :src="component.photo"
              :alt="component.name"
              class="w-full h-full object-cover"
            >
            <i v-else class='bx bx-chip text-4xl text-gray-300'></i>
            
            <!-- Actions -->
            <div class="absolute top-4 right-4 opacity-0 group-hover:opacity-100 transition-opacity duration-200">
              <div class="flex space-x-2">
                <button
                  @click="editComponent(component)"
                  class="w-8 h-8 bg-white rounded-full flex items-center justify-center shadow-sm hover:shadow-md transition-shadow duration-200"
                >
                  <i class='bx bx-edit text-gray-600 text-sm'></i>
                </button>
                <button
                  @click="deleteComponent(component.id)"
                  class="w-8 h-8 bg-white rounded-full flex items-center justify-center shadow-sm hover:shadow-md transition-shadow duration-200"
                >
                  <i class='bx bx-trash text-gray-600 text-sm'></i>
                </button>
              </div>
            </div>
          </div>

          <!-- Component Info -->
          <div class="p-6">
            <h3 class="font-medium text-gray-900 mb-2 truncate">{{ component.name }}</h3>
            <div class="space-y-2 text-sm text-gray-500">
              <div>{{ component.category }}</div>
              <div class="flex justify-between">
                <span>{{ component.quantity }} units</span>
                <span>${{ component.cost.toFixed(2) }}</span>
              </div>
              <div>{{ component.location }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- List View -->
      <div v-else class="space-y-4">
        <div
          v-for="component in filteredComponents"
          :key="component.id"
          class="group bg-white rounded-xl border border-gray-100 p-6 hover:border-gray-200 transition-all duration-300"
        >
          <div class="flex items-center space-x-6">
            <!-- Component Image -->
            <div 
              class="w-16 h-16 bg-gray-50 rounded-lg flex items-center justify-center flex-shrink-0 cursor-pointer hover:bg-gray-100 transition-colors duration-200"
              @click="openImageModal(component)"
            >
              <img
                v-if="component.photo"
                :src="component.photo"
                :alt="component.name"
                class="w-full h-full object-cover rounded-lg"
              >
              <i v-else class='bx bx-chip text-2xl text-gray-300'></i>
            </div>

            <!-- Component Info -->
            <div class="flex-1 min-w-0">
              <h3 class="font-medium text-gray-900 mb-1 truncate">{{ component.name }}</h3>
              <div class="flex items-center space-x-6 text-sm text-gray-500">
                <span>{{ component.category }}</span>
                <span>{{ component.quantity }} units</span>
                <span>${{ component.cost.toFixed(2) }}</span>
                <span>{{ component.location }}</span>
              </div>
            </div>

            <!-- Actions -->
            <div class="flex space-x-2 opacity-0 group-hover:opacity-100 transition-opacity duration-200">
              <button
                @click="editComponent(component)"
                class="w-8 h-8 bg-gray-100 rounded-lg flex items-center justify-center hover:bg-gray-200 transition-colors duration-200"
              >
                <i class='bx bx-edit text-gray-600 text-sm'></i>
              </button>
              <button
                @click="deleteComponent(component.id)"
                class="w-8 h-8 bg-gray-100 rounded-lg flex items-center justify-center hover:bg-gray-200 transition-colors duration-200"
              >
                <i class='bx bx-trash text-gray-600 text-sm'></i>
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- Empty State -->
      <div v-if="filteredComponents.length === 0" class="text-center py-24">
        <div class="w-16 h-16 bg-gray-100 rounded-full flex items-center justify-center mx-auto mb-6">
          <i class='bx bx-package text-2xl text-gray-400'></i>
        </div>
        <h3 class="text-lg font-medium text-gray-900 mb-2">
          {{ searchQuery ? 'No components found' : 'No components yet' }}
        </h3>
        <p class="text-gray-500 mb-8 max-w-sm mx-auto">
          {{ searchQuery ? 'Try a different search term.' : 'Add your first component to get started.' }}
        </p>
        <button
          v-if="!searchQuery"
          @click="openAddModal"
          class="bg-black text-white px-6 py-2.5 rounded-lg hover:bg-gray-800 transition-colors duration-200 text-sm font-medium"
        >
          Add Component
        </button>
      </div>
    </main>

    <!-- Image Modal -->
    <Teleport to="body">
      <div 
        v-if="showImageModal" 
        class="fixed inset-0 bg-black/80 flex items-center justify-center p-6 z-50"
        @click="closeImageModal"
      >
        <div class="relative max-w-4xl max-h-full">
          <!-- Close Button -->
          <button
            @click="closeImageModal"
            class="absolute -top-12 right-0 w-10 h-10 bg-white/10 hover:bg-white/20 rounded-full flex items-center justify-center transition-colors duration-200 z-10"
          >
            <i class='bx bx-x text-white text-xl'></i>
          </button>
          
          <!-- Image -->
          <div class="bg-white rounded-2xl overflow-hidden shadow-2xl" @click.stop>
            <img
              v-if="selectedImage?.photo"
              :src="selectedImage.photo"
              :alt="selectedImage.name"
              class="max-w-full max-h-[80vh] object-contain"
            >
            <div v-else class="w-96 h-96 bg-gray-100 flex items-center justify-center">
              <i class='bx bx-chip text-6xl text-gray-300'></i>
            </div>
            
            <!-- Image Info -->
            <div class="p-6 border-t border-gray-100">
              <h3 class="font-medium text-gray-900 mb-2">{{ selectedImage?.name }}</h3>
              <div class="flex items-center space-x-6 text-sm text-gray-500">
                <span>{{ selectedImage?.category }}</span>
                <span>{{ selectedImage?.quantity }} units</span>
                <span>${{ selectedImage?.cost.toFixed(2) }}</span>
                <span>{{ selectedImage?.location }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </Teleport>

    <!-- Add/Edit Component Modal -->
    <Teleport to="body">
      <div v-if="showModal" class="fixed inset-0 bg-black/20 flex items-center justify-center p-6 z-50">
        <div class="bg-white rounded-2xl shadow-xl max-w-md w-full max-h-[90vh] overflow-y-auto">
          <div class="p-8">
            <div class="flex justify-between items-start mb-8">
              <div>
                <h2 class="text-xl font-semibold text-gray-900 mb-1">
                  {{ editingComponent ? 'Edit Component' : 'Add Component' }}
                </h2>
                <p class="text-sm text-gray-500">
                  {{ editingComponent ? 'Update component details' : 'Fill in component information' }}
                </p>
              </div>
              <button
                @click="closeModal"
                class="w-8 h-8 flex items-center justify-center rounded-lg hover:bg-gray-100 transition-colors duration-200"
              >
                <i class='bx bx-x text-xl text-gray-400'></i>
              </button>
            </div>

            <form @submit.prevent="saveComponent" class="space-y-6">
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Name</label>
                <input
                  v-model="form.name"
                  type="text"
                  required
                  class="w-full px-4 py-3 border border-gray-200 rounded-lg focus:outline-none focus:border-gray-400 transition-colors duration-200"
                  placeholder="Component name"
                >
              </div>

              <!-- Image Upload -->
              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Photo</label>
                
                <!-- Upload Method Toggle -->
                <div class="flex space-x-1 mb-4">
                  <button
                    type="button"
                    @click="uploadMethod = 'file'"
                    :class="[
                      'px-3 py-1.5 text-xs rounded-md transition-colors duration-200',
                      uploadMethod === 'file' 
                        ? 'bg-gray-900 text-white' 
                        : 'bg-gray-100 text-gray-600 hover:bg-gray-200'
                    ]"
                  >
                    Upload
                  </button>
                  <button
                    type="button"
                    @click="uploadMethod = 'url'"
                    :class="[
                      'px-3 py-1.5 text-xs rounded-md transition-colors duration-200',
                      uploadMethod === 'url' 
                        ? 'bg-gray-900 text-white' 
                        : 'bg-gray-100 text-gray-600 hover:bg-gray-200'
                    ]"
                  >
                    URL
                  </button>
                </div>

                <!-- File Upload -->
                <div v-if="uploadMethod === 'file'">
                  <div
                    @drop="handleDrop"
                    @dragover.prevent
                    @dragenter.prevent
                    @dragleave="isDragOver = false"
                    @dragover="isDragOver = true"
                    :class="[
                      'border-2 border-dashed rounded-lg p-8 text-center transition-colors duration-200 cursor-pointer',
                      isDragOver ? 'border-gray-400 bg-gray-50' : 'border-gray-200 hover:border-gray-300'
                    ]"
                    @click="$refs.fileInput.click()"
                  >
                    <div v-if="!uploadState.uploading && !form.photo">
                      <i class='bx bx-cloud-upload text-3xl text-gray-300 mb-3'></i>
                      <p class="text-sm text-gray-600 mb-1">Drop image here or click to browse</p>
                      <p class="text-xs text-gray-400">JPG, PNG, GIF up to 10MB</p>
                    </div>
                    
                    <!-- Upload Progress -->
                    <div v-else-if="uploadState.uploading" class="py-4">
                      <div class="w-6 h-6 border-2 border-gray-300 border-t-gray-900 rounded-full animate-spin mx-auto mb-3"></div>
                      <p class="text-sm text-gray-600">Uploading...</p>
                    </div>
                    
                    <!-- Preview -->
                    <div v-else-if="form.photo">
                      <img :src="form.photo" alt="Preview" class="w-20 h-20 object-cover rounded-lg mx-auto mb-3">
                      <p class="text-sm text-gray-600 mb-2">Image uploaded</p>
                      <button
                        type="button"
                        @click.stop="removeImage"
                        class="text-xs text-gray-500 hover:text-gray-700"
                      >
                        Remove
                      </button>
                    </div>
                  </div>
                  
                  <input
                    ref="fileInput"
                    type="file"
                    accept="image/*"
                    @change="handleFileSelect"
                    class="hidden"
                  >
                  
                  <!-- Error -->
                  <div v-if="uploadState.error" class="mt-3 p-3 bg-red-50 border border-red-100 rounded-lg">
                    <p class="text-red-700 text-sm">{{ uploadState.error }}</p>
                  </div>
                </div>

                <!-- URL Input -->
                <div v-else>
                  <input
                    v-model="form.photo"
                    type="url"
                    class="w-full px-4 py-3 border border-gray-200 rounded-lg focus:outline-none focus:border-gray-400 transition-colors duration-200"
                    placeholder="https://example.com/image.jpg"
                  >
                </div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Category</label>
                <select
                  v-model="form.category"
                  required
                  class="w-full px-4 py-3 border border-gray-200 rounded-lg focus:outline-none focus:border-gray-400 transition-colors duration-200"
                >
                  <option value="">Select category</option>
                  <option v-for="category in categories" :key="category" :value="category">{{ category }}</option>
                </select>
              </div>

              <div class="grid grid-cols-2 gap-4">
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Quantity</label>
                  <input
                    v-model.number="form.quantity"
                    type="number"
                    min="0"
                    required
                    class="w-full px-4 py-3 border border-gray-200 rounded-lg focus:outline-none focus:border-gray-400 transition-colors duration-200"
                    placeholder="0"
                  >
                </div>
                <div>
                  <label class="block text-sm font-medium text-gray-700 mb-2">Cost</label>
                  <input
                    v-model.number="form.cost"
                    type="number"
                    min="0"
                    step="0.01"
                    required
                    class="w-full px-4 py-3 border border-gray-200 rounded-lg focus:outline-none focus:border-gray-400 transition-colors duration-200"
                    placeholder="0.00"
                  >
                </div>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700 mb-2">Location</label>
                <input
                  v-model="form.location"
                  type="text"
                  required
                  class="w-full px-4 py-3 border border-gray-200 rounded-lg focus:outline-none focus:border-gray-400 transition-colors duration-200"
                  placeholder="Storage location"
                >
              </div>

              <div class="flex space-x-3 pt-4">
                <button
                  type="submit"
                  :disabled="uploadState.uploading"
                  class="flex-1 bg-black text-white py-3 rounded-lg hover:bg-gray-800 disabled:bg-gray-400 transition-colors duration-200 font-medium"
                >
                  {{ editingComponent ? 'Update' : 'Add' }}
                </button>
                <button
                  type="button"
                  @click="closeModal"
                  class="flex-1 bg-gray-100 text-gray-700 py-3 rounded-lg hover:bg-gray-200 transition-colors duration-200 font-medium"
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
const showImageModal = ref(false)
const editingComponent = ref(null)
const selectedImage = ref(null)
const searchQuery = ref('')
const currentView = ref('grid')
const uploadMethod = ref('file')
const isDragOver = ref(false)

const form = reactive({
  name: '',
  photo: '',
  category: '',
  quantity: 0,
  cost: 0,
  location: ''
})

const uploadState = reactive({
  uploading: false,
  error: null
})

const filteredComponents = computed(() => {
  if (!searchQuery.value) return components.value
  
  return components.value.filter(component => {
    const query = searchQuery.value.toLowerCase()
    return component.name.toLowerCase().includes(query) ||
           component.category.toLowerCase().includes(query) ||
           component.location.toLowerCase().includes(query)
  })
})

const totalQuantity = computed(() => {
  return components.value.reduce((total, component) => total + component.quantity, 0)
})

const totalValue = computed(() => {
  return components.value.reduce((total, component) => total + (component.quantity * component.cost), 0)
})

const openImageModal = (component) => {
  if (component.photo) {
    selectedImage.value = component
    showImageModal.value = true
  }
}

const closeImageModal = () => {
  showImageModal.value = false
  selectedImage.value = null
}

const uploadToImgur = async (file) => {
  const formData = new FormData()
  formData.append('image', file)

  try {
    uploadState.uploading = true
    uploadState.error = null

    const response = await fetch('https://api.imgur.com/3/image', {
      method: 'POST',
      headers: {
        'Authorization': 'Client-ID 546c25a59c58ad7'
      },
      body: formData
    })

    if (!response.ok) {
      throw new Error('Upload failed')
    }

    const data = await response.json()
    
    if (data.success) {
      form.photo = data.data.link
      return data.data.link
    } else {
      throw new Error('Upload failed')
    }
  } catch (error) {
    uploadState.error = 'Failed to upload image'
    throw error
  } finally {
    uploadState.uploading = false
  }
}

const validateFile = (file) => {
  const validTypes = ['image/jpeg', 'image/jpg', 'image/png', 'image/gif', 'image/webp']
  const maxSize = 10 * 1024 * 1024

  if (!validTypes.includes(file.type)) {
    throw new Error('Please select a valid image file')
  }

  if (file.size > maxSize) {
    throw new Error('File size must be less than 10MB')
  }

  return true
}

const handleFileSelect = async (event) => {
  const file = event.target.files[0]
  if (file) {
    try {
      validateFile(file)
      await uploadToImgur(file)
    } catch (error) {
      uploadState.error = error.message
    }
  }
}

const handleDrop = async (event) => {
  event.preventDefault()
  isDragOver.value = false

  const files = Array.from(event.dataTransfer.files)
  const imageFile = files.find(file => file.type.startsWith('image/'))

  if (imageFile) {
    try {
      validateFile(imageFile)
      await uploadToImgur(imageFile)
    } catch (error) {
      uploadState.error = error.message
    }
  } else {
    uploadState.error = 'Please drop an image file'
  }
}

const removeImage = () => {
  form.photo = ''
  uploadState.error = null
}

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
  uploadState.error = null
  uploadState.uploading = false
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
  uploadMethod.value = 'file'
}

const saveComponent = () => {
  if (uploadState.uploading) return

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
  if (confirm('Delete this component?')) {
    components.value = components.value.filter(c => c.id !== id)
  }
}
</script>

