# Prueba Técnica 2 — Desarrollo Web con Vue.js (EMTELCO)

Aplicación en Vue 3 + Vite que:
- Carga usuarios desde un JSON local usando `fetch`
- Guarda los datos en estado reactivo
- Muestra usuarios en tabla (`v-for`)
- Filtra por rol (select) y búsqueda simple
- Permite eliminar usuarios solo de la vista (no modifica el JSON real)
- Muestra mensaje cuando no haya usuarios visibles (`v-if / v-else`)
- Incluye un componente separado: `UserList.vue`

## Requisitos
- Node.js 18+ recomendado

## Instalación
```bash
npm install
```

## Ejecutar en desarrollo
```bash
npm run dev
```

Abre la URL que te muestre la consola (normalmente http://localhost:5173).

## Construir para producción
```bash
npm run build
```

## Vista previa del build
```bash
npm run preview
```

⚙️ Funcionalidades implementadas
1. Carga de datos

Consumo del archivo users.json mediante fetch

Almacenamiento de los datos en estado reactivo (ref)

Manejo básico de estado de carga

2. Visualización

Listado de usuarios en tabla

Uso de v-for con key

Visualización de:

Nombre

Email

Rol

3. Filtro simple

Filtro por rol (admin / user) mediante select

Implementado con propiedades computadas (computed)

4. Interacción básica

Botón para eliminar usuarios solo de la vista

No se modifica el archivo JSON original

Mensaje informativo cuando no hay usuarios visibles (v-if / v-else)

🧠 Manejo del estado

El estado principal se mantiene en App.vue

El componente UserList.vue recibe datos por props

La eliminación de usuarios se comunica mediante eventos (emit)

No se utilizan variables globales

▶️ Instalación y ejecución
Requisitos

Node.js 18 o superior

Pasos
npm install
npm run dev


Luego abrir en el navegador:

http://localhost:5173

📌 Consideraciones técnicas

La eliminación de usuarios es visual, tal como se solicita en la prueba

El proyecto no depende de backend ni base de datos

Se priorizó claridad, legibilidad y buenas prácticas sobre complejidad innecesaria

👤 Autor

Deivy Sebastián Sánchez Echeverri
Tecnólogo en Análisis y Desarrollo de Software
Practicante – EMTELCO

✅ Estado del proyecto

✔️ Prueba técnica completada
✔️ Requisitos funcionales y técnicos cumplidos