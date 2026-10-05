# Bugs Frontend

## FE-01  BACKEND_URL de .env.example apunta al puerto 3005
- Dónde: .env.example:2
- Problema: El backend corre en http://localhost:3000; con 3005 el proxy /api/... no conecta y toda llamada da 502 'No se pudo conectar con el servidor'.
- Solución: BACKEND_URL=http://localhost:3000
- Cómo demostrarlo: cp .env.example .env.local; npm run dev; login → antes 502, después entra al panel.
- Estado: verificado

