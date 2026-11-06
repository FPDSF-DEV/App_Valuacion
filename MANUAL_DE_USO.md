# Manual de uso — Valuación de Activos

Para el responsable de activos de la E.S.E. Este manual explica cómo hacer una valuación de
principio a fin. No hace falta saber de programación.

---

## Qué hace la aplicación, y qué no

**Hace:**

- Carga el inventario que usted contó en un **barrido**, desde una plantilla de Excel.
- Calcula, a la **fecha de corte** que usted elija, la depreciación, el índice de obsolescencia y el
  semáforo de cada bien.
- Señala qué bienes son **candidatos a baja** y explica por qué.
- Registra las bajas que su entidad decidió.
- Genera el **informe en PDF**, tamaño Carta.

**No hace:**

- No tramita el saneamiento contable: no convoca al comité, no redacta actas ni recoge firmas. Eso
  lo hace la entidad por su cuenta, y la aplicación nunca se detiene esperándolo.
- No se usa en campo. No hay cámara, escáner ni aplicación móvil: el conteo se hace en Excel y
  después se importa.
- No da de baja nada por su cuenta. **Propone**, y usted decide.
- No necesita internet ni usuario. Todo queda en el equipo donde se instala.

---

## Instalar

1. Ejecute `Valuación de Activos-<versión>-instalador.exe`.
2. Windows puede mostrar **«Windows protegió su PC»** porque el instalador no está firmado. Pulse
   **Más información → Ejecutar de todas formas**.
3. Elija la carpeta de instalación. No necesita permisos de administrador.

Actualizar a una versión nueva o desinstalar **no borra sus datos**.

---

## El proceso de valuación

Todo el trabajo se organiza en **procesos**. Un proceso es una valuación completa a una fecha de
corte, por ejemplo «Valuación cierre 2025» al 31/12/2025.

- Cada proceso es **independiente**: tiene sus propios datos del hospital, su catálogo, su
  inventario y su cálculo. Si inicia uno nuevo, tendrá que cargarle de nuevo el catálogo y el
  inventario.
- Al abrir la aplicación verá **Procesos de valuación**: los que están en curso, para continuarlos,
  y los finalizados, para consultarlos.

El recorrido completo es este:

| # | Sección | Qué hace usted |
|:-:|---|---|
| 1 | **Iniciar un proceso** | Le da nombre, fecha de corte y datos del hospital |
| 2 | **Configurar** | Carga sedes, servicios, clases de activo y parámetros |
| 3 | **Formatos** | Descarga las plantillas Excel para llenarlas |
| 4 | **Inventario** | Importa el barrido (`PL-03`) y los datos económicos (`PL-05`) |
| 5 | **Calcular** | Calcula a la fecha de corte |
| 6 | **Bajas** | Registra las bajas que se decidieron |
| 7 | **Informe** | Guarda el PDF |
| 8 | **Finalizar** | Cierra el proceso, que queda de solo lectura |

En la pantalla **Resumen** de cada proceso puede ver en qué paso va.

---

## Paso a paso

### 1. Iniciar un proceso

En **Procesos de valuación**, pulse **Iniciar un proceso nuevo**. Hay dos formas:

- **Crear desde PL-01**: elige el archivo `PL-01` diligenciado. La aplicación toma de él la razón
  social, el NIT, el gerente, la fecha de corte y los parámetros de cálculo, y le muestra una vista
  previa antes de crear el proceso.
- **A mano**: escribe el nombre del proceso, la fecha de corte y los datos del hospital.

La **fecha de corte** es obligatoria y no puede estar en el futuro. Si la cambia más adelante, se
descarta el cálculo que hubiera.

### 2. Configurar

En **Configurar** encontrará cuatro apartados:

