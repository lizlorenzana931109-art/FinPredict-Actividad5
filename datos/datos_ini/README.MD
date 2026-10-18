# Datos iniciales | FinPredict

## 1. Descripción

Este directorio contiene los conjuntos de datos sintéticos generados para **FinPredict**, proyecto de Machine Learning orientado a la predicción del incumplimiento de pago en créditos hipotecarios.

Los datos representan una cartera simulada entre enero de 2021 y diciembre de 2025 y no proceden de clientes reales.

El proyecto distingue entre dos fuentes:

- **Fuente financiera y analítica validada:** conserva las condiciones contractuales, obligaciones, movimientos y etiquetas de incumplimiento.
- **Fuente inicial para calidad de datos:** incorpora atributos personales ficticios e incidencias deliberadas para evaluar procedimientos de limpieza, estandarización, deduplicación y protección de información.

Esta separación permite conservar la trazabilidad del proceso sin modificar los resultados financieros previamente validados.

## 2. Conjuntos de datos financieros

| Archivo | Registros | Descripción |
|---|---:|---|
| `contratos.parquet` | 50,000 | Información contractual, financiera y sociodemográfica de los créditos. |
| `obligaciones.parquet` | 1,475,182 | Calendario histórico de obligaciones mensuales exigibles. |
| `movimientos.parquet` | 1,584,222 | Pagos ordinarios, parciales y regularizaciones extraordinarias. |

Los archivos se relacionan mediante el identificador `id_credito`.

## 3. Dataset analítico validado

A partir de los datos financieros se construyó un conjunto de observaciones contrato-periodo mediante ventanas trimestrales.

| Característica | Resultado |
|---|---:|
| Observaciones contrato-periodo | 390,661 |
| Contratos distintos | 44,913 |
| Variables originales | 30 |
| Variables predictoras iniciales | 15 |
| Observaciones de desarrollo histórico | 350,520 |
| Observaciones de evaluación final | 40,141 |
| Eventos históricos de incumplimiento | 23,650 |

**Archivos:**

- `finpredict_analitico_v0.parquet`
- `finpredict_desarrollo_v0.parquet`
- `finpredict_evaluacion_v0.parquet`
- `manifiesto_v0.json`

La denominación `v0` identifica la versión analítica preservada antes de introducir incidencias controladas para el ejercicio de calidad.

## 4. Fuente inicial enriquecida para calidad de datos

Se desarrolló una fuente adicional a partir del dataset analítico validado, incorporando información de identificación completamente sintética.

**Archivo:** `finpredict_fuente_calidad.parquet`

| Característica | Resultado |
|---|---:|
| Registros | 390,861 |
| Columnas | 33 |
| Titulares sintéticos distintos por contrato | 44,913 |
| Variables personales añadidas | 3 |

### Atributos de identificación

- `nombre_titular`: nombre y apellidos sintéticos con apariencia realista.
- `rfc_sintetico`: identificador de prueba con estructura semejante a un RFC mexicano.
- `direccion`: domicilio sintético con calle, número, colonia y código postal.

Estos atributos se generan exclusivamente para probar medidas de protección de datos. No se utilizarán como variables predictoras.

Los identificadores no han sido verificados como RFC fiscales auténticos y los domicilios no garantizan ausencia de coincidencias accidentales con direcciones existentes.

### Incidencias controladas

Para evaluar los procesos de preparación se incorporaron deliberadamente las siguientes incidencias:

| Incidencia | Casos verificados |
|---|---:|
| Valores nulos en `ingreso_mensual` | 300 |
| Formatos inconsistentes en `tipo_empleo` | 250 |
| Importes negativos en `monto_original` | 100 |
| Fechas con formato alternativo `DD/MM/YYYY` | 250 |
| Registros duplicados | 200 |
| **Total** | **1,100** |

Los duplicados exactos y los duplicados por clave representan los mismos 200 registros y no se contabilizan dos veces.

Las incidencias corresponden a una simulación académica deliberada, no a errores observados en información bancaria real.

**Manifiesto asociado:** `manifiesto_incidencias.json`.

## 5. Definición de la variable objetivo

La variable `incumplimiento_90d` identifica si un crédito alcanza **90 días o más de atraso** durante los tres meses posteriores a su fecha de corte.

Se utilizaron ventanas trimestrales:

- **Observación:** información disponible hasta la fecha de corte.
- **Predicción:** los tres meses posteriores al corte.
- **Elegibilidad:** créditos que todavía no presentan mora de 90 días al corte.

La ventana octubre–diciembre de 2025 permanece reservada para evaluación final.

La incorporación de atributos personales ficticios e incidencias controladas no modifica la definición ni los valores originales de la variable objetivo para las observaciones existentes.

## 6. Validación y trazabilidad

Durante la construcción se realizaron controles de integridad referencial, unicidad, consistencia temporal, importes, saldos y conciliación de etiquetas.

### Resultados relevantes

- 2,310 incumplimientos positivos confirmados mediante auditoría independiente en la línea base original.
- 40,141 etiquetas coincidentes de 40,141 comparadas entre el enfoque histórico y la línea base, sobre la población compartida.
- Conservación de las obligaciones, movimientos y etiquetas originales durante el enriquecimiento sintético.

### Limitaciones identificadas

Se detectaron pagos acumulados superiores a las obligaciones exigibles en determinados cortes históricos.

El simulador emplea una aplicación FIFO simplificada y no distingue explícitamente los anticipos de los saldos a favor. Estos excedentes permanecen documentados como una limitación metodológica y no deben interpretarse automáticamente como errores contables reales.

## 7. Preparación de datos y protección de información

El procesamiento de la fuente enriquecida se realizará en el notebook:

`fuentes/02_preparacion_datos.ipynb`

Las operaciones previstas incluyen:

1. Diagnóstico de calidad de la fuente recibida.
2. Detección y eliminación de duplicados.
3. Estandarización de fechas y categorías.
4. Tratamiento justificado de valores faltantes e importes inválidos.
5. Supresión de nombres, RFC y domicilios sintéticos, con minimización de datos personales.
6. Verificación de la integridad de la variable objetivo y de la separación temporal.
7. Generación y versionamiento del dataset preparado para Machine Learning.

La preparación no se realizará sobre los archivos financieros originales, que permanecerán conservados como referencia.

## 8. Almacenamiento y reproducibilidad

Los conjuntos de datos se almacenan en formato **Apache Parquet** con compresión Snappy, conservando los tipos de datos según la naturaleza de cada fuente.

Los archivos de manifiesto contienen metadatos y huellas SHA-256 para verificar la integridad de los conjuntos versionados.

La lógica de generación y construcción se documenta en:

`fuentes/01_construccion_dataset.ipynb`

La separación entre los notebooks de construcción y preparación permite reproducir y auditar cada etapa de manera independiente.

## 9. Consideraciones éticas y alcance

Todos los registros son sintéticos y fueron generados con fines académicos y experimentales.

Los atributos personales ficticios se incorporaron exclusivamente para evaluar controles de privacidad y minimización de datos.

Las reglas del simulador no representan necesariamente las condiciones de riesgo, morosidad o recuperación de una cartera bancaria real.

Los modelos desarrollados a partir de estos datos deberán evaluarse considerando el desbalance de clases, la dependencia temporal entre observaciones de un mismo contrato, las limitaciones de simulación, la prevención de fuga de información y los posibles sesgos.

---

**Proyecto:** FinPredict — Actividad 5  
**Área:** Machine Learning aplicado a riesgo crediticio hipotecario  
**Estado:** Construcción de datos finalizada; fuente inicial enriquecida y auditada; preparación de datos pendiente.
