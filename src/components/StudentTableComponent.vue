<template>
    <div class="bg-white rounded-xl shadow-lg overflow-hidden h-full flex flex-col">
        <div class="px-6 py-4 border-b border-gray-200 bg-gray-50">
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-4">
                <div>
                    <h2 class="text-xl font-bold text-gray-800">Student Records</h2>
                    <p class="text-sm text-gray-600 mt-1">
                        {{ students.length }} student{{ students.length !== 1 ? 's' : '' }} found
                    </p>
                </div>
                <div class="flex items-center space-x-4">
                    <div class="text-sm text-gray-500">
                        Showing all students
                    </div>
                </div>
            </div>
        </div>

        <div class="overflow-x-auto grow">
            <table class="w-full">
                <thead class="bg-gray-50">
                    <tr>
                        <th
                            class="py-4 px-6 text-left text-xs font-medium text-gray-500 uppercase tracking-wider sticky top-0 bg-gray-50">
                            <div class="flex items-center">
                                ID
                                <svg class="w-4 h-4 ml-1 text-gray-400" fill="none" stroke="currentColor"
                                    viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                        d="M8 9l4-4 4 4m0 6l-4 4-4-4" />
                                </svg>
                            </div>
                        </th>
                        <th
                            class="py-4 px-6 text-left text-xs font-medium text-gray-500 uppercase tracking-wider sticky top-0 bg-gray-50">
                            Student Info
                        </th>
                        <th
                            class="py-4 px-6 text-left text-xs font-medium text-gray-500 uppercase tracking-wider sticky top-0 bg-gray-50">
                            Math
                        </th>
                        <th
                            class="py-4 px-6 text-left text-xs font-medium text-gray-500 uppercase tracking-wider sticky top-0 bg-gray-50">
                            Physics
                        </th>
                        <th
                            class="py-4 px-6 text-left text-xs font-medium text-gray-500 uppercase tracking-wider sticky top-0 bg-gray-50">
                            English
                        </th>
                        <th
                            class="py-4 px-6 text-left text-xs font-medium text-gray-500 uppercase tracking-wider sticky top-0 bg-gray-50">
                            Average Score
                        </th>
                        <th
                            class="py-4 px-6 text-left text-xs font-medium text-gray-500 uppercase tracking-wider sticky top-0 bg-gray-50">
                            Ranking
                        </th>
                        <th
                            class="py-4 px-6 text-left text-xs font-medium text-gray-500 uppercase tracking-wider sticky top-0 bg-gray-50">
                            Actions
                        </th>
                    </tr>
                </thead>
                <tbody class="divide-y divide-gray-200">
                    <tr v-for="student in students" :key="student.id"
                        class="hover:bg-gray-50 transition-colors duration-150">
                        <td class="py-5 px-6">
                            <div class="flex items-center">
                                <span
                                    class="inline-flex items-center justify-center w-10 h-10 bg-gradient-to-br from-blue-100 to-blue-50 text-blue-800 rounded-full text-sm font-bold shadow-sm">
                                    #{{ student.id.toString().padStart(3, '0') }}
                                </span>
                            </div>
                        </td>
                        <td class="py-5 px-6">
                            <div>
                                <p class="font-semibold text-gray-900 text-lg">{{ student.firstName }} {{
                                    student.lastName }}</p>
                                <p class="text-sm text-gray-500 mt-1">Student ID: STU{{
                                    student.id.toString().padStart(4, '0') }}</p>
                            </div>
                        </td>
                        <td class="py-5 px-6">
                            <div class="flex flex-col space-y-2">
                                <span class="font-bold text-gray-900 text-lg">{{ student.math }}</span>
                                <div class="w-full bg-gray-200 rounded-full h-2">
                                    <div :style="{ width: student.math + '%' }" :class="getScoreColor(student.math)"
                                        class="h-2 rounded-full transition-all duration-500"></div>
                                </div>
                            </div>
                        </td>
                        <td class="py-5 px-6">
                            <div class="flex flex-col space-y-2">
                                <span class="font-bold text-gray-900 text-lg">{{ student.physics }}</span>
                                <div class="w-full bg-gray-200 rounded-full h-2">
                                    <div :style="{ width: student.physics + '%' }"
                                        :class="getScoreColor(student.physics)"
                                        class="h-2 rounded-full transition-all duration-500"></div>
                                </div>
                            </div>
                        </td>
                        <td class="py-5 px-6">
                            <div class="flex flex-col space-y-2">
                                <span class="font-bold text-gray-900 text-lg">{{ student.english }}</span>
                                <div class="w-full bg-gray-200 rounded-full h-2">
                                    <div :style="{ width: student.english + '%' }"
                                        :class="getScoreColor(student.english)"
                                        class="h-2 rounded-full transition-all duration-500"></div>
                                </div>
                            </div>
                        </td>

                        <td class="py-5 px-6">
                            <div class="flex flex-col items-center space-y-2">
                                <span class="font-bold text-2xl text-gray-900">
                                    {{ calculateAverage(student).toFixed(1) }}
                                </span>
                                <div class="w-24 bg-gray-200 rounded-full h-2">
                                    <div :style="{ width: calculateAverage(student) + '%' }"
                                        :class="getGradeColor(calculateAverage(student))" class="h-2 rounded-full">
                                    </div>
                                </div>
                            </div>
                        </td>

                        <td class="py-5 px-6">
                            <div class="flex items-center justify-center">
                                <span :class="getGradeClass(student)"
                                    class="px-4 py-2 rounded-full text-sm font-bold shadow-sm w-16 text-center">
                                    {{ getGrade(student) }}
                                </span>
                            </div>
                        </td>

                        <td class="py-5 px-6">
                            <div class="flex items-center space-x-3">
                                <button @click="handleEdit(student)"
                                    class="inline-flex items-center px-4 py-2 bg-gradient-to-r from-blue-500 to-blue-600 text-white rounded-lg hover:from-blue-600 hover:to-blue-700 transition-all duration-200 shadow-sm hover:shadow-md">
                                    Edit
                                </button>
                                <button @click="handleDelete(student.id)"
                                    class="inline-flex items-center px-4 py-2 bg-gradient-to-r from-red-500 to-red-600 text-white rounded-lg hover:from-red-600 hover:to-red-700 transition-all duration-200 shadow-sm hover:shadow-md">
                                    Delete
                                </button>
                            </div>
                        </td>
                    </tr>

                    <tr v-if="students.length === 0">
                        <td colspan="8" class="py-16 text-center">
                            <div class="max-w-md mx-auto">
                                <div class="text-gray-400 mb-6">
                                    <svg class="w-24 h-24 mx-auto opacity-50" fill="none" stroke="currentColor"
                                        viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1"
                                            d="M12 14l9-5-9-5-9 5 9 5z" />
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1"
                                            d="M12 14l6.16-3.422a12.083 12.083 0 01.665 6.479A11.952 11.952 0 0012 20.055a11.952 11.952 0 00-6.824-2.998 12.078 12.078 0 01.665-6.479L12 14z" />
                                    </svg>
                                </div>
                                <p class="text-2xl font-medium text-gray-500 mb-2">No students yet</p>
                                <p class="text-gray-400">Add your first student using the form</p>
                            </div>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
        <div v-if="students.length > 0" class="px-6 py-4 border-t border-gray-200 bg-gray-50">
            <div class="flex flex-col md:flex-row md:items-center justify-between text-sm text-gray-600">
                <div>
                    <span class="font-medium">Summary:</span>
                    Highest Average: {{ highestAverage }},
                    Lowest Average: {{ lowestAverage }}
                </div>
                <div class="mt-2 md:mt-0">
                    <span class="font-medium">Overall Class Performance:</span>
                    <span :class="overallClassColor" class="ml-2 px-2 py-1 rounded-full text-xs font-semibold">
                        {{ overallClassPerformance }}
                    </span>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { defineProps, computed } from 'vue'