| Apartado | Qué contiene | Cómo se llena |
|---|---|---|
| **Proceso y hospital** | Razón social, NIT, municipio, representante legal y contador | A mano o importando `PL-01` |
| **Sedes y servicios** | Dónde están los bienes | Importando `PL-02b` |
| **Clases de activo** | Cada clase con su vida útil | Importando `PL-02`. Trae una lista sugerida |
| **Parámetros de cálculo** | Método de conteo de meses, valor residual y umbrales del semáforo | Se precargan con valores por defecto |

> Las **vidas útiles** y los **parámetros** que trae la aplicación son **sugerencias**. Revíselos
> con el contador de la entidad **antes** de calcular: cambian las cifras del informe.

### 3. Descargar los formatos

En **Formatos** descargue las plantillas que se van a llenar. Salen con los catálogos del proceso
(clases, sedes y servicios) como **listas desplegables**, para que quien las llene no tenga que
escribirlos.

| Plantilla | Para qué |
|---|---|
| `PL-01` | Datos de la entidad y parámetros |
| `PL-02` | Clases de activo y vida útil |
| `PL-02b` | Sedes y servicios |
| `PL-03` | **Toma de inventario físico**: el barrido, un bien por fila |
| `PL-05` | **Hoja de vida**: fecha de adquisición, costo, mantenimientos |

Si va a entregar los formatos a otra persona para que los llene, désele también el
[instructivo de diligenciamiento](datos_de_prueba/INSTRUCTIVO_DILIGENCIAMIENTO.md). Explica cada
columna, los valores admitidos y los errores más frecuentes.

### 4. Cargar el inventario

En **Inventario → Cargar**:

1. Importe el **`PL-03`** con el barrido.
2. Importe el **`PL-05`** con los datos económicos.

Antes de guardar, la aplicación le muestra **fila por fila** qué entra, qué tiene una advertencia y
qué tiene un error, con el motivo. **No importa nada hasta que usted lo confirme.** Si hay errores,
puede corregir el Excel y volver a cargarlo, o confirmar para que entren solo las filas válidas.

**Cada carga de `PL-03` es un barrido nuevo.** La aplicación:

- **crea** los bienes que no conocía,
- **actualiza** los que ya tenía,
- marca como **no encontrados** los que faltan en los servicios que recorrió el archivo.

En **Barridos** queda la historia de cada carga, y en **Bienes** está el listado completo, con
búsqueda y filtros.

> Para corregir un dato de un bien, hoy hay que cargar un barrido o un `PL-05` nuevos.

### 5. Calcular

En **Calcular**, pulse **Calcular**. El cálculo se hace siempre a la fecha de corte del proceso.

Verá:

- **Totales contables**: costo, depreciación acumulada y valor neto.
- **El semáforo** de obsolescencia. Puede navegar por cada color:

  | Color | Qué indica (con los umbrales por defecto) |
  |---|---|
  | 🟢 Verde | El bien ha consumido hasta el 50 % de su vida útil |
  | 🟡 Amarillo | Entre el 50 % y el 80 % |
  | 🟠 Naranja | Entre el 80 % y el 99 % |
  | 🔴 Rojo | Más del 99 %: agotó su vida útil o está a punto de agotarla |

- **Bien por bien**: el detalle de cada uno.
- **Qué quedó fuera del cálculo**: los bienes que no se pudieron calcular, cada uno con su motivo.

> **Un bien que no se pudo calcular nunca aparece como cero.** Sale con uno de estos tres estados:
>
> | Estado | Significa | Ejemplo |
> |---|---|---|
> | **No aplica** | No corresponde calcularlo | Un terreno, que no se deprecia; un bien en comodato; un bien adquirido después de la fecha de corte |
> | **No calculable** | Falta un dato para calcularlo | Sin fecha de adquisición o sin costo |
> | **Error en los datos** | Hay datos contradictorios | Fechas imposibles |

Si corrige algo y vuelve a calcular, el cálculo nuevo **reemplaza** al anterior: hay uno solo por
proceso. Si el inventario cambió después de calcular, la aplicación se lo avisa.

