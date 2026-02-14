<template>
  <div>
    <div class="todo-header">
      <div>
        <h2>Lista de tareas</h2>
        <p class="muted">Asigna por rol o a una persona específica.</p>
      </div>
      <span class="badge">
        Pendientes: <strong>{{ pendingCount }}</strong>
      </span>
    </div>

    <div class="row">
      <label>
        Nueva tarea:
        <input
          v-model="newTitle"
          placeholder="Ej: Programar onboarding"
          @keyup.enter="submit"
        />
      </label>

      <label>
        Rol responsable:
        <select v-model="newRole">
          <option value="">-- Selecciona rol --</option>
          <option v-for="role in roles" :key="role" :value="role">{{ role }}</option>
        </select>
      </label>

      <label>
        Asignar a usuario (opcional):
        <select v-model="newUserId">
          <option value="">Cualquiera con ese rol</option>
          <option
            v-for="user in usersByRole(newRole)"
            :key="user.id"
            :value="user.id"
          >
            {{ user.name }} ({{ user.role }})
          </option>
        </select>
      </label>

      <button class="btn" @click="submit">Agregar</button>
    </div>

    <p v-if="!tasks.length" class="muted">Aún no hay tareas.</p>

    <table v-else class="todo-table">
      <thead>
        <tr>
          <th style="width:50px;">Hecho</th>
          <th>Tarea</th>
          <th style="width:140px;">Rol</th>
          <th style="width:200px;">Asignada a</th>
          <th style="width:180px;">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in tasks" :key="task.id">
          <td>
            <input type="checkbox" :checked="task.done" @change="toggle(task.id)" />
          </td>
          <td>
            <div class="task-title" :class="{ done: task.done }">{{ task.title }}</div>
            <div class="task-note muted" v-if="task.done">Completada</div>
          </td>
          <td>
            <span class="badge">{{ task.role }}</span>
          </td>
          <td>
            <select v-model="assigneeSelection[task.id]">
              <option value="">Cualquiera ({{ task.role }})</option>
              <option
                v-for="user in usersByRole(task.role)"
                :key="user.id"
                :value="user.id"
              >
                {{ user.name }} ({{ user.role }})
              </option>
            </select>
          </td>
          <td>
            <div class="row" style="gap:6px; margin:0;">
              <button class="btn" @click="saveAssignee(task.id)">Guardar</button>
              <button class="btn btn-danger" @click="remove(task.id)">Eliminar</button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { computed, reactive, ref, watch } from 'vue'

const props = defineProps({
  tasks: { type: Array, required: true, default: () => [] },
  roles: { type: Array, required: true, default: () => [] },
  users: { type: Array, required: true, default: () => [] }
})

const emit = defineEmits(['toggle', 'add', 'update-assignee', 'remove'])

const newTitle = ref('')
const newRole = ref('')
const newUserId = ref('')

const assigneeSelection = reactive({})

watch(
  () => props.tasks,
  (tasks) => {
    tasks.forEach(t => {
      assigneeSelection[t.id] = t.assignedUserId ?? ''
    })
  },
  { immediate: true, deep: true }
)

const usersByRole = (role) => {
  if (!role) return props.users
  return props.users.filter(u => u.role === role)
}

const pendingCount = computed(() => props.tasks.filter(t => !t.done).length)

const submit = () => {
  if (!newTitle.value.trim() || !newRole.value) return
  emit('add', {
    title: newTitle.value,
    role: newRole.value,
    userId: newUserId.value ? Number(newUserId.value) : null
  })
  newTitle.value = ''
  newUserId.value = ''
}

const toggle = (id) => emit('toggle', id)

const saveAssignee = (taskId) => {
  const userId = assigneeSelection[taskId]
  emit('update-assignee', { id: taskId, userId: userId === '' ? null : Number(userId) })
}

const remove = (id) => emit('remove', id)
</script>
