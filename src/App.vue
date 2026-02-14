<template>
  <div class="container">
    <div class="card">
      <h1>Prueba Técnica 2 — Vue.js</h1>
      <p class="muted">
        Carga usuarios desde un JSON local, muestra la lista, filtra por rol y permite eliminar usuarios solo de la vista.
      </p>

      <div v-if="loading" class="loading">
        <span class="dot"></span>
        <span>Cargando usuarios...</span>
      </div>

      <div v-else>
        <UserList
          :users="users"
          @remove="handleRemove"
        />

        <div class="footer">
          <span>Usuarios en memoria: <strong>{{ users.length }}</strong></span>
          <span class="muted">Nota: eliminar solo afecta la vista (no modifica el JSON real).</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import UserList from './components/UserList.vue'

const users = ref([])
const loading = ref(true)

// Cargar JSON local usando fetch y guardarlo en el estado (reactivo)
onMounted(async () => {
  try {
    const res = await fetch('/src/data/users.json')
    if (!res.ok) throw new Error('No se pudo cargar el JSON local')
    users.value = await res.json()
  } catch (err) {
    console.error(err)
    users.value = []
  } finally {
    loading.value = false
  }
})

// Eliminar solo de la vista (estado en memoria)
const handleRemove = (id) => {
  users.value = users.value.filter(u => u.id !== id)
}
</script>
