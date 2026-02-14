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

      <div v-if="flash" :class="['flash', flashType]">{{ flash }}</div>

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
          <span>Usuarios visibles: <strong>{{ users.length }}</strong></span>
          <span class="muted">Nota: eliminar solo afecta la vista (no modifica el JSON real).</span>
        </div>

        <div class="hidden-users">
          <button class="btn" @click="showHidden = !showHidden">
            {{ showHidden ? 'Ocultar usuarios eliminados' : 'Mostrar usuarios eliminados' }} ({{ hiddenUsers.length }})
          </button>

          <div v-if="showHidden && hiddenUsers.length" class="hidden-list">
            <div v-for="user in hiddenUsers" :key="user.id" class="hidden-item">
              <span>{{ user.name }} ({{ user.role }})</span>
              <button class="btn" @click="restoreUser(user.id)">Restaurar</button>
            </div>
          </div>
          <p v-else-if="showHidden" class="muted">No hay usuarios eliminados.</p>
        </div>
      </div>
    </div>

    <div class="card todo-card">
      <TodoList
        :tasks="tasks"
        :roles="roleOptions"
        :users="users"
        @toggle="toggleTask"
        @add="addTask"
        @update-assignee="updateTaskAssignee"
        @remove="removeTask"
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
const hiddenUsers = ref([])
const showHidden = ref(false)
const loading = ref(true)

const flash = ref('')
const flashType = ref('flash-success')
let flashTimer = null

const tasks = ref([
  { id: 1, title: 'Revisar logs de errores del dashboard', role: 'admin', assignedUserId: null, done: false },
  { id: 2, title: 'Actualizar datos de contacto de clientes VIP', role: 'user', assignedUserId: null, done: false },
  { id: 3, title: 'Preparar reporte semanal de CX para gerencia', role: 'manager', assignedUserId: 6, done: true },
  { id: 4, title: 'Capacitar a nuevo agente en script de ventas', role: 'trainer', assignedUserId: 10, done: false },
  { id: 5, title: 'Optimizar guion de llamadas salientes', role: 'analyst', assignedUserId: 7, done: false },
  { id: 6, title: 'Revisar KPIs de calidad (QA) del sprint', role: 'qa', assignedUserId: 8, done: true },
  { id: 7, title: 'Auditar 5 llamadas aleatorias del día', role: 'supervisor', assignedUserId: 9, done: false },
  { id: 8, title: 'Configurar tablero de indicadores en Power BI', role: 'manager', assignedUserId: null, done: false },
  { id: 9, title: 'Refinar checklist de QA para onboarding', role: 'aprendiz', assignedUserId: 11, done: true },
  { id: 10, title: 'Documentar dudas surgidas durante la inducción', role: 'aprendiz', assignedUserId: 11, done: true },
  { id: 11, title: 'Prueba Técnica 1 CRUD Empleados', role: 'aprendiz', assignedUserId: 11, done: true },
  { id: 12, title: 'Prueba Técnica 2 — Vue.js', role: 'aprendiz', assignedUserId: 11, done: true },
  { id: 13, title: 'Citación aprendices nuevos', role: 'Psicologa', assignedUserId: 12, done: true }
])

const roleOptions = computed(() => {
  const set = new Set([
    ...users.value.map(u => u.role),
    ...tasks.value.map(t => t.role)
  ])
  return Array.from(set).sort((a, b) => a.localeCompare(b))
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

const notify = (msg, type = 'flash-success') => {
  flash.value = msg
  flashType.value = type
  if (flashTimer) clearTimeout(flashTimer)
  flashTimer = setTimeout(() => (flash.value = ''), 2500)
}

const handleRemove = (id) => {
  const user = users.value.find(u => u.id === id)
  if (user) hiddenUsers.value = [...hiddenUsers.value.filter(u => u.id !== id), user]
  users.value = users.value.filter(u => u.id !== id)
  tasks.value = tasks.value.map(t => t.assignedUserId === id ? { ...t, assignedUserId: null } : t)
  notify('Usuario eliminado de la vista. Puedes restaurarlo abajo.', 'flash-info')
}

const restoreUser = (id) => {
  const user = hiddenUsers.value.find(u => u.id === id)
  if (!user) return
  users.value = [...users.value, user]
  hiddenUsers.value = hiddenUsers.value.filter(u => u.id !== id)
  notify('Usuario restaurado correctamente.', 'flash-success')
}

const toggleTask = (id) => {
  tasks.value = tasks.value.map(t =>
    t.id === id ? { ...t, done: !t.done } : t
  )
  notify('Estado de la tarea actualizado.', 'flash-success')
}

const addTask = ({ title, role, userId }) => {
  const cleanTitle = title.trim()
  const cleanRole = role.trim() || 'user'
  if (!cleanTitle) return
  const nextId = tasks.value.length ? Math.max(...tasks.value.map(t => t.id)) + 1 : 1
  tasks.value = [
    ...tasks.value,
    { id: nextId, title: cleanTitle, role: cleanRole, assignedUserId: userId, done: false }
  ]
  notify('Tarea creada y asignada.', 'flash-success')
}

const updateTaskAssignee = ({ id, userId }) => {
  tasks.value = tasks.value.map(t =>
    t.id === id ? { ...t, assignedUserId: userId } : t
  )
  notify('Asignación de tarea actualizada.', 'flash-success')
}

const removeTask = (id) => {
  tasks.value = tasks.value.filter(t => t.id !== id)
  notify('Tarea eliminada.', 'flash-info')
}
</script>
