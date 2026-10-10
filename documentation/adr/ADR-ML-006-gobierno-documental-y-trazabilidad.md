# ADR-ML-006 — Gobierno documental y trazabilidad de cambios

- **Estado:** aceptada
- **Fecha:** 2026-10-10
- **Decisores:** equipo de Manager Library

## Contexto

El primer objetivo del proyecto exige una documentación general que refleje los cambios y funcionalidades implementadas. Sin una estructura común, la documentación se desvincula de los requisitos y no permite determinar qué se construyó ni con qué evidencia.

## Decisión

El repositorio `docs` mantendrá, como mínimo:

```text
docs/
├── architecture/     # adaptación Onion y límites por repositorio
├── adr/              # decisiones con contexto, alternativas y verificación
├── changes/          # registro cronológico de cambios
├── features/         # detalle por HU o funcionalidad relevante
└── quality/          # trazabilidad, evidencias y ejecución de pruebas
```

Cada funcionalidad terminada registra: estado, requisitos HU/CA, repositorios afectados, decisión técnica, cambios en API/datos, pruebas y evidencia, y pendientes. Las decisiones difíciles de revertir se expresan como ADR antes o junto a la implementación.

## Consecuencias

- Ninguna tarea T-01 a T-23 se marca terminada sin una evidencia ejecutable o verificable.
- Los cambios de contrato, migración o arquitectura se pueden revisar antes de integrarse.
- El trabajo de Antigravity queda auditable por el equipo sin leer todo el código.

## Verificación

- Existe una matriz que enlaza HU/CA con implementación, pruebas y estado.
- Cada cambio integrado aparece en `changes/` y, cuando corresponde, tiene un ADR enlazado.
- La documentación de instalación permite levantar y demostrar el proyecto sin conocimiento implícito.
