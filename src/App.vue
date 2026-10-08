<script setup>
import { ref, computed, onMounted } from 'vue'
import AssignmentItem from './components/AssignmentItem.vue'

// ---------- STATE ----------
const assignments = ref([])

// form fields for the "Create" part
const title = ref('')
const subject = ref('')
const dueDate = ref('')
const priority = ref('Medium')

// ---------- CREATE ----------
function addAssignment() {
  if (title.value.trim() === '' || subject.value.trim() === '') {
    return
  }

  assignments.value.push({
    id: Date.now(),
    title: title.value.trim(),
    subject: subject.value.trim(),
    dueDate: dueDate.value,
    priority: priority.value,
    completed: false
  })

  title.value = ''
  subject.value = ''
  dueDate.value = ''
  priority.value = 'Medium'

  saveAssignments()
}

// ---------- DELETE ----------
function deleteAssignment(id) {
  assignments.value = assignments.value.filter(a => a.id !== id)

  saveAssignments()
}

// ---------- UPDATE ----------
function updateAssignment(updated) {
  const index = assignments.value.findIndex(a => a.id === updated.id)

  if (index !== -1) {
    assignments.value[index] = updated
  }

  saveAssignments()
}

// ---------- READ (display + filter) ----------
const filter = ref('All')

const filteredAssignments = computed(() => {
  if (filter.value === 'Pending') {
    return assignments.value.filter(a => !a.completed)
  }
  if (filter.value === 'Done') {
    return assignments.value.filter(a => a.completed)
  }
  return assignments.value
})

const pendingCount = computed(
  () => assignments.value.filter(a => !a.completed).length
)

// ---------- STORAGE ----------
function saveAssignments() {
  localStorage.setItem('assignments', JSON.stringify(assignments.value))
}

onMounted(() => {
  const saved = localStorage.getItem('assignments')

  if (saved) {
    assignments.value = JSON.parse(saved)
  }
})
</script>

<template>
  <div class="container">

    <h1>Study Nest Tracker</h1>
    <p class="subtitle">{{ pendingCount }} pending assignment(s)</p>

    <!-- Add Assignment -->
    <div class="add-form">
      <input v-model="title" type="text" placeholder="Assignment title" />
      <input
        v-model="subject"
        type="text"
        placeholder="Subject (e.g. CC302)"
        @keyup.enter="addAssignment"
      />

      <div class="row">
        <input v-model="dueDate" type="date" />

        <select v-model="priority">
          <option>Low</option>
          <option>Medium</option>
          <option>High</option>
        </select>

        <button class="add-btn" @click="addAssignment">Add</button>
      </div>
    </div>

    <!-- Filter -->
    <div class="filters">
      <button
        v-for="f in ['All', 'Pending', 'Done']"
        :key="f"
        :class="{ active: filter === f }"
        @click="filter = f"
      >
        {{ f }}
      </button>
    </div>

    <!-- Assignment List -->
    <div class="list">
      <AssignmentItem
        v-for="a in filteredAssignments"
        :key="a.id"
        :assignment="a"
        @delete="deleteAssignment"
        @update="updateAssignment"
      />

      <p v-if="filteredAssignments.length === 0" class="empty">
        Nothing here yet. Add an assignment above.
      </p>
    </div>

  </div>
</template>

<style>
.container {
  width: 520px;
  max-width: 100%;
  margin: 40px auto;
  padding: 0 16px;
}

h1 {
  text-align: center;
  margin-bottom: 4px;
}

.subtitle {
  text-align: center;
  color: #777;
  margin-top: 0;
}

.add-form {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 16px;
}

.add-form .row {
  display: flex;
  gap: 10px;
}

.add-form .row input {
  flex: 1;
}

.add-btn {
  background: #3d6b4f;
  color: white;
}

.filters {
  display: flex;
  gap: 8px;
  margin-bottom: 14px;
}

.filters button {
  background: #e4ded3;
}

.filters button.active {
  background: #3d6b4f;
  color: white;
}

.list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.empty {
  text-align: center;
  color: #999;
}
</style>
