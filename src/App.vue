<template>
  <div class="min-h-screen bg-gray-50 p-4 md:p-8">
    <div class="max-w-7xl mx-auto">
      <header class="mb-8">
        <h1 class="text-3xl font-bold text-gray-800 mb-2">Student Management System</h1>
        <p class="text-gray-600">Manage student records with CRUD operations</p>
      </header>

      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-8">

        <div class="h-full">
          <StudentForm :student="editingStudent" @add-student="handleAddStudent" @update-student="handleUpdateStudent"
            @cancel-edit="handleCancelEdit" />
        </div>

        <div class="h-full">
          <div class="bg-white p-6 rounded-xl shadow-lg h-full">
            <h2 class="text-xl font-bold text-gray-800 mb-4">Statistics</h2>
            <div class="space-y-4">
              <div class="bg-blue-50 p-4 rounded-lg border border-blue-100">
                <h3 class="font-semibold text-gray-600 text-sm mb-1">Total Students</h3>
                <p class="text-3xl font-bold text-blue-600">{{ students.length }}</p>
              </div>
              <div class="grid grid-cols-3 gap-4">
                <div class="bg-green-50 p-4 rounded-lg border border-green-100">
                  <h3 class="font-semibold text-gray-600 text-sm mb-1">Avg Math</h3>
                  <p class="text-2xl font-bold text-green-600">{{ calculateAverage('math') }}</p>
                </div>
                <div class="bg-sky-50 p-4 rounded-lg border border-sky-100">
                  <h3 class="font-semibold text-gray-600 text-sm mb-1">Avg Physics</h3>
                  <p class="text-2xl font-bold text-sky-600">{{ calculateAverage('physics') }}</p>
                </div>
                <div class="bg-purple-50 p-4 rounded-lg border border-purple-100">
                  <h3 class="font-semibold text-gray-600 text-sm mb-1">Avg English</h3>
                  <p class="text-2xl font-bold text-purple-600">{{ calculateAverage('english') }}</p>
                </div>
              </div>
              <div class="bg-yellow-50 p-4 rounded-lg border border-yellow-100">
                <h3 class="font-semibold text-gray-600 text-sm mb-1">Average Overall Score</h3>
                <p class="text-3xl font-bold text-yellow-600">{{ calculateOverallAverage() }}</p>
                <div class="mt-2 h-2 bg-gray-200 rounded-full overflow-hidden">
                  <div :style="{ width: calculateOverallAverage() + '%' }" :class="overallScoreColor"
                    class="h-full rounded-full transition-all duration-300">
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="w-full">
        <StudentTable :students="students" @edit-student="handleEditStudent" @delete-student="handleDeleteStudent" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import StudentForm from './components/StudentFormComponent.vue'
import StudentTable from './components/StudentTableComponent.vue'

// State
const students = ref([])
const editingStudent = ref(null)
const STORAGE_KEY = 'student-vdata'

// Initialize with sample data (Fallback)
const initializeSampleData = () => {
  const sampleStudents = [
    { id: 1, firstName: 'John', lastName: 'Doe', math: 85, physics: 78, english: 92 },
    { id: 2, firstName: 'Jane', lastName: 'Smith', math: 90, physics: 88, english: 95 },
    { id: 3, firstName: 'Bob', lastName: 'Johnson', math: 75, physics: 82, english: 88 },
  ]
  students.value = sampleStudents
}

// CRUD Operations
const handleAddStudent = (student) => {
  const newId = students.value.length > 0
    ? Math.max(...students.value.map(s => s.id)) + 1
    : 1;

  const newStudent = {
    ...student,
    id: newId
  }

  students.value = [...students.value, newStudent]
}

const handleEditStudent = (student) => {
  editingStudent.value = { ...student }
}

const handleUpdateStudent = (updatedStudent) => {
  const index = students.value.findIndex(s => s.id === updatedStudent.id)
  if (index !== -1) {
    const updatedStudents = [...students.value]
    updatedStudents[index] = { ...updatedStudent }
    students.value = updatedStudents
  }
  editingStudent.value = null
}

const handleDeleteStudent = (id) => {
  students.value = students.value.filter(student => student.id !== id)
  if (editingStudent.value?.id === id) {
    editingStudent.value = null
  }
}

const handleCancelEdit = () => {
  editingStudent.value = null
}

// Computed Properties
const calculateAverage = (subject) => {
  if (students.value.length === 0) return '0.0'
  const total = students.value.reduce((sum, student) => sum + student[subject], 0)
  return (total / students.value.length).toFixed(1)
}

const calculateOverallAverage = () => {
  if (students.value.length === 0) return '0.0'
  const total = students.value.reduce((sum, student) => {
    return sum + (student.math + student.physics + student.english) / 3
  }, 0)
  return (total / students.value.length).toFixed(1)
}

const overallScoreColor = computed(() => {
  const avg = parseFloat(calculateOverallAverage())
  if (avg >= 80) return 'bg-green-500'
  if (avg >= 60) return 'bg-yellow-500'
  return 'bg-red-500'
})

// Persistence Logic

// 1. Watch for changes in students array and save to localStorage
// This covers Add, Update, and Delete automatically
watch(students, (newVal) => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(newVal))
}, { deep: true })

// 2. Load from localStorage on mount
onMounted(() => {
  const savedData = localStorage.getItem(STORAGE_KEY)
  if (savedData) {
    try {
      students.value = JSON.parse(savedData)
    } catch (e) {
      console.error('Error parsing local storage data', e)
      initializeSampleData()
    }
  } else {
    // Only load sample data if localStorage is empty
    initializeSampleData()
  }
})
</script>

<style scoped>
/* Smooth transitions */
.transition-all {
  transition-property: all;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 300ms;
}
</style>