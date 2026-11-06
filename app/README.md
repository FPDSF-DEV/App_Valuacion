# `app/` — la aplicación

Electron + React + TypeScript. Este archivo explica cómo poner el proyecto en marcha. Cómo está
construido por dentro se explica en [`docs/`](docs/README.md).

> Antes de tocar código, lea [`../proyecto/gestion/estado_actual.md`](../proyecto/gestion/estado_actual.md).
> Dice en qué va el trabajo y cuál es el siguiente paso.

## Requisitos

| | |
|---|---|
| Sistema | **Windows** (el instalador es NSIS; la CI también corre en Linux con `xvfb`) |
| Node.js | **≥ 22.20.0** (`engines` en `package.json`) |
| Gestor | **npm**, con `package-lock.json`. No use pnpm ni yarn |
| Compilador C++ | **No hace falta**: `better-sqlite3` trae binarios precompilados |

## Instalar

```bash
npm ci --ignore-scripts
```

**Use `--ignore-scripts`.** Sin el flag, npm ejecuta `node-gyp rebuild` sobre `better-sqlite3`. Lo
hace porque el lock no guarda `gypfile: false`, y en un equipo sin Visual Studio C++ falla. Los
scripts que se omiten no se necesitan: el proyecto nunca compila módulos nativos
(`npmRebuild: false`).

Si después `esbuild` se queja de que le falta su binario:

```bash
node node_modules/esbuild/install.js
```

## Ejecutar

```bash
npm run dev:aislado                         # instalación propia en app/.datos-dev, en blanco
npm run dev:aislado -- --conservar          # conserva los datos de la vez anterior
npm run dev:aislado -- caso-b               # otro escenario, en blanco
```

> **`npm run dev` abre la base real** de la aplicación instalada
> (`%APPDATA%\Valuación de Activos\valuacion.db`) y le aplica las migraciones pendientes. Además,
> con la app instalada abierta ni siquiera arranca, porque comparten el bloqueo de instancia única.
> Use `dev:aislado` salvo que sepa lo que hace. Detalle en
> [docs/10](docs/10_CONSTRUIR_Y_DISTRIBUIR.md).

Para un recorrido con datos, cargue el hospital ficticio de
[`../datos_de_prueba/`](../datos_de_prueba/LEEME.md).

## Comandos

### Día a día

| Comando | Qué hace |
|---|---|
| `npm run dev:aislado` | Desarrollo sobre datos aislados |
| `npm run dev` / `dev:watch` | Desarrollo sobre la base real (ver el aviso) |
| `npm run verificar:todo` | **Puerta de calidad**: `typecheck` + `lint` + `boundaries` + `test` + `trazabilidad` |
| `npm test` / `test:watch` | Vitest |
| `npm run test:rendimiento` | Pruebas de carga (20.000 bienes), sin paralelismo |
| `npm run test:e2e` | Playwright sobre `out/`. Con `PROBAR_PAQUETE=1`, sobre el paquete de `npm run pack` |
| `npm run lint` / `lint:fix` | ESLint + Stylelint |
| `npm run format` | Prettier |
| `npm run boundaries` | dependency-cruiser: vigila las fronteras entre capas |

### Base de datos

| Comando | Qué hace |
|---|---|
| `npm run db:generar` | Genera una migración nueva desde el esquema (drizzle-kit) |
| `npm run db:migrar` | Aplica las migraciones desde la consola |
| `npm run db:triggers` | Regenera los disparadores. **Falla** si una tabla no declara cómo llega a su proceso |

