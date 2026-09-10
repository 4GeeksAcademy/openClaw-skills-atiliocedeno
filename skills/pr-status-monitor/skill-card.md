## Description: <br>
Skill personalizado para revisar automáticamente el estado de un Pull Request cada 5 horas, verificando checks de CI/CD, mergeability, conflictos y el estado general del PR. <br>

This skill is ready for commercial/non-commercial use. <br>

## Publisher: <br>
[Atilio Cedeño](https://example.com) <br>

### License/Terms of Use: <br>

## Use Case: <br>
Desarrolladores y equipos de QA usan este skill para monitorear el estado de un PR entregado sin revisar manualmente cada cambio. <br>

### Deployment Geography for Use: <br>
Global <br>

## Known Risks and Mitigations: <br>
Risk: El skill dependerá del acceso validado del usuario a GitHub y del repositorio correcto. <br>
Mitigation: Verificar `gh auth status`, confirmar el repositorio y el número del PR antes de emitir un estado final. <br>

## Skill Output: <br>
**Output Type(s):** [Shell commands, API Calls, Guidance] <br>
**Output Format:** [Markdown summary with status checks] <br>
**Output Parameters:** [1D] <br>

## Skill Version(s): <br>
1.0.0 <br>
