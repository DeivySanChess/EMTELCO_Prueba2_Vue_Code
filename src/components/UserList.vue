<template>
  <div>
    <div class="row">
      <label>
        Filtrar por rol:
        <select v-model="selectedRole">
          <option value="">Todos</option>
          <option v-for="role in roles" :key="role" :value="role">
            {{ role }}
          </option>
        </select>
      </label>

      <label>
        Buscar por nombre/email:
        <input v-model="search" placeholder="Ej: ana o @mail.com" />
      </label>

      <span class="badge">
        Visibles: <strong>{{ filteredUsers.length }}</strong>
      </span>
    </div>

    <p v-if="filteredUsers.length === 0" class="muted">
      No hay usuarios visibles.
    </p>

    <table v-else>
      <thead>
        <tr>
          <th>Nombre</th>
          <th>Email</th>
          <th>Rol</th>
          <th>Tareas asignadas</th>
          <th style="width:140px;">Acción</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="user in filteredUsers" :key="user.id">
          <td>{{ user.name }}</td>
          <td class="email-cell">{{ user.email }}</td>
          <td>
            <span class="badge">{{ user.role }}</span>
          </td>
          <td>
            <div v-if="tasksForUser(user).length" class="tasks-cell">
              <div
                v-for="task in tasksForUser(user)"
                :key="task.id"
                class="task-chip"
                :class="{ done: task.done }"
              >
                <span>{{ task.title }}</span>
                <span class="status">{{ task.done ? 'Completada' : 'Pendiente' }}</span>
              </div>
            </div>
            <p v-else class="muted">Sin tareas asignadas</p>
          </td>
          <td>
            <button class="btn btn-danger" @click="removeUser(user.id)">
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  users: { type: Array, required: true, default: () => [] },
  tasks: { type: Array, required: true, default: () => [] }
})

const emit = defineEmits(['remove'])

const selectedRole = ref('')
const search = ref('')

const roles = computed(() => {
  const set = new Set(props.users.map(u => u.role))
  return Array.from(set).sort((a, b) => a.localeCompare(b))
})

const filteredUsers = computed(() => {
  const role = selectedRole.value.trim()
  const q = search.value.trim().toLowerCase()

  const list = props.users.filter(u => {
    const byRole = !role || u.role === role
    const byQuery = !q || (u.name?.toLowerCase().includes(q) || u.email?.toLowerCase().includes(q))
    return byRole && byQuery
  })

  return list.sort((a, b) => {
    const roleCmp = a.role.localeCompare(b.role)
    if (roleCmp !== 0) return roleCmp
    return a.name.localeCompare(b.name)
  })
})

const tasksForUser = (user) => props.tasks.filter(t => {
  if (t.assignedUserId) return t.assignedUserId === user.id
  return t.role === user.role
})

const removeUser = (id) => emit('remove', id)
</script>
