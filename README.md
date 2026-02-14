# Prueba Técnica 2 — EMTELCO (Vue 3 + Vite)

Aplicación SPA que gestiona usuarios de un JSON local y una lista de tareas con contexto por rol y asignación individual.

## Stack
- Vue 3 + Vite
- CSS plano (tema inspirado en la paleta índigo/cian de EMTELCO)

## Características principales
- **Usuarios**: carga con `fetch` desde `src/data/users.json`, filtros por rol y búsqueda, eliminación solo visual, restauración de usuarios eliminados.
- **Tareas**: lista tipo to‑do con crear, marcar como hecha, reasignar a un usuario o dejar “cualquiera (rol)”, eliminar tarea.
- **Contexto rol/usuario**: las tareas se muestran tanto por rol como por usuario asignado.
- **Feedback**: avisos de confirmación en la sección de tareas y globales.
- **Seed de datos**:
  - Usuarios de ejemplo en `src/data/users.json`.
  - Tareas iniciales en `src/App.vue` (array `tasks`).

## Scripts
```bash
npm install      # instalar dependencias
npm run dev      # modo desarrollo (http://localhost:5173)
npm run build    # build de producción
npm run preview  # vista previa del build
```

## Estructura relevante
- `src/App.vue` — estado principal, tareas seed, lógica de usuarios ocultos/restauración y notificaciones.
- `src/components/UserList.vue` — tabla de usuarios con filtros y tareas asociadas.
- `src/components/TodoList.vue` — alta/edición/asignación/eliminación de tareas y confirmaciones inline.
- `src/data/users.json` — base de usuarios de ejemplo.
- `src/styles.css` — tema y layout inspirados en EMTELCO.

## Notas de uso
- Eliminar usuarios solo afecta la vista; se pueden restaurar sin recargar.
- Para que una tarea quede para una persona específica, asigna `assignedUserId` al `id` del usuario y usa el `role` correspondiente.
- Si agregas más usuarios o roles en `users.json`, se reflejan automáticamente en filtros y asignaciones.
