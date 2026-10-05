# Bugs Frontend

## FE-01  BACKEND_URL de .env.example apunta al puerto 3005
- Dónde: .env.example:2
- Problema: El backend corre en http://localhost:3000; con 3005 el proxy /api/... no conecta y toda llamada da 502 'No se pudo conectar con el servidor'.
- Solución: BACKEND_URL=http://localhost:3000
- Cómo demostrarlo: cp .env.example .env.local; npm run dev; login → antes 502, después entra al panel.
- Estado: verificado

## FE-02  proxy solo protegía la raíz de cada área por rol
- Dónde: src/proxy.ts:20
- Problema: La comparación pathname === p solo cubría /admin, /docente y /estudiante exactos; un estudiante podía abrir /admin/usuarios o /docente/grupos por URL.
- Solución: Coincidir también con subrutas: pathname.startsWith(p + '/').
- Cómo demostrarlo: Login como juliana.herrera147 → abrir /admin/usuarios. Antes carga la pantalla; después redirige a /estudiante.
- Estado: corregido sin verificar

## FE-03  Etiqueta del lunes mal escrita
- Dónde: src/lib/format.ts:6
- Problema: DAY_LABEL.lunes era 'Lrrrrunes'; aparece en horarios y formularios de grupos.
- Solución: Cambiar a 'Lunes'.
- Cómo demostrarlo: Estudiante → Horario / Admin → Grupos (días). Antes 'Lrrrrunes', después 'Lunes'.
- Estado: corregido sin verificar

## FE-04  Notas truncadas en vez de redondeadas
- Dónde: src/lib/format.ts:17 (grade)
- Problema: Math.floor trunca: 2.96 se mostraba 2.9 (parece reprobado aunque es 3.0) y 4.46 como 4.4.
- Solución: Usar Math.round(value*10)/10.
- Cómo demostrarlo: Ver una nota con dos decimales (p. ej. definitiva 2.96) en Notas/planilla: antes 2.9, después 3.0.
- Estado: corregido sin verificar

## FE-05  Estado reprobada mostrado como 'Aprobada'
- Dónde: src/lib/format.ts:25 (STATUS_LABEL)
- Problema: La matrícula reprobada tenía la etiqueta 'Aprobada', engañando al estudiante en historial y materias.
- Solución: reprobada: 'Reprobada'.
- Cómo demostrarlo: Estudiante con una materia reprobada → Historial. Antes 'Aprobada' (en rojo), después 'Reprobada'.
- Estado: corregido sin verificar

## FE-06  Panel admin muestra estudiantes en 'Docentes activos'
- Dónde: src/app/(app)/admin/page.tsx:23
- Problema: La tarjeta Docentes activos usaba d.active.students; la API devuelve active.teachers (99) y se mostraba 97.
- Solución: value={d.active.teachers}
- Cómo demostrarlo: Admin → Inicio. Antes Docentes activos = 97 (igual que estudiantes); después 99 (GET /api/reports/dashboard → active.teachers).
- Estado: corregido sin verificar

## FE-07  Matrículas por estado del periodo siempre en 0
- Dónde: src/app/(app)/admin/page.tsx:58
- Problema: Se indexaba enrollmentsByStatus con la etiqueta ('Activas') en vez de la clave del API ('activa'), así que todos los contadores salían 0.
- Solución: Usar enrollmentsByStatus[key].
- Cómo demostrarlo: Admin → Inicio con un periodo abierto: antes 0/0/0/0, después los conteos reales por estado.
- Estado: corregido sin verificar

## FE-08  Saludo del estudiante usa el apellido
- Dónde: src/app/(app)/estudiante/page.tsx:22
- Problema: split(' ')[1] toma la segunda palabra: 'Hola, Herrera' en vez de 'Hola, Juliana' (el docente usa [0]).
- Solución: split(' ')[0].
- Cómo demostrarlo: Login juliana.herrera147 → Inicio. Antes 'Hola, Herrera', después 'Hola, Juliana'.
- Estado: corregido sin verificar

## FE-09  Botón 'Guardar nombre' nunca se habilita
- Dónde: src/app/(app)/cuenta/account-forms.tsx:79
- Problema: El estado dirty nunca se ponía en true, y el botón exige dirty, así que no se podía cambiar el nombre.
- Solución: Marcar dirty al editar el campo (setDirty(true) en onChange).
- Cómo demostrarlo: Mi cuenta → cambiar el nombre. Antes el botón sigue deshabilitado; después se habilita y PATCH /api/users/me guarda.
- Estado: corregido sin verificar

