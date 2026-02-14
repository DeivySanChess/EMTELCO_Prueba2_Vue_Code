<template>
  <div>
    <div class="todo-header">
      <div>
        <h2>Lista de tareas</h2>
        <p class="muted">Contextualiza pendientes según el rol para priorizar.</p>
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
          <option v-for="role in roles" :key="role" :value="role">{{ role }}</option>
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
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { computed, ref, watchEffect } from 'vue'

const props = defineProps({
  tasks: { type: Array, required: true, default: () => [] },
  roles: { type: Array, required: true, default: () => [] }
})

const emit = defineEmits(['toggle', 'add'])

const newTitle = ref('')
const newRole = ref('')

watchEffect(() => {
  if (!newRole.value && props.roles.length) {
    newRole.value = props.roles[0]
  }
})

const pendingCount = computed(() => props.tasks.filter(t => !t.done).length)

const submit = () => {
  if (!newTitle.value.trim()) return
  emit('add', { title: newTitle.value, role: newRole.value })
  newTitle.value = ''
}

const toggle = (id) => emit('toggle', id)
</script>
