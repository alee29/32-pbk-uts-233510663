<script setup>
import { ref, computed } from 'vue'
    
    const newTask = ref('')
    const tasks = ref([])
    const showPending = ref(false)
    const notification = ref('')
    const showNotification = ref(false)
    const confirmingDelete = ref(false)
    const taskToDelete = ref(null)
    let taskIndexToDelete = -1
    
    function showMessage(msg) {
      notification.value = msg
      showNotification.value = true
      setTimeout(() => {
        showNotification.value = false
      }, 3000)
    }
    function markAsDone(task) {
      task.done = true
    }
    
    function addTask() {
      const trimmedInput = newTask.value.trim()
      const normalizedInput = trimmedInput.toLowerCase()
    
      if (trimmedInput === '') {
        showMessage('⚠️ Masukkan kegiatan terlebih dahulu.')
        return
      }
    
      const isDuplicate = tasks.value.some(
        task => task.text.trim().toLowerCase() === normalizedInput
      )
    
      if (isDuplicate) {
        showMessage('⚠️ Tugas sudah ada dalam daftar.')
        return
      }
    
      tasks.value.push({ text: trimmedInput, done: false })
      newTask.value = ''
    }
    
    function deleteTask(index) {
      taskToDelete.value = tasks.value[index]
      taskIndexToDelete = index
      confirmingDelete.value = true
    }

    function confirmDelete() {
      const removed = tasks.value.splice(taskIndexToDelete, 1)[0]
      showMessage(`🗑️ Kegiatan "${removed.text}" berhasil dihapus.`)
      confirmingDelete.value = false
      taskToDelete.value = null
      taskIndexToDelete = -1
    }

    function cancelDelete() {
      confirmingDelete.value = false
      taskToDelete.value = null
      taskIndexToDelete = -1
    }
    const pendingTasks = computed(() => tasks.value.filter(task => !task.done))
    
    function togglePending() {
      showPending.value = !showPending.value
    }
</script>

<template>
<div class="app">
  <h1>Kegiatan Ale📋</h1>
      <div class="input-section">
        <input v-model="newTask" @keyup.enter="addTask" placeholder="Tambah kegiatan..." />
        <button @click="addTask">Tambah</button>
      </div>
</div>
</template>

<style scoped>
</style>