## FE-10  Menú lateral con texto blanco sobre fondo blanco
- Dónde: src/components/app-shell.tsx:83, :106, :112
- Problema: Los enlaces inactivos y los títulos de sección usaban text-white sobre bg-surface (#fff): el menú era invisible salvo el ítem activo.
- Solución: Usar text-muted (como el resto de textos secundarios).
- Cómo demostrarlo: Entrar con cualquier rol: antes solo se lee el ítem activo; después se ven todos los ítems y secciones.
- Estado: corregido sin verificar

## FE-11  Cancelar matrícula usa PATCH en vez de POST
- Dónde: src/app/(app)/estudiante/materias/cancel-button.tsx:17
- Problema: El backend expone POST /api/enrollments/:id/cancel (Swagger); con PATCH responde 404 y el estudiante no puede cancelar.
- Solución: method: 'POST'.
- Cómo demostrarlo: Estudiante → Mis materias → Cancelar → Sí, cancelar. Antes error 404 'Cannot PATCH'; después la matrícula queda cancelada.
- Estado: corregido sin verificar

## FE-12  Mis materias no se actualiza tras cancelar
- Dónde: src/app/(app)/estudiante/materias/cancel-button.tsx:22
- Problema: Tras cancelar con éxito no se recargaban los datos del servidor: la materia seguía como 'En curso' con botón Cancelar hasta recargar a mano.
- Solución: router.refresh() después de cancelar.
- Cómo demostrarlo: Cancelar una matrícula propia: antes sigue 'En curso'; después pasa a 'Cancelada' sin recargar.
- Estado: corregido sin verificar

## FE-13  Mis materias ordena periodos del más viejo al más reciente
- Dónde: src/app/(app)/estudiante/materias/page.tsx:22
- Problema: El comentario y la UI exigen 'el más reciente primero', pero a.localeCompare(b) ordenaba ascendente (igual que Notas que sí usa b-a).
- Solución: b.localeCompare(a).
- Cómo demostrarlo: Estudiante con varios periodos → Mis materias: antes el periodo más antiguo arriba; después el actual arriba.
- Estado: corregido sin verificar

## FE-14  Nota 3.0 pintada como reprobada en Mis notas
- Dónde: src/app/(app)/estudiante/notas/page.tsx:73
- Problema: value <= PASSING marca en rojo un 3.0, pero se aprueba con 3.0 o más (README y subtítulo de la pantalla).
- Solución: value < PASSING.
- Cómo demostrarlo: Evaluación con nota 3.0 → antes en rojo; después en verde.
- Estado: corregido sin verificar

## FE-15  Acumulado de notas sin ponderar por peso
- Dónde: src/app/(app)/estudiante/notas/page.tsx:40
- Problema: El acumulado era el promedio simple de las notas, ignorando el peso de cada evaluación; el backend (academic.service average) pondera por peso.
- Solución: Promedio ponderado: suma(nota*peso)/suma(pesos evaluados).
- Cómo demostrarlo: Grupo con evaluaciones 20% nota 5.0 y 30% nota 2.0: antes 3.50, después 3.20 (igual que la planilla del docente).
- Estado: corregido sin verificar

## FE-16  Clases del sábado aparecen en el viernes
- Dónde: src/components/week-schedule.tsx:15
- Problema: Math.min(indice, 4) mandaba las clases del sábado a la columna del viernes y la tarjeta Sábado siempre decía 'Sin clases'.
- Solución: Cada columna muestra byDay[day].
- Cómo demostrarlo: Horario (estudiante o docente) con clase en sábado: antes aparece bajo Viernes; después bajo Sábado.
- Estado: corregido sin verificar

## FE-17  Matrícula del estudiante envía 'group' en vez de 'groupId'
- Dónde: src/app/(app)/estudiante/matricula/enroll-view.tsx:50
- Problema: CreateEnrollmentDto del backend exige groupId; con 'group' responde 400 'property group should not exist. groupId must be a mongodb id' y nadie puede matricularse.
- Solución: body: { groupId: g.group }.
- Cómo demostrarlo: POST /api/enrollments {group:...} → 400 (evidencia con curl). Después: Matricular → 'Quedaste matriculado…'.
- Estado: corregido sin verificar (400 reproducido con curl)

## FE-18  Admin: matricular envía 'group' en vez de 'groupId'
- Dónde: src/components/admin/operations.tsx:337 (enrollments.toBody)
- Problema: Mismo contrato: el backend espera groupId; el formulario 'Matricular estudiante' siempre fallaba con 400.
- Solución: toBody → { student, groupId }.
- Cómo demostrarlo: Admin → Matrículas → Matricular estudiante: antes 400; después crea la matrícula.
- Estado: corregido sin verificar

## FE-19  Admin: cancelar matrícula usa PATCH (cambio incluido en el commit de FE-18) + Docente: cupo invertido
- Dónde: src/components/admin/operations.tsx:346 y src/app/(app)/docente/grupos/page.tsx:52
- Problema: (a) Admin cancelaba con PATCH /enrollments/:id/cancel → 404 'Cannot PATCH' (verificado con curl); el backend es POST. (b) En Mis grupos del docente se mostraba 'capacidad / matriculados' (ej. '40 / 12 estudiantes').
- Solución: (a) method POST. (b) '{enrolled} / {capacity} estudiantes'.
- Cómo demostrarlo: (a) Admin → Matrículas → acción Cancelar: antes 404, después cancela. (b) Docente → Mis grupos: antes '40 / 12', después '12 / 40'.
- Estado: corregido sin verificar

## FE-20  Mis grupos (docente) no filtra por el periodo abierto por defecto
- Dónde: src/app/(app)/docente/grupos/page.tsx:21
- Problema: Sin ?period en la URL el selector muestra el periodo abierto, pero la consulta solo filtraba si 'requested' venía en la URL: se listaban grupos de todos los periodos.
- Solución: Filtrar por 'selected' (periodo abierto por defecto) salvo 'todos'.
- Cómo demostrarlo: Docente → Mis grupos sin parámetros: antes aparecen grupos de periodos cerrados; después solo los del periodo abierto (igual que el Inicio del docente).
- Estado: corregido sin verificar

