# El precio del watt público

Compra pública de energía solar en México: contratos, precios unitarios y documentos fuente.

Este repositorio reúne la información con la que se construyó una base comparada de precios de la compra pública de paneles solares, inversores, sistemas fotovoltaicos, calentadores solares y servicios de instalación y mantenimiento. Contiene solo datos y documentos, sin análisis.

## Contenido

| Archivo | Qué es |
|---|---|
| [`datos/contratos.csv`](datos/contratos.csv) | 413 registros de contratos o partidas de contrato de 28 entidades y la Federación, con 212 precios unitarios comparables. Años fiscales 2021 a 2026, 402 de ellos de 2024 a 2026. |
| [`datos/solicitudes.csv`](datos/solicitudes.csv) | 93 solicitudes de acceso a la información, 92 presentadas en la Plataforma Nacional de Transparencia entre el 24 de septiembre y el 27 de octubre de 2026. |
| [`datos/manifiesto_documentos.csv`](datos/manifiesto_documentos.csv) | Índice de los 1,160 documentos fuente, con el zip que los contiene, el registro o la solicitud a la que corresponden y su huella sha256. |
| [`DICCIONARIO.md`](DICCIONARIO.md) | Qué significa cada columna, los vocabularios y las advertencias para analizar los datos. |

Los **documentos fuente** (convocatorias, fallos, contratos, catálogos de conceptos y respuestas a solicitudes, 4.68 GB sin comprimir y 4.36 GB en zip, en GB de 10^9 bytes) están en el [release `documentos-2026-11-10`](../../releases/tag/documentos-2026-11-10), en un zip por origen:

| Zip | Documentos |
|---|---|
| `documentos_comprasmx.zip` | 534 documentos de ComprasMX |
| `documentos_pnt_buscador.zip` | 199 documentos del buscador de contratos de la PNT |
| `documentos_portal_estatal.zip` | 123 documentos de portales estatales de compras y transparencia |
| `documentos_respuesta_solicitud.zip` | 303 documentos entregados en respuesta a las solicitudes |
| `documentos_sistema6.zip` | 1 documento de la Plataforma Digital Estatal de Aguascalientes |

Para encontrar los documentos de un registro, buscar su `id_registro` en la columna del mismo nombre de `manifiesto_documentos.csv`. Un documento puede servir a varios registros, separados por `|`, así que conviene buscar por contenido y no por coincidencia exacta.

## Fuentes y corte

- ComprasMX, el sistema de compras públicas del gobierno federal.
- Plataforma Nacional de Transparencia: obligaciones de transparencia sobre contratos y respuestas a solicitudes de acceso.
- Portales estatales de Chihuahua, Michoacán, Sonora, Querétaro y Aguascalientes.

Corte de datos: 10 de noviembre de 2026. Las respuestas y los recursos de revisión posteriores a esa fecha no están incluidos.

## Cómo usar los datos

- Cada fila de `contratos.csv` dice en `como_verificar` y `notas_metodologicas` de dónde sale cada cifra.
- Para comparar precios, filtrar `comparable = TRUE` y agrupar por `alcance_precio` y `unidad_precio_unitario`. Un precio por watt de un panel no se compara con el de un sistema completo. Ver [`DICCIONARIO.md`](DICCIONARIO.md).
- Los montos se publican tal como aparecen en la fuente. En contratos abiertos pueden ser montos máximos y no gasto ejercido.

## Créditos

Elaborado por PODER y Abrimos.info con el apoyo de Transparencia Mexicana, en el marco de la convocatoria CAIS-T2-C1 del fondo AC4ALL.

## Licencia

Las bases de datos, el manifiesto y el diccionario se publican con licencia [Creative Commons Atribución-CompartirIgual 4.0 Internacional (CC BY-SA 4.0)](LICENSE). Los documentos fuente son documentos públicos emitidos por las instituciones que los publicaron o entregaron, y se reproducen como evidencia de los datos.
