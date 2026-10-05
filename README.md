# InsurDesk

Gestión de seguros y CRM comercial en un solo producto. Frontend React + Vite + Tailwind; backend Node + Express + SQLite.
Se vende por **funciones** (planes), no por usuarios ni pólizas.

```
web/      Frontend: portal + módulos del CRM (src/modules)
server/   API, autenticación, entidades con permisos, funciones, automatizaciones
          plans.json  → catálogo de funciones y planes
          modules/    → módulos del CRM (crm, automatizaciones, citas, ia, integraciones) y su kernel
```

## Arranque (Docker)
```bash
cp .env.example .env     # ADMIN_EMAIL, ADMIN_PASSWORD, APP_URL, correo, LICENSE_KEY
docker compose up -d --build
```
Abre `http://localhost:3001`. Los datos (SQLite, archivos y documentos) quedan en `./data`: **respáldalos**.

## Desarrollo
```bash
cd server && npm install && PLAN=nivel3 ADMIN_EMAIL=admin@local.dev ADMIN_PASSWORD=Admin12345 npm run dev
cd web && npm install && npm run dev      # http://localhost:5173
```

## Planes (`server/plans.json`)
| Función | Nivel 1 | Nivel 2 | Nivel 3 |
|---|---|---|---|
| Estructura de agentes | sin estructura | hasta 3 niveles | ilimitada |
| Reportes personalizados | — | ✔ | ✔ |
| CRM: prospectos, leads, tareas, calendario, automatizaciones | ✔ | ✔ | ✔ |
| CRM: pipelines | 1 | ilimitados | ilimitados |
| CRM: integraciones (Meta, WhatsApp, formularios web, Google Calendar, reserva pública) | — | ✔ | ✔ |
| Inteligencia artificial (asistente, Radar) | — | — | ✔ |

Cada nivel incluye lo del anterior. El servidor bloquea (403) lo que el plan no incluye; la interfaz además lo oculta.
Licencias: claves firmadas `GA1.…` (herramienta del proveedor aparte) en *Admin → Plan y módulos* o `LICENSE_KEY`.
Sin licencia válida, en producción funciona como Nivel 1.

## Un solo sistema de usuarios
Un único `User` con perfil y un único rol por usuario (`app_role`). El rol dentro del CRM se **deriva** de él
(admin→admin_crm, gerencial/jefatura→jefatura_crm, agente→agente_crm) y el acceso se concede con el interruptor
"CRM" del usuario (los administradores siempre). Invitaciones, MFA y recuperación de contraseña son los del portal.

## Permisos por rol
La matriz central es `server/policies.json` (rol × entidad × operación, con alcances `all`, `own`, `team`, `team_or_all`).
`PERMISOS.md` la muestra legible y se regenera con `cd server && npm run permisos`. Lo que no tenga regla queda **solo para
administradores** (seguro por defecto). Pagos, renovaciones y documentos se filtran por la póliza del agente; clientes por
creador o por pólizas de su agente/estructura; en el CRM, cada agente ve solo sus prospectos y leads.

## Visibilidad por estructura (upline)
Cada quien ve **hacia abajo** de su estructura y nunca hacia arriba: un agente ve sus propios registros y los de todos sus
subagentes (pólizas, comisiones, pagos, clientes, agentes); no ve a su upline ni a agentes de otras ramas. La cabeza de una
estructura ve todo lo de sus subagentes. En el Nivel 1 (sin estructura) cada agente ve solo lo suyo.
**Los agentes son de solo lectura en el portal**: no crean ni editan pólizas, clientes, pagos ni renovaciones. Solo crean
prospectos y leads (y su actividad) en el CRM. Gerencia/jefatura vinculadas a un agente se limitan a su estructura; si no
están vinculadas, ven todo.

## Importaciones
- **Importación inicial** (una sola vez): admite enlazar por nombre. Al terminar, en *Admin → Importación de datos del sistema*:
  **Revisar relaciones → Reparar relaciones → Cerrar importación inicial**. El cierre solo procede si no queda nada pendiente.
- **Importaciones posteriores** (incrementales): el archivo debe traer el **ID o código único** de compañía, ramo, producto y agente
  (columnas `company_id`, `line_id`, `product_id`, `agent_id`; también valen `CIA-0001`, `AGT-0001`…). No se adivina nada por
  nombre: el importador valida que existan y que producto, compañía y ramo sean coherentes entre sí.
- Tras el cierre, **el servidor** rechaza (422) cualquier escritura de pólizas, productos, comisiones, pagos, siniestros,
  endosos, tablas, presupuestos o asignaciones que no traiga sus ids clave, venga de donde venga. Siempre (aun antes del
  cierre) rechaza ids que no existen. Se puede reabrir si hace falta otra carga inicial.

## Integridad de relaciones
El id es la verdad; el nombre guardado junto al id es solo una copia para mostrar.
- Compañías, ramos, sucursales, productos y agentes reciben un **código único e inmutable** (`CIA-0001`, `RAM-0001`, `SUC-`, `PRD-`, `AGT-`).
- Al **renombrar** un maestro, el servidor actualiza todas las copias (pólizas, comisiones, tablas, presupuestos, metas…).
- No se puede **borrar** lo que está en uso (409 con el detalle); se marca como inactivo.
- Nombres únicos (sin distinguir mayúsculas) en compañías, ramos, sucursales y productos (por compañía y ramo).
- Datos importados o heredados: `POST /api/functions/repairRelations` (admin). Por defecto solo simula (`dryRun: true`);
  con `{ "dryRun": false }` enlaza por id lo que solo tenía nombre, asigna códigos y refresca nombres. Es idempotente.

## Seguridad
- Cuentas nuevas sin rol activo no ven datos. Solo un administrador cambia roles.
- Credenciales de terceros (Meta, WhatsApp) solo para administradores; los tokens de Google nunca salen al navegador.
- `ALLOW_REGISTRATION=false` para operar solo por invitación. Detrás de HTTPS.

## Pendiente (no incluido aún)
- Conversión prospecto → cliente al ganar una venta (hoy el lead conserva campos sin uso `gportal_*`).
- Los demás importadores (catálogos en Importación de datos del sistema, agentes desde Excel, auditoría) siguen enlazando por nombre: sirven para la carga inicial; tras cerrarla hay que pasarlos a ids (solo el importador de pólizas ya lo hace).
- En los detalles de póliza y cliente, los botones de miembros y dependientes aún se muestran a los agentes aunque el servidor ya los rechaza.
- Unificar Tareas (portal) con Seguimientos (CRM), y metas del embudo con metas de producción.
- Un solo asistente de IA con las herramientas del portal y del CRM.
- Multi-empresa (SaaS): hoy una instalación = una empresa.
- Pruebas reales de Meta, WhatsApp, Google Calendar y del asistente de IA con claves.
- Logo en PDF y plantillas de correo del CRM con el estilo anterior.
