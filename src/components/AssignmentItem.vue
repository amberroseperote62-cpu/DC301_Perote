<script setup>
import { ref } from 'vue'

const props = defineProps({
  assignment: Object
})

const emit = defineEmits(['delete', 'update'])

const isEditing = ref(false)

// copy of the values so editing doesn't change the original until Save
const editedTitle = ref(props.assignment.title)
const editedSubject = ref(props.assignment.subject)
const editedDueDate = ref(props.assignment.dueDate)
const editedPriority = ref(props.assignment.priority)

function startEditing() {
  editedTitle.value = props.assignment.title
  editedSubject.value = props.assignment.subject
  editedDueDate.value = props.assignment.dueDate
  editedPriority.value = props.assignment.priority
  isEditing.value = true
}

function saveChanges() {
  if (editedTitle.value.trim() === '' || editedSubject.value.trim() === '') {
    return
  }

  emit('update', {
    ...props.assignment,
    title: editedTitle.value.trim(),
    subject: editedSubject.value.trim(),
    dueDate: editedDueDate.value,
    priority: editedPriority.value
  })

  isEditing.value = false
}

function toggleCompleted() {
  emit('update', {
    ...props.assignment,
    completed: !props.assignment.completed
  })
}

function deleteAssignment() {
  emit('delete', props.assignment.id)
}
</script>

<template>
  <div class="item">

    <!-- Normal Mode -->
    <div v-if="!isEditing" class="view">
      <input
        type="checkbox"
        :checked="assignment.completed"
        @change="toggleCompleted"
      />

      <div class="info">
        <span class="title" :class="{ completed: assignment.completed }">
          {{ assignment.title }}
        </span>
        <small>
          {{ assignment.subject }}
          <template v-if="assignment.dueDate"> · Due {{ assignment.dueDate }}</template>
        </small>
      </div>

      <span class="badge" :class="assignment.priority.toLowerCase()">
        {{ assignment.priority }}
      </span>

      <button class="edit" @click="startEditing">Edit</button>
      <button class="delete" @click="deleteAssignment">Delete</button>
    </div>

    <!-- Edit Mode -->
    <div v-else class="edit-mode">
      <input v-model="editedTitle" placeholder="Title" />
      <input v-model="editedSubject" placeholder="Subject" />

      <div class="row">
        <input v-model="editedDueDate" type="date" />
        <select v-model="editedPriority">
          <option>Low</option>
          <option>Medium</option>
          <option>High</option>
        </select>
      </div>

      <div class="row">
        <button class="save" @click="saveChanges">Save</button>
        <button class="cancel" @click="isEditing = false">Cancel</button>
      </div>
    </div>

  </div>
</template>

<style scoped>
.item {
  padding: 12px;
  background: white;
  border: 1px solid #ddd5c8;
  border-radius: 8px;
}

.view {
  display: flex;
  align-items: center;
  gap: 10px;
}

.info {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.info small {
  color: #888;
  margin-top: 2px;
}

.completed {
  text-decoration: line-through;
  color: gray;
}

.badge {
  font-size: 12px;
  padding: 3px 8px;
  border-radius: 10px;
}

.badge.low {
  background: #dcefe2;
  color: #2d6a43;
}

.badge.medium {
  background: #fdf0cc;
  color: #8a6a00;
}

.badge.high {
  background: #f9d9d6;
  color: #a02a22;
}

.edit {
  background: #e4ded3;
}

.delete,
.cancel {
  background: #f1d3d0;
  color: #a02a22;
}

.save {
  background: #3d6b4f;
  color: white;
}

.edit-mode {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.row {
  display: flex;
  gap: 8px;
}

.row input {
  flex: 1;
}
</style>
