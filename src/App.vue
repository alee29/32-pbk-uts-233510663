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
      <div
       v-if="showNotification"
      :class="['notification', notification.includes('⚠️') ? 'notification-warning' : 'notification-success']">
       {{ notification }}
    </div>
      <table class="task-list">
      <thead>
        <tr>
          <th>Kegiatan</th>
          <th>Status</th>
          <th>Aksi</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index" :class="{ done: task.done }">
          <td :class="{ done: task.done }">{{ task.text }}</td>
          <td>
            <input type="checkbox" v-model="task.done" />
          </td>
          <td>
            <button @click="deleteTask(index)">❌</button>
          </td>
        </tr>
      </tbody>
    </table>
    <button @click="togglePending" class="toggle-btn">
      {{ showPending ? 'Tutup' : 'Tugas yg Belum Dikerjakan' }}
    </button>
    <div v-if="showPending" class="pending-section">
      <h2>Belum Dikerjakan</h2>
      <ul>
        <li v-for="(task, index) in pendingTasks":key="index" @click="markAsDone(task)">
           {{ task.text }}🔔
        </li>
      </ul>
    </div>
    <div v-if="confirmingDelete" class="modal-backdrop">
  <div class="modal-box">
    <p>🗑️ Yakin ingin menghapus kegiatan <strong>"{{ taskToDelete?.text }}"</strong>?</p>
    <div class="modal-actions">
      <button @click="confirmDelete" class="confirm-btn">Ya, Hapus</button>
      <button @click="cancelDelete" class="cancel-btn">Batal</button>
    </div>
  </div>
</div>
</div>
</template>

<style scoped>
.done {
  text-decoration: line-through;
  color: gray;
}
.app {
  max-width: 700px;
  margin: auto;
  padding: 2rem;
  font-family: 'Arial', sans-serif;
  text-align: center;
  background-color: #f9f9f9;
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
  animation: fadeZoomIn 1s ease;
  font-size: 0.9rem;
}
.input-section {
  display: flex;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 1rem;
}
input {
  padding: 0.5rem;
  font-size: 0.9rem;
  border: 1px solid #ddd;
  border-radius: 5px;
  width: 60%;
}
button {
  padding: 0.5rem 0.8rem;
  background-color: #4caf50;
  color: white;
  font-size: 0.9rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
  margin-top: 1.5rem;
}
button:hover {
  background-color: #45a049;
}
.notification {
  background-color: #ffeb3b;
  color: #333;
  padding: 0.8rem;
  margin-bottom: 1rem;
  border-radius: 8px;
  border: 1px solid #fbc02d;
  animation: fadeInOut 3s ease-in-out;
}
.task-list {
  width: 100%;
  margin-top: 1rem;
  border-collapse: collapse;
  table-layout: fixed;
  color: #333;
  font-size: 0.85rem;
}
.task-list th, .task-list td {
  padding: 0.8rem;
  text-align: center;
}
.task-list th {
  background-color: #4caf50;
  color: white;
}
.task-list td {
  background-color: #f9f9f9;
}
.task-list tr:nth-child(even) td {
  background-color: #f1f1f1;
}
.task-list td.done {
  text-decoration: line-through;
  color: gray;
}
.task-list td input {
  cursor: pointer;
}
.pending-section {
  margin-top: 2rem;
  padding: 1.2rem;
  background-color: #e7f5ff;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  animation: slideIn 0.5s ease-in-out;
}
.pending-section h2 {
  color: #007bff;
  font-size: 1.3rem;
  margin-bottom: 1rem;
}
.pending-section ul {
  list-style-type: none;
  padding: 0;
}
.pending-section li {
  background-color: #ffffff;
  padding: 8px;
  margin: 6px 0;
  border-radius: 5px;
  border: 1px solid #b0e0ff;
  text-align: center;
  font-size: 0.9rem;
  color: #333;
  transition: background-color 0.3s, transform 0.3s;
  cursor: pointer;
  justify-content: center;
}
.pending-section li:hover {
  background-color: #d1ecf1;
  transform: translateY(-2px);
}
@keyframes fadeInOut {
  0% { opacity: 0; transform: translateY(-10px); }
  10% { opacity: 1; transform: translateY(0); }
  90% { opacity: 1; }
  100% { opacity: 0; transform: translateY(-10px); }
}
@keyframes slideIn {
  0% { opacity: 0; transform: translateY(20px); }
  100% { opacity: 1; transform: translateY(0); }
}
@keyframes fadeIn {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}
@keyframes fadeZoomIn {
  0% {
    opacity: 0;
    transform: scale(0.9) translateY(20px);
  }
  100% {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}
h1 {
  font-family: 'Indie Flower', cursive;
  font-size: 2.4rem;
  color: #009688;
  text-shadow: 2px 2px 6px rgba(0,0,0,0.1);
}
.notification {
  padding: 0.9rem 1.2rem;
  margin: 1rem auto;
  width: fit-content;
  border-radius: 10px;
  font-weight: 500;
  display: inline-block;
  animation: fadeInOut 3s ease-in-out;
  font-size: 0.9rem;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
}
.notification-warning {
  background-color: #fff8e1;
  color: #9e7700;
  border: 1px solid #ffe082;
}
.notification-success {
  background-color: #e6f4ea;
  color: #2e7d32;
  border: 1px solid #a5d6a7;
}
button.toggle-btn {
  margin-top: 2rem;
  background-color: #03a9f4;
}
button.toggle-btn:hover {
  background-color: #0288d1;
}
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,0.4);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease-in-out;
}
.modal-box {
  background-color: #fff;
  padding: 1.5rem;
  border-radius: 12px;
  width: 90%;
  max-width: 400px;
  box-shadow: 0 8px 24px rgba(0,0,0,0.2);
  text-align: center;
  animation: slideIn 0.3s ease;
}
.modal-box p {
  margin-bottom: 1.2rem;
  font-size: 1rem;
}
.modal-actions {
  display: flex;
  justify-content: space-around;
  gap: 1rem;
}
.confirm-btn {
  background-color: #e53935;
  color: white;
  border: none;
  padding: 0.6rem 1.2rem;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s;
}
.confirm-btn:hover {
  background-color: #d32f2f;
}
.cancel-btn {
  background-color: #9e9e9e;
  color: white;
  border: none;
  padding: 0.6rem 1.2rem;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s;
}
.cancel-btn:hover {
  background-color: #757575;
}
.modal-backdrop {
  backdrop-filter: blur(5px); /* Efek blur */
  background-color: rgba(0, 0, 0, 0.6); /* Menggelapkan latar belakang */
}
.confirm-btn {
  background-color: #ff6347;
  transition: background-color 0.3s ease, transform 0.3s ease;
}
.confirm-btn:hover {
  background-color: #e53935;
  transform: scale(1.1); /* Efek membesar */
}

.cancel-btn {
  background-color: #9e9e9e;
  transition: background-color 0.3s ease, transform 0.3s ease;
}
.cancel-btn:hover {
  background-color: #757575;
  transform: scale(1.1);
}
</style>