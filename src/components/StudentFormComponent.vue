<template>
    <div class="bg-white rounded-xl shadow-lg p-6 h-full">
        <h2 class="text-xl font-bold text-gray-800 mb-6">
            {{ isEditing ? 'Edit Student' : 'Add New Student' }}
        </h2>

        <form @submit.prevent="handleSubmit" class="space-y-4">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <!-- First Name -->
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">
                        First Name *
                    </label>
                    <input v-model="formData.firstName" type="text" required
                        class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition"
                        placeholder="Enter first name" />
                </div>

                <!-- Last Name -->
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">
                        Last Name *
                    </label>
                    <input v-model="formData.lastName" type="text" required
                        class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition"
                        placeholder="Enter last name" />
                </div>
            </div>

            <!-- Scores -->
            <div class="space-y-4">
                <h3 class="font-medium text-gray-700">Scores (0-100)</h3>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <!-- Math Score -->
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">
                            Math
                        </label>
                        <input v-model="formData.math" type="number" min="0" max="100" required
                            class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition"
                            placeholder="0-100" @input="validateScore('math')" />
                    </div>

                    <!-- Physics Score -->
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">
                            Physics
                        </label>
                        <input v-model="formData.physics" type="number" min="0" max="100" required
                            class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition"
                            placeholder="0-100" @input="validateScore('physics')" />
                    </div>

                    <!-- English Score -->
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">
                            English
                        </label>
                        <input v-model="formData.english" type="number" min="0" max="100" required
                            class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition"
                            placeholder="0-100" @input="validateScore('english')" />
                    </div>
                </div>

                <!-- Score Visualization -->
                <div class="mt-6 p-4 bg-gray-50 rounded-lg">
                    <div class="flex items-center justify-between mb-2">
                        <span class="font-medium text-gray-700">Performance Summary</span>
                        <span class="text-sm text-gray-500">Average: {{ averageScore }}</span>
                    </div>
                    <div class="space-y-2">
                        <div class="flex items-center justify-between">
                            <span class="text-sm text-gray-600">Total Score:</span>
                            <span class="font-bold">{{ totalScore }}/300</span>
                        </div>
                        <div class="h-3 bg-gray-200 rounded-full overflow-hidden">
                            <div :class="scoreBarColor" :style="{ width: averageScorePercentage + '%' }"
                                class="h-full rounded-full transition-all duration-500"></div>
                        </div>
                        <div class="flex justify-between text-xs text-gray-500">
                            <span>0</span>
                            <span>50</span>
                            <span>100</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Form Status -->
            <div v-if="formStatus" class="p-3 rounded-lg"
                :class="formStatus.type === 'success' ? 'bg-green-50 text-green-700' : formStatus.type === 'info' ? 'bg-sky-50 text-sky-700' : 'bg-red-50 text-red-700'">
                {{ formStatus.message }}
            </div>

            <!-- Buttons -->
            <div class="flex gap-3 pt-4">
                <button type="submit"
                    class="flex-1 bg-blue-600 hover:bg-blue-700 text-white font-medium py-3 px-4 rounded-lg transition duration-200 shadow-sm hover:shadow-md">
                    {{ isEditing ? 'Update Student' : 'Add Student' }}
                </button>

                <button v-if="isEditing" @click="handleCancel" type="button"
                    class="px-6 py-3 border border-gray-300 text-gray-700 font-medium rounded-lg hover:bg-gray-50 transition duration-200 hover:shadow-sm">
                    Cancel
                </button>
            </div>
        </form>
    </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({
    student: {
        type: Object,
        default: null
    }
})

const emit = defineEmits(['add-student', 'update-student', 'cancel-edit'])

// Form data
const formData = ref({
    id: null,
    firstName: '',
    lastName: '',
    math: 0,
    physics: 0,
    english: 0
})

// Form status for feedback
const formStatus = ref(null)

// Computed properties
const isEditing = computed(() => !!props.student)
const totalScore = computed(() => {
    return Number(formData.value.math || 0) +
        Number(formData.value.physics || 0) +
        Number(formData.value.english || 0)
})
const averageScore = computed(() => (totalScore.value / 3).toFixed(1))
const averageScorePercentage = computed(() => (totalScore.value / 3))
const scoreBarColor = computed(() => {
    const avg = averageScorePercentage.value
    if (avg >= 80) return 'bg-green-500'
    if (avg >= 60) return 'bg-yellow-500'
    return 'bg-red-500'
})

// Methods
const validateScore = (subject) => {
    let value = formData.value[subject]
    if (value < 0) formData.value[subject] = 0
    if (value > 100) formData.value[subject] = 100
}

const handleSubmit = () => {
    // Validate all scores
    validateScore('math')
    validateScore('physics')
    validateScore('english')

    const studentData = {
        id: isEditing.value ? formData.value.id : null,
        firstName: formData.value.firstName.trim(),
        lastName: formData.value.lastName.trim(),
        math: Number(formData.value.math),
        physics: Number(formData.value.physics),
        english: Number(formData.value.english)
    }

    // Validate required fields
    if (!studentData.firstName || !studentData.lastName) {
        showStatus('Please fill in all required fields', 'error')
        return
    }

    if (isEditing.value) {
        emit('update-student', studentData)
        showStatus('Student updated successfully!', 'success')
    } else {
        emit('add-student', studentData)
        showStatus('Student added successfully!', 'success')
    }

    // Reset form after submission
    if (!isEditing.value) {
        resetForm()
    }
}

const handleCancel = () => {
    emit('cancel-edit')
    resetForm()
    showStatus('Edit cancelled', 'info')
}

const resetForm = () => {
    formData.value = {
        id: null,
        firstName: '',
        lastName: '',
        math: 0,
        physics: 0,
        english: 0
    }
}

const showStatus = (message, type) => {
    formStatus.value = { message, type }
    setTimeout(() => {
        formStatus.value = null
    }, 3000)
}

// Watch for editing student changes
watch(() => props.student, (newStudent) => {
    if (newStudent) {
        formData.value = { ...newStudent }
        showStatus('Editing student: ' + newStudent.firstName + ' ' + newStudent.lastName, 'info')
    } else {
        resetForm()
    }
}, { immediate: true })
</script>

<style scoped>
/* Custom input focus styles */
input:focus {
    outline: none;
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}
</style>