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

