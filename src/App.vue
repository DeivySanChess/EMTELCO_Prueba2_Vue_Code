<template>
  <div class="container">
    <div class="card">
      <div class="header">
        <div class="title">
          <h1>Prueba Técnica 2 — Vue.js</h1>
          <p class="muted">
            Carga usuarios desde un JSON local, muestra la lista, filtra por rol y permite eliminar usuarios solo de la vista.
          </p>
        </div>
        <img class="brand-logo" :src="logo" alt="Logo Emtelco" />
      </div>

      <div v-if="loading" class="loading">
        <span class="dot"></span>
        <span>Cargando usuarios...</span>
      </div>

      <div v-else>
        <UserList
          :users="users"
          :tasks="tasks"
          @remove="handleRemove"
        />

        <div class="footer">
          <span>Usuarios en memoria: <strong>{{ users.length }}</strong></span>
          <span class="muted">Nota: eliminar solo afecta la vista (no modifica el JSON real).</span>
        </div>
      </div>
    </div>

    <div class="card todo-card">
      <TodoList
        :tasks="tasks"
        :roles="roleOptions"
        @toggle="toggleTask"
        @add="addTask"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import UserList from './components/UserList.vue'
import TodoList from './components/TodoList.vue'
import logo from './assets/emtelco-logo.png'

const users = ref([])
const loading = ref(true)

const tasks = ref([
  { id: 1, title: 'Revisar logs de errores del dashboard', role: 'admin', done: false },
  { id: 2, title: 'Actualizar datos de contacto de clientes VIP', role: 'user', done: false },
  { id: 3, title: 'Preparar reporte semanal de CX para gerencia', role: 'manager', done: true },
  { id: 4, title: 'Capacitar a nuevo agente en script de ventas', role: 'trainer', done: false },
  { id: 5, title: 'Optimizar guion de llamadas salientes', role: 'analyst', done: false },
  { id: 6, title: 'Revisar KPIs de calidad (QA) del sprint', role: 'qa', done: true },
  { id: 7, title: 'Auditar 5 llamadas aleatorias del día', role: 'supervisor', done: false },
  { id: 8, title: 'Configurar tablero de indicadores en Power BI', role: 'manager', done: false },
  { id: 9, title: 'Refinar checklist de QA para onboarding', role: 'aprendiz', done: false },
  { id: 10, title: 'Documentar dudas surgidas durante la inducción', role: 'aprendiz', done: true }
])

const roleOptions = computed(() => {
  const set = new Set([
    ...users.value.map(u => u.role),
    ...tasks.value.map(t => t.role)
  ])
  return Array.from(set)
})

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

const handleRemove = (id) => {
  users.value = users.value.filter(u => u.id !== id)
}

const toggleTask = (id) => {
  tasks.value = tasks.value.map(t =>
    t.id === id ? { ...t, done: !t.done } : t
  )
}

const addTask = ({ title, role }) => {
  const cleanTitle = title.trim()
  const cleanRole = role.trim() || 'user'
  if (!cleanTitle) return
  const nextId = tasks.value.length ? Math.max(...tasks.value.map(t => t.id)) + 1 : 1
  tasks.value = [
    ...tasks.value,
    { id: nextId, title: cleanTitle, role: cleanRole, done: false }
  ]
}
</script>