Antes de añadir una migración, lea el procedimiento de
[docs/10 § Añadir una migración](docs/10_CONSTRUIR_Y_DISTRIBUIR.md#añadir-una-migración). Hay que
probarla sobre una base que ya tenga datos.

### Generadores

| Comando | Qué genera |
|---|---|
| `npm run datos:prueba` | El hospital ficticio de `../datos_de_prueba/` |
| `npm run instructivo` | `../datos_de_prueba/INSTRUCTIVO_DILIGENCIAMIENTO.md`, a partir de las definiciones del importador |
| `npm run trazabilidad` | `docs/trazabilidad.csv`: cada requisito de la teoría y su estado |
| `npx tsx scripts/sembrar.ts` | Datos de carga para medir rendimiento |

### Construir y distribuir

| Comando | Resultado |
|---|---|
| `npm run build` | `typecheck` + `electron-vite build` → `out/` |
| `npm run pack` | Aplicación empaquetada sin instalador → `dist/win-unpacked/` |
| `npm run dist:win` | **Instalador** → `dist/Valuación de Activos-<versión>-instalador.exe` |
| `npm run verificar:versiones` | Comprueba que todas las versiones son exactas (sin `^` ni `~`) |

El instalador lleva solo las 5 plantillas que la app entrega (`electron-builder.yml`). Actualizar o
desinstalar **nunca** borra los datos del usuario.

## Estructura

```
app/
├── src/
│   ├── compartido/   Lo que main y renderer comparten. No depende de nadie.
│   │   └── motor/    El cálculo. Dominio puro: sin fs, electron, sqlite, react ni Date.
│   ├── main/         Proceso principal: arranque, base de datos, IPC, Excel, PDF.
│   ├── preload/      Un solo archivo. Expone exactamente `invocar` y `suscribir`.
│   └── renderer/     La interfaz (React + Tailwind). No toca el sistema.
├── recursos/semillas/  Catálogos sugeridos, parámetros por defecto, hospital de demostración.
├── scripts/            Herramientas de consola.
├── tests/e2e/          Playwright sobre la app real y sobre el paquete.
└── docs/               Documentación técnica.
```

## Dónde guarda los datos

```
%APPDATA%\Valuación de Activos\
├── valuacion.db     la base (con -wal y -shm)
├── respaldos/       respaldo diario con rotación, y uno previo a cada migración
├── logs/            app-AAAA-MM-DD.log
└── config.json      si existe
```

La ruta se puede cambiar con `--datos=<ruta>` o con `config.json`, en ese orden de precedencia. Si
la base es de otra versión con un esquema incompatible, el arranque ofrece **apartarla** (se
renombra a `valuacion-incompatible-<fecha>.db`, no se borra) y empezar en blanco, o salir.

## Documentación técnica

| | |
|---|---|
| [01 Panorama](docs/01_PANORAMA.md) | Qué hace, cifras del código, stack |
| [02 Arquitectura](docs/02_ARQUITECTURA.md) | Las cuatro capas y sus fronteras |
| [03 Arranque y datos](docs/03_ARRANQUE_Y_DATOS.md) | Rutas, migraciones, respaldos, instancia única |
| [04 Base de datos](docs/04_BASE_DE_DATOS.md) | Tablas, disparadores, centavos, proceso congelado |
| [05 IPC](docs/05_IPC.md) | Canales, middleware y prohibiciones |
| [06 Motor de cálculo](docs/06_MOTOR_DE_CALCULO.md) | Depreciación, obsolescencia, semáforo y bajas |
| [07 Importación](docs/07_IMPORTACION.md) | Cómo entra un Excel y cómo se rechaza fila por fila |
| [08 Interfaz](docs/08_INTERFAZ.md) | Rutas, componentes, estado, accesibilidad |
| [09 Pruebas y calidad](docs/09_PRUEBAS_Y_CALIDAD.md) | Tests, E2E, CI |
| [10 Construir y distribuir](docs/10_CONSTRUIR_Y_DISTRIBUIR.md) | Scripts, empaquetado, desarrollo aislado |

## Problemas frecuentes

| Síntoma | Causa y solución |
|---|---|
| `npm ci` falla con *Could not find any Visual Studio* | Falta `--ignore-scripts` |
| `npm run dev` arranca y se cierra en silencio | La app instalada está abierta, o quedó un `electron.exe` colgado reteniendo el bloqueo. Ciérrelo y reintente |
| `Cannot import BrowserWindow from 'electron'` | `ELECTRON_RUN_AS_NODE` está definida. `dev:aislado` la limpia |
| La app avisa *«Base de datos de otra versión»* | La base se creó con otro esquema (por ejemplo, una de desarrollo anterior a ADR-029). Apártela desde el diálogo o use `dev:aislado` |
| Falla un test del motor | La primera hipótesis es que el código está mal, no el test. Sus valores esperados salen de `ANEXO_C` (`RG-01`) |