### 6. Registrar las bajas

En **Bajas → Candidatos a baja** verá los bienes que el cálculo señaló, con la razón:

- superó su vida útil;
- tiene el índice de obsolescencia alto (por encima del umbral amarillo) y está en estado **malo**
  o **inservible**;
- se declaró que ya no cumple su función (obsolescencia funcional);
- no está operativo y tiene registrado un mantenimiento correctivo fallido.

**La aplicación no decide.** La baja la aprueba la entidad por su propio trámite. Cuando esté
decidida, regístrela aquí:

| Campo | |
|---|---|
| **Causal** | Obsolescencia, Inservible, Caso fortuito, Desuso, o Donación o traslado |
| **Fecha de la baja** | |
| **Justificación** | **Individual y obligatoria**, de al menos 20 caracteres. Las justificaciones genéricas («está dañado») se rechazan |
| **Documento que la aprobó** | Opcional: la resolución o el acta de la entidad |

También puede registrar la baja de un bien que no era candidato (**Registrar la baja de otro bien**).
Una baja registrada por error se **anula**, y la anulación queda a la vista con **Mostrar anuladas**.

### 7. Generar el informe

En **Informe**, revise la vista previa y guarde el **PDF**. El informe declara el método de cálculo
en el encabezado y en el pie de cada página.

El informe **no lleva firmas**. Incluye una nota que indica dónde va la firma: en el acto
administrativo de la entidad.

### 8. Finalizar

Cuando termine, vaya a **Resumen** y pulse **Finalizar proceso**. Para finalizar, el proceso tiene
que tener un cálculo.

Un proceso finalizado:

- queda de **solo lectura**: nada de él se puede cambiar;
- se puede consultar cuando quiera;
- puede volver a generar su informe en cualquier momento.

> **Finalizar no tiene vuelta atrás.** Si después necesita valuar de nuevo, inicie otro proceso.

Un proceso en curso se puede **eliminar** solo mientras no tenga inventario cargado.

---

## Sus datos

Todo se guarda en el equipo, en `%APPDATA%\Valuación de Activos\`:

| | |
|---|---|
| `valuacion.db` | La base de datos con todos los procesos |
| `respaldos\` | Un respaldo automático cada día, y otro antes de cada actualización |
| `logs\` | El registro técnico, útil si tiene que pedir soporte |

Para guardar una copia de seguridad propia, **cierre la aplicación** y copie esa carpeta completa.

---

## Si algo sale mal

| Qué ve | Qué hacer |
|---|---|
| **«Windows protegió su PC»** al instalar | **Más información → Ejecutar de todas formas** |
| **«Base de datos de otra versión»** al abrir | La base guardada no es compatible con esta versión. **Si tiene procesos que necesita conservar, elija Salir** y pida soporte. Si no, *Apartarla y empezar en blanco*: la base anterior **no se borra**, solo se renombra a `valuacion-incompatible-<fecha>.db` |
| **«La aplicación no pudo abrirse»** | Envíe a soporte el archivo más reciente de la carpeta `logs\` |
| La aplicación **no abre** y no muestra nada | Probablemente ya está abierta en otra ventana: búsquela en la barra de tareas |
| Una fila del Excel **se rechaza** | Lea el motivo en el reporte de la importación. Lo habitual es una clase, sede o servicio que no está en el catálogo: impórtelo primero con `PL-02` o `PL-02b` |
| El cálculo deja bienes **fuera** | Revise *Qué quedó fuera del cálculo*: cada bien dice qué le falta |

---

## Para practicar

La carpeta [`datos_de_prueba/`](datos_de_prueba/LEEME.md) trae un hospital **ficticio** con 41
bienes y todas las plantillas llenas. Tiene un caso limpio para recorrer el proceso completo y otro
con errores a propósito, para ver cómo informa la aplicación cuando algo está mal.
