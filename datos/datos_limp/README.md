# Datos limpios | FinPredict

## 1. Descripción

Este directorio contiene la **versión preparada y validada de los datos sintéticos de FinPredict**, proyecto de Machine Learning orientado a la predicción del incumplimiento de pago en créditos hipotecarios.

Los archivos son resultado del procesamiento realizado en `02_preparacion_datos.ipynb`, a partir de la fuente `finpredict_fuente_calidad.parquet`.

El objetivo de esta etapa fue mejorar la calidad, consistencia y trazabilidad de la información, preservando el universo analítico y la variable objetivo.

Todos los datos son sintéticos y se utilizan exclusivamente con fines académicos y experimentales.

## 2. Archivos disponibles

| Archivo | Registros | Columnas | Descripción |
|---|---:|---:|---|
| `finpredict_preparado_v1.parquet` | 390,661 | 34 | Dataset completo después del procesamiento de calidad. |
| `finpredict_desarrollo_v1.parquet` | 350,520 | 34 | Observaciones históricas destinadas al desarrollo y validación de modelos. |
| `finpredict_evaluacion_v1.parquet` | 40,141 | 34 | Conjunto temporal reservado para evaluación final. |
| `manifiesto_v1.json` | — | — | Metadatos, variables seleccionadas y huellas SHA-256. |

La denominación `v1` identifica la primera versión preparada del dataset.

## 3. Procesos de calidad aplicados

Se identificaron y resolvieron **1,100 incidencias sintéticas controladas**.

| Incidencia | Antes | Después | Tratamiento |
|---|---:|---:|---|
| Duplicados por contrato y corte | 200 | 0 | Eliminación de duplicados exactos. |
| Ingresos mensuales faltantes | 300 | 0 | Recuperación histórica (259) e imputación por mediana (41). |
| Categorías de empleo inconsistentes | 250 | 0 | Normalización de espacios y mayúsculas. |
| Montos contractuales inválidos | 100 | 0 | Recuperación histórica (91) y conciliación con fuente maestra (9). |
| Fechas con formatos heterogéneos | 250 | 0 | Conversión al tipo `datetime64[ns]`. |
| **Total de incidencias** | **1,100** | **0** | |

Adicionalmente, se suprimieron tres columnas de identificación directa:

- `nombre_titular`
- `rfc_sintetico`
- `direccion`

Esta operación corresponde a minimización de datos. No implica una garantía de anonimización irreversible.

## 4. Integridad analítica

Después de la limpieza se verificó la correspondencia con el dataset analítico original.

| Indicador | Resultado |
|---|---:|
| Observaciones válidas | 390,661 |
| Contratos distintos | 44,913 |
| Eventos de incumplimiento | 23,650 |
| Observaciones históricas | 350,520 |
| Observaciones de evaluación final | 40,141 |
| Duplicados por clave | 0 |

La comparación confirmó la conservación del universo, las etiquetas y las particiones temporales.

## 5. Variable objetivo y predictores

La variable objetivo es `incumplimiento_90d`, que identifica si un crédito alcanza 90 días o más de atraso durante los tres meses posteriores a la fecha de corte.

Se seleccionaron **14 variables predictoras iniciales**:

| Grupo | Variables |
|---|---|
| Contractuales | `monto_original`, `plazo_meses`, `tasa_interes_anual`, `pago_mensual` |
| Capacidad de pago | `ingreso_mensual`, `relacion_pago_ingreso` |
| Antigüedad | `antiguedad_meses` |
| Situación de cartera | `saldo_vencido`, `dias_atraso`, `saldo_vencido_mensualidades` |
| Comportamiento histórico | `importe_pagado_3m`, `numero_pagos_3m`, `importe_exigible_3m`, `porcentaje_pago_3m` |

Las columnas de identificación técnica, auditoría y control temporal se preservan en los archivos para trazabilidad, pero no forman parte de los 14 predictores iniciales.

Se excluyeron de esta selección las variables constantes `obligaciones_3m` y `mora_90_corte`.

## 6. Separación temporal

La preparación conserva la siguiente distribución:

- **Desarrollo histórico:** 350,520 observaciones de ventanas anteriores al corte de evaluación.
- **Evaluación final:** 40,141 observaciones al 30 de septiembre de 2025, cuyo resultado se observa entre octubre y diciembre de 2025.

La evaluación final permanecerá fuera del ajuste de hiperparámetros y de la selección de modelos.

Debido a que un mismo contrato puede aparecer en diferentes trimestres, el diseño de validación deberá considerar la dependencia entre observaciones y el orden temporal.

## 7. Versionamiento y reproducibilidad

Los datos se almacenan en formato Apache Parquet con compresión Snappy.

El archivo `manifiesto_v1.json` registra:

- Versión del conjunto preparado.
- Fuente de origen.
- Número de registros y columnas.
- Variable objetivo y predictores seleccionados.
- Separación de desarrollo y evaluación.
- Controles de calidad.
- Huellas SHA-256 para validar integridad.

La preparación se documenta en:

`fuentes/02_preparacion_datos.ipynb`

## 8. Limitaciones metodológicas

La versión `v1` incluye imputaciones estadísticas preliminares calculadas sobre el conjunto histórico de desarrollo.

Durante el entrenamiento y la validación temporal deberán aplicarse tratamientos compatibles con cada partición, evitando utilizar estadísticas de periodos posteriores para transformar observaciones anteriores.

También se requiere considerar los posibles sesgos derivados de la simulación y las limitaciones de generalización hacia carteras hipotecarias reales.

La resolución del 100% de las incidencias introducidas demuestra la efectividad de los controles de calidad evaluados, pero no garantiza por sí sola la capacidad predictiva de los modelos.

## 9. Siguiente etapa

Los archivos preparados servirán como insumo para:

- Regresión logística y Random Forest.
- Validación cruzada y optimización con GridSearchCV.
- Registro de experimentos mediante MLflow.
- Comparación de métricas y análisis de resultados.
- Evaluación ética y recomendaciones de MLOps.

---

**Proyecto:** FinPredict — Actividad 5  
**Área:** Machine Learning aplicado a riesgo crediticio hipotecario  
**Versión:** v1  
**Estado:** Preparación de datos concluida; modelado pendiente.