const props = defineProps({
    students: {
        type: Array,
        required: true,
        default: () => []
    }
})

const emit = defineEmits(['edit-student', 'delete-student'])

// Computed properties for table
const highestAverage = computed(() => {
    if (props.students.length === 0) return '0.0'
    const averages = props.students.map(calculateAverage)
    return Math.max(...averages).toFixed(1)
})

const lowestAverage = computed(() => {
    if (props.students.length === 0) return '0.0'
    const averages = props.students.map(calculateAverage)
    return Math.min(...averages).toFixed(1)
})

const overallClassPerformance = computed(() => {
    if (props.students.length === 0) return 'No Data'
    const averages = props.students.map(calculateAverage)
    const classAverage = averages.reduce((sum, avg) => sum + avg, 0) / averages.length

    if (classAverage >= 80) return 'Excellent'
    if (classAverage >= 70) return 'Good'
    if (classAverage >= 60) return 'Average'
    return 'Needs Improvement'
})

const overallClassColor = computed(() => {
    const perf = overallClassPerformance.value
    if (perf === 'Excellent') return 'bg-green-100 text-green-800'
    if (perf === 'Good') return 'bg-blue-100 text-blue-800'
    if (perf === 'Average') return 'bg-yellow-100 text-yellow-800'
    return 'bg-red-100 text-red-800'
})

// Methods
const handleEdit = (student) => {
    emit('edit-student', student)
}

const handleDelete = (id) => {
    if (confirm('Are you sure you want to delete this student record?')) {
        emit('delete-student', id)
    }
}

const calculateAverage = (student) => {
    // Ensure numbers are treated as numbers
    const m = Number(student.math) || 0
    const p = Number(student.physics) || 0
    const e = Number(student.english) || 0
    return (m + p + e) / 3
}

// const getScoreGrade = (score) => {
//     if (score >= 90) return 'A'
//     if (score >= 80) return 'B'
//     if (score >= 70) return 'C'
//     if (score >= 60) return 'D'
//     return 'F'
// }

const getGrade = (student) => {
    const avg = calculateAverage(student)
    if (avg >= 90) return 'A'
    if (avg >= 80) return 'B'
    if (avg >= 70) return 'C'
    if (avg >= 60) return 'D'
    return 'F'
}

const getGradeClass = (student) => {
    const avg = calculateAverage(student)
    if (avg >= 90) return 'bg-gradient-to-r from-green-500 to-green-600 text-white'
    if (avg >= 80) return 'bg-gradient-to-r from-blue-500 to-blue-600 text-white'
    if (avg >= 70) return 'bg-gradient-to-r from-yellow-500 to-yellow-600 text-white'
    if (avg >= 60) return 'bg-gradient-to-r from-orange-500 to-orange-600 text-white'
    return 'bg-gradient-to-r from-red-500 to-red-600 text-white'
}

const getGradeColor = (average) => {
    if (average >= 80) return 'bg-green-500'
    if (average >= 60) return 'bg-yellow-500'
    return 'bg-red-500'
}

const getScoreColor = (score) => {
    if (score >= 80) return 'bg-green-500'
    if (score >= 60) return 'bg-yellow-500'
    if (score >= 40) return 'bg-orange-500'
    return 'bg-red-500'
}
</script>

<style scoped>
.sticky {
    position: sticky;
    top: 0;
    z-index: 10;
}

tr {
    transition: all 0.2s ease-in-out;
}

tr:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
}
</style>