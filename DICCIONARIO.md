# Diccionario de datos

Tres archivos en `datos/`, en CSV con codificación UTF-8 y separador coma:

- `contratos.csv`: un registro por contrato o por partida de contrato con compra de energía solar (413 filas, 33 columnas).
- `solicitudes.csv`: las solicitudes de acceso a la información presentadas en la Plataforma Nacional de Transparencia (PNT) para completar los precios (93 filas, 21 columnas).
- `manifiesto_documentos.csv`: un renglón por documento fuente publicado en los archivos zip del release (1,160 filas, 10 columnas).

Los valores booleanos se escriben `TRUE` o `FALSE`. Los montos están en pesos mexicanos (MXN) corrientes, sin redondear. Las fechas van en formato `AAAA-MM-DD`.

## contratos.csv

| Columna | Qué contiene |
|---|---|
| `id_registro` | Identificador único de la fila. El prefijo indica la vía de extracción: `CMX` (ComprasMX), `PNT` (Plataforma Nacional de Transparencia o portal estatal), `PILOTO` (muestra inicial). |
| `fuente` | Origen del dato: `ComprasMX`, `PNT` o `Portal estatal (Estado)`. |
| `id_contrato_fuente` | Número de contrato o de procedimiento tal como aparece en la fuente. |
| `duplicado_de` | `id_registro` de la fila gemela cuando el mismo contrato se encontró en dos fuentes. Ver «Duplicados». |
| `url_documento` | Enlace al documento fuente (convocatoria, anexo técnico, fallo o contrato) cuando existe un enlace público. |
| `url_verificacion` | Enlace a la página del expediente o del procedimiento donde se puede comprobar el registro. |
| `entidad_federativa` | Entidad del comprador, o `Federacion` si compra una dependencia del gobierno federal. |
| `dependencia_compradora` | Institución que compra. |
| `municipio` | Municipio, cuando aplica. |
| `familia` | Categoría del bien o servicio. Ver «Vocabularios». |
| `descripcion_original` | Texto tal como aparece en el contrato o en la fuente. |
| `especificaciones_tecnicas` | Características que permiten comparar el bien: capacidad, tecnología, marca y modelo si constan. |
| `cantidad` | Unidades compradas. |
| `unidad_medida` | `pieza`, `sistema`, `servicio`, `lote`, `paquete` o `informe`. |
| `capacidad_watts` | Capacidad total en watts, solo para componentes o sistemas fotovoltaicos que la declaran. |
| `monto_con_iva_mxn` | Monto del contrato o de la partida con IVA. |
| `monto_sin_iva_mxn` | El mismo monto sin IVA. |
| `precio_unitario_con_iva_mxn` | Monto con IVA entre `capacidad_watts` cuando hay watts, o entre `cantidad` en los demás casos. |
| `precio_unitario_sin_iva_mxn` | Lo mismo, sin IVA. |
| `alcance_precio` | Qué cubre el precio unitario. Ver «Vocabularios». |
| `categoria_compra` | Tipo de contratación: `adquisicion`, `servicios`, `obra_publica` o `servicios_relacionados_obra`. |
| `mecanismo_procedimiento` | Cómo se adjudicó: `adjudicacion_directa`, `invitacion_restringida` o `licitacion_publica`. |
| `depurado_obra_publica` | `TRUE` si el registro venía mezclado con obra civil y se intentó aislar el componente solar. |
| `comparable` | `TRUE` si la fila tiene un precio unitario atribuible a un solo bien o servicio de su familia. Ver «Cuándo una fila no es comparable». |
| `año_fiscal` | Año del contrato. |
| `fecha_contrato` | Fecha del contrato o del procedimiento. |
| `notas_metodologicas` | Explicación de las decisiones aplicadas a la fila: de dónde sale cada cifra, qué partida se aisló, por qué no es comparable. Una marca entre corchetes al inicio indica cuándo y por qué cambió la fila (por ejemplo, con la respuesta a una solicitud). |
| `como_verificar` | Cómo comprobar el dato en la fuente: documento, página y partida, o solicitud y folio. |
| `proveedor` | Razón social o nombre del adjudicado tal como lo publica la fuente, sin normalizar. Vacío si la fuente no lo publica. |
| `rfc_proveedor` | RFC publicado por la fuente, sin espacios ni guiones. Se conserva aunque venga incompleto. |
| `unidad_precio_unitario` | Unidad del precio unitario: `MXN/W` si hay `capacidad_watts`, si no `MXN/` más la unidad de medida (`MXN/pieza`, `MXN/servicio`, etcétera). |
| `solicitud_origen` | `id` de la solicitud (en `solicitudes.csv`) cuya respuesta aportó el dato de la fila. Vacía si el dato viene de un documento publicado. |
| `folio_solicitud_origen` | Folio de esa solicitud en la PNT. |

### Vocabularios

**`familia`**: `panel_modulo`, `inversor`, `estructura_montaje`, `sistema_interconectado`, `sistema_aislado`, `instalacion`, `supervision_obra`, `mantenimiento_preventivo`, `mantenimiento_correctivo`, `calentador_solar`, `arrendamiento` (renta de sistemas fotovoltaicos, siempre no comparable) y `componente_mixto` (partida que agrupa varios componentes sin desglose).

**`alcance_precio`**:

