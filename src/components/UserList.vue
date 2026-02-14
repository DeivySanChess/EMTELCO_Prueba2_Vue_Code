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

    <!-- v-if / v-else: mensaje cuando no haya usuarios visibles -->
    <p v-if="filteredUsers.length === 0" class="muted">
      No hay usuarios visibles.
    </p>

    <table v-else>
      <thead>
        <tr>
          <th>Nombre</th>
          <th>Email</th>
          <th>Rol</th>
          <th style="width:140px;">Acción</th>
        </tr>
      </thead>

      <tbody>
        <!-- v-for para iterar -->
        <tr v-for="user in filteredUsers" :key="user.id">
          <td>{{ user.name }}</td>
          <td>{{ user.email }}</td>
          <td>
            <span class="badge">{{ user.role }}</span>
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

// Props: lista de usuarios
const props = defineProps({
  users: {
    type: Array,
    required: true,
    default: () => []
  }
})

// Evento al padre para eliminar (sin tocar el JSON real)
const emit = defineEmits(['remove'])

// Estado local (reactivo) para filtros
const selectedRole = ref('')
const search = ref('')

// Roles disponibles (derivados de los datos)
const roles = computed(() => {
  const set = new Set(props.users.map(u => u.role))
  return Array.from(set)
})

// Lista filtrada (computed => eficiente y claro)
const filteredUsers = computed(() => {
  const role = selectedRole.value.trim()
  const q = search.value.trim().toLowerCase()

  return props.users.filter(u => {
    const byRole = !role || u.role === role
    const byQuery = !q || (u.name?.toLowerCase().includes(q) || u.email?.toLowerCase().includes(q))
    return byRole && byQuery
  })
})

const removeUser = (id) => {
  emit('remove', id)
}
</script>
