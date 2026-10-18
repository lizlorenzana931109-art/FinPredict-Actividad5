# Datos iniciales | FinPredict

## 1. Descripción

Este directorio contiene los conjuntos de datos sintéticos generados para el proyecto **FinPredict**, cuyo objetivo es desarrollar modelos de Machine Learning para anticipar el incumplimiento de pago en créditos hipotecarios.

Los datos representan una cartera hipotecaria simulada entre enero de 2021 y diciembre de 2025. No contienen información de clientes reales.

Los archivos se conservan como fuente de referencia, antes de aplicar los procesos de limpieza, estandarización y preparación de datos correspondientes a la Actividad 5.

## 2. Conjuntos de datos

| Archivo | Registros | Descripción |
|---|---:|---|
| `contratos.parquet` | 50,000 | Información contractual, financiera y sociodemográfica de los créditos. |
| `obligaciones.parquet` | 1,475,182 | Calendario histórico de obligaciones mensuales exigibles. |
| `movimientos.parquet` | 1,584,222 | Movimientos de pago ordinarios, parciales y regularizaciones extraordinarias. |

Los tres conjuntos se relacionan mediante el identificador `id_credito`.

## 3. Dataset analítico

A partir de los archivos originales se construyó un dataset analítico con las siguientes características:

| Característica | Resultado |
|---|---:|
| Observaciones contrato-periodo | 390,661 |
| Contratos distintos | 44,913 |
| Variables disponibles | 30 |
| Variables predictoras iniciales | 15 |
| Observaciones de desarrollo histórico | 350,520 |
| Observaciones reservadas para evaluación final | 40,141 |
| Eventos históricos de incumplimiento | 23,650 |

Los archivos analíticos generados son:

- `finpredict_analitico_v0.parquet`
- `finpredict_desarrollo_v0.parquet`
- `finpredict_evaluacion_v0.parquet`
- `manifiesto_v0.json`

La denominación `v0` identifica la versión inicial del conjunto analítico, previa al procesamiento de calidad de datos.

## 4. Definición de la variable objetivo

La variable `incumplimiento_90d` representa si un crédito alcanza **90 días o más de atraso** durante los tres meses posteriores a una fecha de corte.

Para su construcción se utilizaron ventanas trimestrales, diferenciando:

- Periodo de observación: información disponible hasta la fecha de corte.
- Periodo de predicción: los tres meses posteriores.
- Población elegible: contratos que todavía no presentan mora de 90 días al corte.

La ventana octubre–diciembre de 2025 se reservó para la evaluación final.

## 5. Validación y trazabilidad

Durante la construcción se ejecutaron controles de integridad referencial, unicidad, consistencia temporal, importes y conciliación de etiquetas.

La evaluación final presentó **40,141 etiquetas coincidentes de 40,141 comparadas** con la línea base previamente auditada.

Se identificaron excedentes de pagos acumulados respecto a las obligaciones exigibles. Estos se conservan como una limitación documentada del simulador, dado que el modelo actual no distingue explícitamente los anticipos y los saldos a favor.

## 6. Almacenamiento y reproducibilidad

Los datasets se almacenan en formato **Apache Parquet**, utilizando compresión Snappy para reducir el espacio y conservar los tipos de datos.

El archivo `manifiesto_v0.json` registra las características de los datasets analíticos y sus huellas SHA-256 para verificar la integridad de los archivos.

El proceso de construcción se documenta en el notebook disponible en el directorio `fuentes/`.

## 7. Consideraciones éticas y alcance

Todos los registros son sintéticos y se generaron exclusivamente con fines académicos y experimentales.

Las reglas de simulación no representan necesariamente la distribución real de riesgo, morosidad o comportamiento financiero de una institución bancaria.

Los modelos desarrollados a partir de estos datos deberán evaluarse considerando sus limitaciones, la prevención de fuga temporal de información y posibles sesgos.

---

**Proyecto:** FinPredict — Actividad 5  
**Área:** Machine Learning aplicado a riesgo crediticio hipotecario  
**Estado:** Datos iniciales generados y respaldados; preparación de datos pendiente.
