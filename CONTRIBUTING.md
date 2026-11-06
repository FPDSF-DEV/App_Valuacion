# Cómo contribuir

Esto vale igual para una persona y para un agente. Las reglas completas están en
[`CLAUDE.md`](CLAUDE.md); aquí está el **flujo de trabajo**.

## 1. Antes de empezar

1. **Lea [`proyecto/gestion/estado_actual.md`](proyecto/gestion/estado_actual.md).** Es
   obligatorio. Dice cuál fue la última tarea, cuál está en curso, cuál es el siguiente paso y qué
   está bloqueado. Sin leerlo se duplica o se destruye trabajo ajeno.
2. Busque la tarea en el [backlog](proyecto/plan/FASE_5_IMPLEMENTACION/5.0_backlog_maestro.md) y
   revise las [decisiones](proyecto/plan/DECISIONES/README.md) que la afectan.
3. Lea el documento de [`app/docs/`](app/docs/README.md) del área que vaya a tocar.

## 2. Qué manda sobre qué

```
especificacion/teoria  (v2.1; en el motor manda ANEXO_C)
        ▼
proyecto/plan/DECISIONES  (ADR cerrados)
        ▼
proyecto/plan  ·  app/docs  ·  el código
```

- **El código nunca contradice la teoría.** Si la teoría se contradice a sí misma, se documenta como
  `CT-*` en [`1.4_contradicciones_teoria.md`](proyecto/plan/FASE_1_ANALISIS/1.4_contradicciones_teoria.md).
  Si hay que corregirla, se pone nota al pie y entrada en
  [`CORRECCIONES.md`](especificacion/teoria/CORRECCIONES.md), y **solo con decisión del
  propietario**. Nunca se corrige en silencio.
- Lo que la teoría describe y la app no hace se marca `FUERA_DE_ALCANCE` en
  `app/docs/trazabilidad.csv`. No se contradice.
- **Los ADR están cerrados.** Si aparece información nueva, se abre un ADR nuevo con el número
  siguiente; los anteriores no se reabren.

## 3. Mientras trabaja

### Reglas de código

| Regla | Por qué |
|---|---|
| Todo en **español**: código, tablas, enums, documentación | Igual que la teoría. Solo las APIs de librerías y las palabras clave quedan en inglés |
| **Dinero en enteros de centavos** en la base y `decimal.js` en el motor. Nunca un `number` de pesos | ADR-006 |
| `src/compartido/motor/` es **dominio puro** | No importa `fs`, `electron`, `sqlite`, `react` ni `Date`. Lo vigila `npm run boundaries` |
| La **aritmética vive en el motor**, no en los casos de uso | `main/modules/*` orquesta |
| **Un cálculo que no se pudo hacer nunca es un cero** | Se devuelve `NO_APLICA`, `NO_CALCULABLE` o `ERROR_DATOS` con su motivo |
| **La integridad va en disparadores**, no en formularios | Cortes, barridos, bitácora y procesos finalizados son inmutables también frente a SQL directo |
| **Sin login, usuarios ni catálogo de responsables** | ADR-016, ADR-027 |
| **El inventario entra solo por `PL-03`** | ADR-015: la app no se usa en campo |
| **Versiones exactas** de dependencias | Cambiar una exige entrada en la bitácora; cambiar la major de Electron, el procedimiento de ADR-002 |

### Tests del motor

Los valores esperados de `src/compartido/motor/*.test.ts` salen de `ANEXO_C`. **Ninguno se modifica
sin justificarlo en la bitácora** (riesgo `RG-01`). Si se ajustan al código, el motor queda validado
contra sí mismo, y sus errores terminan en resoluciones firmadas. Si un test del motor falla, se
corrige el código.

### Migraciones

Siga [app/docs/10 § Añadir una migración](app/docs/10_CONSTRUIR_Y_DISTRIBUIR.md#añadir-una-migración).
Resumen:

1. Prefiera migraciones **aditivas**. SQLite no deja eliminar una columna que participa en una FK.
2. Dele al archivo un nombre descriptivo.
3. Ejecute `npm run db:triggers` si la migración toca tablas.
4. Pruébela sobre una base **con datos**.

### Desarrollo

Use `npm run dev:aislado`. `npm run dev` escribe en la base de la aplicación instalada. Ver
[`app/README.md`](app/README.md).

## 4. Antes de cerrar una tarea

```bash
cd app
npm run verificar:todo        # obligatorio
npm run test:rendimiento      # si tocó importación, cálculo o consultas masivas
npm run build && npm run test:e2e   # si tocó la interfaz, el arranque o el IPC
```

Además:

- Si cambió el código, actualice el documento correspondiente de `app/docs/`, con cifras contadas
  sobre el código y no de memoria.
- Si cambió un importador, regenere `npm run instructivo`. Si cambió el formato de las plantillas,
  regenere `npm run datos:prueba`.
- **Actualice los tres archivos de [`proyecto/gestion/`](proyecto/gestion/)**:
  `estado_actual.md`, `roadmap_progreso.md` y `bitacora.md`. Es obligatorio en cada sesión, y el
  protocolo está en la skill `gestion-memoria`.

## 5. Commits y ramas

- No trabaje directamente sobre `main`: use una rama.
- Escriba mensajes en estilo *conventional commits* (`feat:`, `fix:`, `docs:`, `refactor:`,
  `test:`), con una descripción breve y concreta.
- Haga un commit por unidad lógica. No mezcle una reorganización con un cambio de comportamiento.
- La CI ([`.github/workflows/ci.yml`](.github/workflows/ci.yml)) repite la puerta de calidad,
  rendimiento, `pack`, E2E y `npm audit`.

## 6. Datos

Nunca suba datos de un hospital real. Para probar, use
[`datos_de_prueba/`](datos_de_prueba/LEEME.md) (ficticio) o `scripts/sembrar.ts`.
