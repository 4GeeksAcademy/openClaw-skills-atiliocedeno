# SKILL_LOG.md — Registro de Skills Creadas

> **Proyecto:** Skills para consumir la API de 4Geeks Academy (BreatheCode)
> **Cliente:** Atilio Cedeño (estudiante AI Engineering)
> **Fecha de creación:** 2026-09-10
> **Última modificación:** 2026-09-10

---

## Índice

1. [authenticate](#1-authenticate)
2. [get_projects](#2-get_projects)
3. [get_pending_work](#3-get_pending_work)
4. [get_progress_summary](#4-get_progress_summary)

---

## 1. authenticate

**Archivo:** `workshop-skills/authenticate/SKILL.md`

### Objetivo

Autenticar al usuario contra la API de 4Geeks Academy y proporcionar un token reutilizable para el resto de las skills.

### Origen de la conversación

**2026-09-10 04:42 UTC** — Atilio envía la especificación completa de la skill `authenticate`:

- Endpoint: `POST /v1/auth/login/`
- Credenciales desde variables de entorno `USER_4GEEK` y `PASS_4GEEK`
- Token se guarda en `memory/4geeks-token.json`
- Reutilización automática si el token sigue vigente
- Manejo de errores sin exponer credenciales

### Endpoint

```
POST https://breathecode.herokuapp.com/v1/auth/login/
Content-Type: application/json

{
  "email": "$USER_4GEEK",
  "password": "$PASS_4GEEK",
  "user_agent": "bc/student"
}
```

### Dependencias

- Ninguna. Es la skill raíz del sistema.
- **Obligatoria** antes de cualquier skill que requiera autenticación.

### Datos que persiste

Archivo `memory/4geeks-token.json` con:
- `token`, `user_id`, `email`, `expires_at`

### Prueba de concepto

✅ 2026-09-10 05:01 UTC — Autenticación exitosa contra la API. Token recibido y almacenado. Validez hasta 2026-09-12.

---

## 2. get_projects

**Archivo:** `workshop-skills/get-projects/SKILL.md`

### Objetivo

Obtener todos los proyectos asignados al estudiante autenticado en 4Geeks Academy.

### Origen de la conversación

**2026-09-10 04:45 UTC** — Atilio define que quiere 3 skills (GETs) separadas, una por cada endpoint.

**2026-09-10 04:52 UTC** — Atilio envía la especificación completa de `get_projects` (Skill 2):

- Endpoint: `GET /v1/assignment/user/me/task?task_type=PROJECT`
- Filtro mediante query parameter (no post-filtrado manual)
- Campos a conservar: id, title, task_status, revision_status, associated_slug, description, github_url, live_url, cohort.id, cohort.name, cohort.slug, y fechas asociadas
- Salida estructurada en `{ total, projects: [...] }`
- Relaciones futuras mediante `project.id`, `cohort.id`, `associated_slug`

### Endpoint

```
GET https://breathecode.herokuapp.com/v1/assignment/user/me/task?task_type=PROJECT
Authorization: Token <token>
Content-Type: application/json
```

### Dependencias

- `authenticate` (para obtener token)

### Integración

Alimenta a `get_pending_work` y `get_progress_summary`.

### Prueba de concepto

✅ 2026-09-10 05:01 UTC — 26 proyectos obtenidos exitosamente.

**Datos extraídos de la API real:**
- Campos disponibles: id, title, task_status, associated_slug, description, revision_status, github_url, live_url, task_type, opened_at, read_at, reviewed_at, delivered_at, cohort (id, name, slug), created_at, updated_at
- Estructura: `{ "count": N, "results": [...] }`
- task_status puede ser: PENDING, DONE, APPROVED, REJECTED

---

## 3. get_pending_work

**Archivo:** `workshop-skills/get-pending-work/SKILL.md`

### Objetivo

Obtener el trabajo que actualmente requiere atención del estudiante o que se encuentra pendiente de revisión en 4Geeks Academy.

### Origen de la conversación

**2026-09-10 04:55 UTC** — Atilio envía la especificación completa de `get_pending_work` (Skill 3):

- Endpoint: `GET /v1/assignment/user/me/task?revision_status=PENDING`
- Clasificación en PENDING_COMPLETION / PENDING_REVIEW / OTHER
- Relación con `get_projects` mediante id, associated_slug, cohort.id
- Incorporar flag `is_project: true/false` al relacionar con proyectos
- Presentación contextual según pregunta del usuario

### Endpoint

```
GET https://breathecode.herokuapp.com/v1/assignment/user/me/task?revision_status=PENDING
Authorization: Token <token>
Content-Type: application/json
```

### Dependencias

- `authenticate` (para token)
- `get_projects` (para relacionar proyectos)

### Reglas clave

- NO confundir `task_status` con `revision_status`
- `revision_status=PENDING` no implica automáticamente que el estudiante deba trabajar
- `task_status=DONE` + `revision_status=PENDING` = pendiente de revisión
- `task_status=PENDING` = pendiente de completar

### Integración

Alimenta a `get_progress_summary`.

### Prueba de concepto

✅ 2026-09-10 05:01 UTC — 52 tareas con `revision_status=PENDING`:
- 34 PENDING_COMPLETION
- 18 PENDING_REVIEW
- 0 OTHER

---

## 4. get_progress_summary

**Archivo:** `workshop-skills/get-progress-summary/SKILL.md`

### Objetivo

Generar un resumen global del progreso académico del estudiante en 4Geeks Academy.

### Origen de la conversación

**2026-09-10 04:56 UTC** — Atilio envía la especificación inicial que incluía el endpoint `/v1/activity/me`.

**2026-09-10 05:06 UTC** — Se prueba el endpoint de actividad y responde 403. El usuario no tiene permiso `read_activity` para ninguna academia.

**2026-09-10 05:11 UTC** — Atilio solicita modificar la Skill 4 para eliminar `/v1/activity/me`. Especifica métricas que incluyan: proyectos completados, pendientes, aprobados, rechazados, pendientes de revisión, ejercicios pendientes, lecciones pendientes. Priorización: PROJECT > EXERCISE > LESSON.

**2026-09-10 05:19 UTC** — Atilio pide eliminar completamente la Skill 4 y luego crearla de nuevo.

**2026-09-10 05:22 UTC** — Atilio define la versión final: una skill con **una sola responsabilidad**, usando únicamente:
- `GET /v1/assignment/user/me/task`
- Sin actividad, sin endpoints alternativos
- Métricas basadas exclusivamente en `task_status` y `revision_status`

### Endpoint

```
GET https://breathecode.herokuapp.com/v1/assignment/user/me/task
Authorization: Token <token>
Content-Type: application/json
```

### Dependencias

- `authenticate` (para token)
- NO modifica ni depende de `get_projects` ni `get_pending_work`

### Métricas que calcula

- Total de tareas
- Tareas completadas (DONE)
- Tareas pendientes (PENDING)
- Proyectos totales (task_type = PROJECT)
- Proyectos completados (PROJECT + DONE)
- Proyectos pendientes (PROJECT + PENDING)
- Pendientes de revisión (DONE + revision_status = PENDING)

### Visión final de Atilio

> *"Esta skill es la capa de presentación y consolidación del sistema."*

### Prueba de concepto

✅ 2026-09-10 05:25 UTC — Datos reales consultados desde la API:
- **126 tareas totales**
- **26 proyectos** (18 completados, 8 pendientes)
- **3 proyectos completados pendientes de revisión** (Milestone 2, Talk to the Machine, My Agent My Way)
- **Múltiples ejercicios y lecciones pendientes** distribuidas en 8 cohorts activas

---

## Arquitectura del Sistema

```
authenticate  ────────────►  memory/4geeks-token.json
    │
    ├──────────►  get_projects          ──►  { total, projects }
    │
    ├──────────►  get_pending_work      ──►  { summary, pending_completion, pending_review }
    │                    │
    │                    └── (relaciona con get_projects via id)
    │
    └──────────►  get_progress_summary  ──►  { summary, projects, pending_tasks, pending_review }
```

### Flujo de datos

1. `authenticate` obtiene token → lo guarda en `memory/4geeks-token.json`
2. `get_projects` usa token → filtra `task_type=PROJECT` → devuelve proyectos estructurados
3. `get_pending_work` usa token + datos de projects → filtra `revision_status=PENDING` → clasifica en completion/review → relaciona con projects
4. `get_progress_summary` usa token → consulta todas las tareas → calcula métricas → presenta resumen

### Claves de relación entre skills

En orden de prioridad:
1. `task.id` / `project.id`
2. `associated_slug`
3. `cohort.id`

Nunca relacionar por título.

---

## Notas técnicas

### API Base URL
```
https://breathecode.herokuapp.com/v1/
```

### Formato de respuesta típico
```json
{
  "count": 126,
  "first": null,
  "next": null,
  "previous": null,
  "last": null,
  "results": [ ... ]
}
```

### Campos relevantes por tarea
| Campo | Tipo | Ejemplo |
|-------|------|---------|
| id | int | 960329 |
| title | string | "Command Line Challenge" |
| task_status | string | PENDING / DONE / APPROVED / REJECTED |
| revision_status | string | PENDING / APPROVED |
| task_type | string | PROJECT / EXERCISE / LESSON |
| associated_slug | string | "exercise-terminal-challenge" |
| cohort.id | int | 1608 |
| cohort.name | string | "Command line - Git & Github" |
| cohort.slug | string | "command-line-git-github" |

### Estados → Significado
| task_status | revision_status | Significado |
|-------------|----------------|-------------|
| PENDING | (cualquiera) | Pendiente de completar |
| DONE | APPROVED | Completado y aprobado |
| DONE | PENDING | Completado, pendiente de revisión |
| APPROVED | (cualquiera) | Aprobado (no usado frecuentemente) |
| REJECTED | (cualquiera) | Rechazado |

---

## Conversaciones relacionadas

- **2026-09-10 04:42 UTC** — Atilio define spec de authenticate
- **2026-09-10 04:45 UTC** — Define estructura: 3 skills GET independientes
- **2026-09-10 04:52 UTC** — Spec de get_projects
- **2026-09-10 04:55 UTC** — Spec de get_pending_work
- **2026-09-10 04:56 UTC** — Spec inicial de get_progress_summary (con activity/me)
- **2026-09-10 05:01-05:05 UTC** — Pruebas de todos los endpoints
- **2026-09-10 05:06 UTC** — Activity/me retorna 403 (sin permiso)
- **2026-09-10 05:11 UTC** — Solicitud de modificación: eliminar activity/me
- **2026-09-10 05:19 UTC** — Eliminación completa de get_progress_summary
- **2026-09-10 05:22 UTC** — Creación final de get_progress_summary sin activity/me
- **2026-09-10 05:25 UTC** — Consulta en vivo de proyectos pendientes (8 proyectos)