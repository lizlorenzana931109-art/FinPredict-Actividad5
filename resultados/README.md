# Resultados de experimentación | FinPredict

## 1. Descripción

Este directorio contiene los resultados de optimización de hiperparámetros de los modelos de Machine Learning desarrollados para **FinPredict — Actividad 5**.

Los archivos CSV fueron generados mediante `GridSearchCV` de scikit-learn y registrados como artefactos en **MLflow Tracking 3.17.1**.

Su propósito es proporcionar evidencia verificable de las configuraciones evaluadas, sus métricas de desempeño y el proceso de selección del modelo.

## 2. Archivos disponibles

| Archivo | Descripción |
|---|---|
| `Regresion_Logistica_GridSearchCV_resultados.csv` | Resultados de las seis configuraciones evaluadas de regresión logística. |
| `Random_Forest_GridSearchCV_resultados.csv` | Resultados de las cuatro configuraciones evaluadas de Random Forest. |

Los archivos conservan la información exportada desde `cv_results_` de GridSearchCV, incluyendo parámetros, métricas por partición temporal, promedios y posiciones en el ranking.

## 3. Configuración experimental

| Característica | Configuración |
|---|---|
| Dataset | `finpredict_desarrollo_v1.parquet` |
| Observaciones de desarrollo | 350,520 |
| Variables predictoras | 14 |
| Variable objetivo | `incumplimiento_90d` |
| Validación | Tres particiones temporales expansivas |
| Optimización | GridSearchCV |
| Métrica principal | Average Precision (PR-AUC) |
| Infraestructura | Google Colab — CPU |

Se mantuvo una separación temporal entre entrenamiento y validación, utilizando etiquetas correspondientes a periodos concluidos antes de cada corte de validación.

El conjunto de evaluación final permaneció excluido del proceso de optimización.

## 4. Resultados de regresión logística

Se evaluaron seis combinaciones de hiperparámetros:

- Regularización `C`: 0.1, 1.0 y 10.0.
- Ponderación de clases `class_weight`: `None` y `balanced`.

**Mejor configuración según PR-AUC:**

| Indicador | Resultado |
|---|---:|
| C | 0.1 |
| Class weight | None |
| PR-AUC promedio | 0.8567 |
| ROC-AUC promedio | 0.9844 |
| Recall promedio | 0.6661 |
| F1-score promedio | 0.7452 |
| Tiempo de optimización | 18.83 segundos |

La configuración sin ponderación obtuvo el mejor PR-AUC promedio dentro de las alternativas evaluadas, mientras que la ponderación balanceada produjo un recall superior al umbral de clasificación de 0.50.

## 5. Resultados de Random Forest

Se evaluaron cuatro combinaciones:

- Profundidad máxima `max_depth`: 10 y 14.
- Mínimo de observaciones por hoja `min_samples_leaf`: 10 y 30.

Se mantuvieron constantes 120 árboles y la ponderación balanceada de clases.

**Mejor configuración según PR-AUC:**

| Indicador | Resultado |
|---|---:|
| Número de árboles | 120 |
| Profundidad máxima | 14 |
| Mínimo por hoja | 30 |
| Class weight | balanced |
| PR-AUC promedio | 0.8631 |
| ROC-AUC promedio | 0.9855 |
| Recall promedio | 0.9284 |
| F1-score promedio | 0.6843 |
| Tiempo de optimización | 1,069.77 segundos |

Random Forest obtuvo el mejor PR-AUC promedio entre los modelos evaluados, aunque presentó un costo computacional considerablemente mayor.

## 6. Comparación de modelos optimizados

| Métrica | Regresión logística | Random Forest |
|---|---:|---:|
| PR-AUC | 0.8567 | **0.8631** |
| ROC-AUC | 0.9844 | **0.9855** |
| Recall | 0.6661 | **0.9284** |
| F1-score | **0.7452** | 0.6843 |

**Modelo seleccionado:** Random Forest.

La selección se realizó utilizando PR-AUC como criterio principal, definido antes de la evaluación final.

El recall y el F1-score reportados corresponden al umbral de clasificación de 0.50. Sus diferencias también dependen de las estrategias de ponderación utilizadas, por lo que no deben atribuirse exclusivamente al algoritmo.

## 7. Evaluación final independiente

Posteriormente, Random Forest se evaluó sobre 40,141 observaciones reservadas correspondientes al corte del 30 de septiembre de 2025.

| Métrica | Resultado |
|---|---:|
| Precision | 0.5377 |
| Recall | 0.9321 |
| F1-score | 0.6819 |
| ROC-AUC | 0.9870 |
| PR-AUC | 0.8641 |

La proximidad entre el PR-AUC promedio de validación (0.8631) y el PR-AUC final (0.8641) indica estabilidad dentro de los periodos simulados.

La evaluación final no se utilizó para modificar los hiperparámetros seleccionados.

## 8. Trazabilidad con MLflow

Los resultados están asociados a las siguientes ejecuciones:

| Modelo | Run ID |
|---|---|
| Regresión logística | `a071b06d029642ef86912f15d578f5c2` |
| Random Forest | `9f1a167022214509af181b39c2d0e047` |

Los CSV se registraron como artefactos dentro de la carpeta `gridsearch/` de cada ejecución.

Las evidencias visuales del seguimiento se encuentran en el directorio superior:

`evidencias/mlflow/`

El código de entrenamiento y optimización se documenta en:

`fuentes/03_modelado_experimentacion.ipynb`

## 9. Interpretación y limitaciones

Los resultados permiten comparar la capacidad predictiva, estabilidad temporal y costo computacional de los algoritmos evaluados.

Sin embargo, deben considerarse las siguientes limitaciones:

- Los datos fueron generados sintéticamente y no representan una cartera bancaria real.
- La búsqueda de hiperparámetros fue acotada para controlar el consumo de recursos.
- Los resultados de validación corresponden a tres ventanas temporales específicas.
- La evaluación de desempeño no demuestra por sí misma una reducción efectiva de pérdidas crediticias.
- Una eventual implementación productiva requeriría validación con datos reales, análisis de sesgos, monitoreo y aprobación de las áreas responsables.

## 10. Reproducibilidad

Los resultados pueden reproducirse utilizando el notebook de modelado, los datasets versionados y los parámetros documentados.

Los archivos CSV permiten auditar las configuraciones evaluadas sin repetir los entrenamientos.

---

**Proyecto:** FinPredict — Actividad 5  
**Etapa:** Optimización y comparación de modelos  
**Herramientas:** scikit-learn, GridSearchCV y MLflow  
**Estado:** Experimentos optimizados, registrados y respaldados.  
**Uso:** Evidencia académica de evaluación y selección de hiperparámetros.
