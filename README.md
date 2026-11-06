# Valuación de Activos

Aplicación de escritorio para que el responsable de activos de una **E.S.E** colombiana (hospital
público) valore sus bienes **cuando quiera**. Carga el inventario de un barrido y calcula la
depreciación y la obsolescencia a una fecha de corte. Muestra qué bienes son candidatos a baja y
entrega el informe en PDF.

- **Windows, sin servidor ni nube.** Todo queda en el equipo del hospital.
- **Sin inicio de sesión ni usuarios** (ADR-016, ADR-027). Es monousuario. En lugar de permisos,
  guarda una bitácora inmutable de cada cambio.
- **Se cede a los hospitales.** El hospital la instala, la usa por su cuenta y se lleva su informe.

> **Estado:** versión `0.1.0`, en desarrollo. Desde el 2026-09-24 rigen
> [ADR-028 y ADR-029](proyecto/plan/DECISIONES/adr_producto.md): la unidad de trabajo es el
> **proceso de valuación** y la aplicación no depende de trámites externos (comité, actas, firmas).
> El estado detallado está en [`proyecto/gestion/estado_actual.md`](proyecto/gestion/estado_actual.md),
> y si este README lo contradice, manda ese archivo.

---

## Cómo funciona

Lo primero que muestra la aplicación es la **lista de procesos**: iniciar uno nuevo o continuar uno
en curso. Cada proceso es independiente y tiene sus propios datos del hospital, su catálogo, su
inventario y su cálculo.

```
Iniciar proceso ─► Configurar ─► Formatos ─► Inventario ─► Calcular ─► Bajas ─► Informe ─► Finalizar
 (nombre, fecha     (sedes,       (descargar  (importar     (a la      (registrar  (PDF     (solo
  de corte,          servicios,    plantillas  PL-03 y       fecha de   las que se  Carta)   lectura)
  hospital o PL-01)  clases,       Excel)      PL-05)        corte)     decidieron)
                     parámetros)
```

| Qué | Cómo lo resuelve |
|---|---|
| **Inventario** | Entra **solo** por importación de la plantilla `PL-03` (ADR-015). La app no se usa en campo. Cada importación es un **barrido**: crea lo nuevo, actualiza lo conocido y marca como *no encontrado* lo que falta |
| **Cálculo** | Depreciación en línea recta con tope en la base depreciable, índice de obsolescencia y semáforo (verde, amarillo, naranja, rojo). Hay **un cálculo por proceso**: recalcular lo reemplaza |
| **Lo que no se puede calcular** | **Nunca es un cero.** Sale como `NO_APLICA`, `NO_CALCULABLE` o `ERROR_DATOS` con su motivo, y se lista aparte |
| **Bajas** | El motor **propone** candidatos con su motivo. La persona decide por el trámite de su entidad y la app solo lo **registra** |
| **Finalizar** | El proceso queda de solo lectura. Lo garantizan disparadores de la base, también frente a SQL directo |

El manual para quien usa la aplicación está en **[MANUAL_DE_USO.md](MANUAL_DE_USO.md)**.

---

## Inicio rápido

### Usar la aplicación

1. Instale `Valuación de Activos-0.1.0-instalador.exe`. Lo genera `npm run dist:win` en `app/dist/`.
   El instalador no está firmado (`T-H-02`), así que Windows mostrará el aviso de SmartScreen.
2. Para probarla sin datos reales, use el hospital ficticio de
   [`datos_de_prueba/`](datos_de_prueba/LEEME.md). El recorrido completo está explicado paso a paso.

### Desarrollar

Requisitos: **Windows**, **Node.js ≥ 22.20** y **npm** (no pnpm).

```bash
cd app
npm ci --ignore-scripts   # sin el flag, npm intenta compilar better-sqlite3 y falla
npm run dev:aislado       # la app, sobre una instalación propia y en blanco
npm run verificar:todo    # la puerta de calidad antes de cerrar cualquier tarea
```