- `panel`: solo el módulo fotovoltaico, aislado del resto del contrato, o con la mano de obra de colocarlo.
- `panel_con_estructura`: una partida que agrupa el módulo con estructura, cableado, protecciones u obra de montaje, sin ser el sistema completo.
- `sistema`: sistema completo llave en mano (módulos, inversor, estructura e instalación, a veces con obra asociada).
- `componente`: pieza suelta que no es un módulo instalable ni un sistema (inversor, controlador, celdas sueltas).
- `equipo`: aparato completo no fotovoltaico, como un calentador solar.
- `servicio`: mantenimiento, supervisión o instalación sin suministro de equipo.

`familia` describe el contrato y `alcance_precio` describe el precio. Un contrato de familia `instalacion` puede traer el precio aislado del panel, con `alcance_precio = panel`.

### Cuándo una fila no es comparable

`comparable = FALSE` cuando no se puede aislar un precio unitario atribuible a un solo bien o servicio: partidas que agrupan componentes sin desglose, obra pública a precio alzado sin catálogo de precios, montos no publicados, contratos abiertos que solo publican el monto máximo, o la segunda fila de un contrato duplicado. Las filas no comparables no se borran porque documentan qué información no está disponible.

### Advertencias para el análisis

- **No promediar precios de distinto alcance ni de distinta unidad.** Dentro de un mismo `alcance_precio` conviven precios por watt y precios por pieza o por sistema. Filtrar siempre por `unidad_precio_unitario` y por `alcance_precio`.
- **Módulos de menos de 300 W.** En `alcance_precio = panel`, los módulos de menos de 300 W por pieza (`capacidad_watts` entre `cantidad`) son de otro mercado (luminarias y sistemas aislados) y su precio por watt es más alto.
- **Contratos abiertos.** Algunos contratos de mantenimiento solo publican el monto máximo. El monto se conserva como lo publica la fuente, pero no es un gasto ejercido.

### Duplicados

`duplicado_de` se usa en dos casos:

- **El mismo contrato en dos fuentes.** Siete contratos aparecen en ComprasMX y en la PNT a la vez. Se conservan las dos filas, enlazadas en ambas direcciones, y como máximo una queda como comparable.
- **Partidas desglosadas de otro registro.** En diez filas, `duplicado_de` apunta al registro del contrato completo del que la fila aísla una partida. El enlace va en una sola dirección y el registro del contrato completo no es comparable.

Filtrar `comparable = TRUE` evita contar dos veces un precio. Para sumar montos, excluir las filas con `duplicado_de` lleno o agruparlas con la fila a la que apuntan.

## solicitudes.csv

| Columna | Qué contiene |
|---|---|
| `id` | Identificador de la solicitud (`PS-NNN`). Se cruza con `solicitud_origen` de `contratos.csv`. |
| `ambito` | `Federal` o `Estatal o municipal`. |
| `entidad_federativa` | Entidad del sujeto obligado. |
| `institucion` | Sujeto obligado al que se dirigió la solicitud. |
| `unidad_administrativa` | Área a la que se pidió turnar la solicitud. |
| `linea_tecnologica` | `fotovoltaica`, `termica` (calentadores solares) o `mixta`. |
| `objetivo_de_la_solicitud` | Qué dato se buscaba obtener. |
| `texto_de_la_solicitud` | Texto presentado en la PNT. |
| `datos_adicionales` | Texto presentado en el campo de datos que facilitan la búsqueda y localización. |
| `periodo_solicitado` | Periodo que cubre la solicitud. |
| `contratos_citados` | Contratos o procedimientos citados en la solicitud. |
| `n_contratos` | Número de contratos citados. |
| `estatus` | `respondida`, `vencida_sin_respuesta`, `enviada` (en plazo al corte) o `pendiente` (no presentada). |
| `folio` | Folio de la PNT. |
| `fecha_envio` | Fecha de presentación. |
| `fecha_limite_respuesta` | Fecha límite de respuesta según la PNT. |
| `estatus_pnt` | Estatus en la PNT al corte (`Terminada` o `En proceso`). |
| `fecha_respuesta` | Fecha de la respuesta. |
| `canal_respuesta` | Vía por la que llegó la respuesta (`PNT` o `correo`). |
| `tipo_respuesta` | `entrega_completa`, `entrega_parcial`, `remite_a_liga_publica`, `inexistencia`, `incompetencia`, `reserva_o_confidencial`, `cobro_reproduccion` u `otro`. |
| `entrega_de_precio` | Si la respuesta permitió obtener un precio: `si`, `parcial` o `no`. |

## manifiesto_documentos.csv

| Columna | Qué contiene |
|---|---|
| `archivo_zip` | Archivo del release que contiene el documento. |
| `ruta_en_zip` | Ruta del documento dentro del zip: `origen/entidad/registro o solicitud/archivo`. |
| `nombre_archivo` | Nombre del archivo. |
| `origen` | `comprasmx`, `pnt_buscador` (buscador de contratos de la PNT), `portal_estatal`, `sistema6` (Plataforma Digital Estatal de Aguascalientes) o `respuesta_solicitud` (documentos entregados en respuesta a una solicitud). |
| `id_registro` | `id_registro` de la fila de `contratos.csv` a la que corresponde el documento, si se pudo vincular. |
| `id_solicitud` | `id` de la solicitud, para los documentos que llegaron como respuesta. |
| `metodo_vinculo` | Cómo se vinculó el documento con la base: `carpeta_procedimiento`, `id_contrato_fuente`, `solicitud_origen`, `solicitud_contratos_ids`, `prefijo_nombre` o `sin_vinculo`. |
| `ext` | Extensión del archivo. |
| `bytes` | Tamaño en bytes. |
| `sha256` | Huella del archivo, para comprobar que no cambió. |
