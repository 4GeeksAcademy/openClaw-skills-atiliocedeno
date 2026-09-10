---
name: pr-status-monitor
description: "Revisa el PR entregado cada 5 horas, verifica el estado de revisión, los checks de CI, los conflictos y entrega un resumen claro del estado actual del pull request."
---

# Skill Personalizado: PR Status Monitor

Este skill está pensado para vigilar un Pull Request entregado y reportar su estado cada 5 horas sin necesidad de revisarlo manualmente.

## Objetivo

Cada 5 horas debes:

1. Identificar el repositorio y el PR activo.
2. Revisar el estado actual del PR.
3. Consultar el estado de los checks de CI/CD.
4. Verificar si hay conflictos o bloqueos.
5. Entregar un resumen claro con el estado actual y la próxima acción sugerida.

## Flujo de revisión

Usa `gh` para consultar el PR y su estado:

```bash
gh pr view <numero-pr> --repo <owner>/<repo> --json number,title,state,author,mergeable,updatedAt
```

```bash
gh pr checks <numero-pr> --repo <owner>/<repo>
```

```bash
gh pr status --repo <owner>/<repo>
```

Si necesitas información adicional:

```bash
gh api repos/<owner>/<repo>/pulls/<numero-pr> --jq '{number, title, state, draft, mergeable, user: .user.login, base: .base.ref, head: .head.ref}'
```

## Regla de salida

Genera un reporte en español con este formato:

```markdown
## Estado del PR
- PR: #<numero>
- Título: <titulo>
- Estado: <open/closed/merged>
- Mergeable: <yes/no/unknown>
- Revisado hace: <tiempo>

## Checks de CI/CD
- <check 1>: <success/failure/pending>
- <check 2>: <success/failure/pending>

## Observaciones
- <si hay conflictos, bloqueos, cambios pendientes o revisión requerida>

## Conclusión
- <está listo, en revisión, bloqueado o necesita cambios>
```

## Interpretación del resultado

- Si el PR está `open` y todos los checks pasan, indica que está listo o casi listo.
- Si hay checks fallando, reporta que está bloqueado.
- Si el PR está `draft`, indica que todavía no está listo para revisión.
- Si `mergeable` es `false`, menciona que hay conflicto de merge.
- Si `state` es `closed` o `merged`, señala que ya no requiere seguimiento.

## Sugerencia de frecuencia

Este skill debe ejecutarse cada 5 horas para mantener un seguimiento constante del PR entregado y avisar sobre cambios importantes de estado.