> **No use `npm run dev` sin leer [este aviso](app/docs/10_CONSTRUIR_Y_DISTRIBUIR.md#desarrollo-aislado-léalo-antes-de-usar-npm-run-dev):**
> abre la misma base de datos que la aplicación instalada.

Todos los comandos están en [`app/README.md`](app/README.md).

---

## Mapa del repositorio

Hay cuatro carpetas: **qué** debe hacer la app, **cómo se construye y en qué va**, la **app** misma
y los **datos de prueba**.

| Carpeta | Qué contiene |
|---|---|
| [`especificacion/teoria/`](especificacion/teoria/README.md) | **Especificación funcional autoritativa** (v2.1): 11 pasos, 4 anexos y [`CORRECCIONES.md`](especificacion/teoria/CORRECCIONES.md). En el motor de cálculo manda [`ANEXO_C`](especificacion/teoria/ANEXO_C_FORMULAS_Y_REGLAS.md) |
| [`especificacion/normatividad/`](especificacion/normatividad/README.md) | Contraste del proceso con la norma colombiana (CGN, saneamiento contable) |
| [`especificacion/plantillas/`](especificacion/plantillas/LEEME_PLANTILLAS.md) | Las 28 plantillas de referencia. La app entrega 5: `PL-01`, `PL-02`, `PL-02b`, `PL-03` y `PL-05` |
| [`proyecto/plan/`](proyecto/plan/README.md) | Plan técnico: 9 fases, [decisiones (ADR)](proyecto/plan/DECISIONES/README.md) y backlog |
| [`proyecto/gestion/`](proyecto/gestion/) | [Estado actual](proyecto/gestion/estado_actual.md), [roadmap](proyecto/gestion/roadmap_progreso.md) y [bitácora](proyecto/gestion/bitacora.md) |
| [`app/`](app/README.md) | La aplicación: Electron + React + TypeScript. Su documentación técnica está en [`app/docs/`](app/docs/README.md) |
| [`datos_de_prueba/`](datos_de_prueba/LEEME.md) | Hospital **ficticio** con un caso limpio y otro con errores a propósito. Incluye el [instructivo de diligenciamiento](datos_de_prueba/INSTRUCTIVO_DILIGENCIAMIENTO.md) |

## Por dónde empezar a leer

| Si usted… | Lea |
|---|---|
| va a **usar** la aplicación | [MANUAL_DE_USO.md](MANUAL_DE_USO.md) |
| va a **llenar las plantillas** Excel | [INSTRUCTIVO_DILIGENCIAMIENTO.md](datos_de_prueba/INSTRUCTIVO_DILIGENCIAMIENTO.md) |
| va a **programar** | [estado_actual.md](proyecto/gestion/estado_actual.md), luego [app/docs/01_PANORAMA.md](app/docs/01_PANORAMA.md) y [02_ARQUITECTURA.md](app/docs/02_ARQUITECTURA.md) |
| va a **contribuir** | [CONTRIBUTING.md](CONTRIBUTING.md) |
| quiere saber **por qué** algo es como es | [las decisiones (ADR)](proyecto/plan/DECISIONES/README.md) |
| quiere saber **qué** debe calcularse | [especificacion/teoria/](especificacion/teoria/README.md) |

## Stack

Electron 44 · React 19 · TypeScript 5.9 · Vite 7 · SQLite (better-sqlite3 + Drizzle ORM) ·
decimal.js · ExcelJS · Zod · Tailwind 3.4 · Vitest · Playwright · electron-builder (NSIS).

Las versiones son exactas y están congeladas. Ver [`app/docs/decisiones-runtime.md`](app/docs/decisiones-runtime.md).

## Reglas que no se negocian

Las completas están en [`CLAUDE.md`](CLAUDE.md). Estas son las que más cuesta romper:

1. **`especificacion/teoria` manda.** El código nunca la contradice, y lo que no implementa se marca
   `FUERA_DE_ALCANCE` en [`trazabilidad.csv`](app/docs/trazabilidad.csv).
2. **El dinero nunca es un `number` de pesos.** Se guarda en enteros de centavos y el motor usa
   `decimal.js` (ADR-006).
3. **El motor de cálculo es dominio puro.** No importa `fs`, `electron`, `sqlite`, `react` ni `Date`.
4. **Lo calculado no se reescribe.** Los cortes, los barridos y la bitácora son inmutables, y un
   proceso finalizado queda congelado. Lo garantizan disparadores (ADR-017, ADR-028, ADR-029).
5. **Un cálculo que no se pudo hacer nunca es un cero.**
6. **Ningún valor esperado en los tests del motor se cambia sin justificarlo en la bitácora**
   (riesgo `RG-01`).

## Licencia

`UNLICENSED`: código privado. Ningún dato del repositorio corresponde a una entidad real.
