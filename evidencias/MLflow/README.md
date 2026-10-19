# Evidencias de MLflow | FinPredict

## 1. Descripción

Este directorio contiene las evidencias visuales del seguimiento de experimentos realizado con **MLflow 3.17.1** durante la Actividad 5 del proyecto FinPredict.

El objetivo es demostrar la trazabilidad, comparación y documentación de modelos supervisados de Machine Learning para la predicción del incumplimiento hipotecario a 90 días.

Se utilizó **MLflow Tracking** para registrar los hiperparámetros, métricas, ejecuciones y artefactos correspondientes a dos algoritmos:

- Regresión logística.
- Random Forest.

Los experimentos se realizaron con datos completamente sintéticos y fueron desarrollados en Google Colab, utilizando scikit-learn.

## 2. Configuración experimental

| Característica | Configuración |
|---|---|
| Experimento MLflow | `FinPredict_Actividad5` |
| Versión MLflow | 3.17.1 |
| Dataset | `finpredict_desarrollo_v1.parquet` |
| Observaciones de desarrollo | 350,520 |
| Variables predictoras | 14 |
| Variable objetivo | `incumplimiento_90d` |
| Validación | 3 particiones temporales expansivas |
| Optimización | GridSearchCV |
| Métrica principal | Average Precision (PR-AUC) |
| Infraestructura | Google Colab — CPU |

La evaluación final se mantuvo reservada durante el entrenamiento, validación y selección de hiperparámetros.

## 3. Experimentos registrados

Se documentaron dos ejecuciones en MLflow Tracking.

| Modelo | Identificador de ejecución |
|---|---|
| Regresión logística | `a071b06d029642ef86912f15d578f5c2` |
| Random Forest | `9f1a167022214509af181b39c2d0e047` |

Ambas ejecuciones contienen parámetros, métricas por partición temporal, resultados promedio y archivos de soporte.

## 4. Comparación de modelos optimizados

| Métrica | Regresión logística | Random Forest |
|---|---:|---:|
| PR-AUC | 0.8567 | **0.8631** |
| ROC-AUC | 0.9844 | **0.9855** |
| Recall | 0.6661 | **0.9284** |
| F1-score | **0.7452** | 0.6843 |
| Tiempo de optimización | 18.83 s | 1,069.77 s |

Random Forest obtuvo el mayor PR-AUC promedio y fue seleccionado como candidato para la evaluación final.

La comparación también evidencia una diferencia considerable en costo computacional, que deberá contemplarse en una eventual implementación empresarial.

## 5. Hiperparámetros del modelo seleccionado

La configuración seleccionada mediante GridSearchCV fue:

| Parámetro | Valor |
|---|---|
| Algoritmo | RandomForestClassifier |
| Número de árboles | 120 |
| Profundidad máxima | 14 |
| Mínimo de observaciones por hoja | 30 |
| Ponderación de clases | balanced |
| Número de predictores | 14 |

Se utilizó un pipeline que incorpora el tratamiento de ingresos faltantes y mantiene la consistencia de la relación pago-ingreso.

## 6. Evaluación final fuera de muestra

Random Forest se evaluó sobre 40,141 observaciones reservadas correspondientes al corte del 30 de septiembre de 2025.

| Métrica | Resultado |
|---|---:|
| Precision | 0.5377 |
| Recall | 0.9321 |
| F1-score | 0.6819 |
| ROC-AUC | 0.9870 |
| PR-AUC | 0.8641 |

### Matriz de confusión

| Resultado | Registros |
|---|---:|
| Verdaderos negativos | 36,030 |
| Falsos positivos | 1,829 |
| Falsos negativos | 155 |
| Verdaderos positivos | 2,127 |

El modelo identificó **2,127 de los 2,282 eventos reales de incumplimiento** de la simulación.

Estos resultados sugieren una capacidad de discriminación estable dentro del entorno experimental; no constituyen evidencia de rendimiento en una cartera bancaria real.

## 7. Evidencias visuales

Las capturas de pantalla de MLflow documentan cuatro componentes:

| Evidencia | Contenido | Propósito |
|---|---|---|
| 01 — Comparación de experimentos | Dos ejecuciones y métricas de validación | Demostrar el registro y la comparación de modelos. |
| 02 — Seguimiento del modelo | Métricas, identificador de ejecución y etiquetas | Evidenciar la trazabilidad experimental. |
| 03 — Parámetros y evaluación final | Hiperparámetros y métricas de evaluación | Sustentar la configuración seleccionada y sus resultados. |
| 04 — Artefactos | Resultados GridSearchCV, pipeline y matriz de confusión | Demostrar la conservación de archivos asociados al experimento. |

Las capturas proceden de la interfaz real de MLflow Tracking.

## 8. Artefactos registrados

Dentro de la ejecución de Random Forest se conservaron:

- `gridsearch/`: resultados completos de la búsqueda de hiperparámetros en CSV.
- `modelos/`: pipeline optimizado serializado mediante Joblib.
- `evaluacion_final/`: matriz de confusión del conjunto reservado.

Los experimentos también se respaldaron en Google Drive mediante una base SQLite y copias verificadas de los artefactos.

**Alcance:** los pipelines se registraron como artefactos `.joblib`, no como versiones publicadas en MLflow Model Registry.

El almacenamiento SQLite conserva rutas asociadas al entorno original de Colab, por lo que restaurar la interfaz en otro entorno requiere ajustar la configuración de almacenamiento de artefactos.

## 9. Consideraciones de reproducibilidad y MLOps

El uso de MLflow permite mantener un historial verificable de las decisiones de modelado, incluyendo:

- Configuraciones de hiperparámetros.
- Resultados por partición temporal.
- Métricas promedio y de evaluación final.
- Identificación de ejecuciones.
- Artefactos de optimización y evaluación.

Para una implementación empresarial se recomendaría incorporar un servidor centralizado de MLflow, almacenamiento administrado de artefactos, control de versiones de modelos, monitoreo de deriva y procedimientos de aprobación antes de producción.

## 10. Referencias del proyecto

**Notebook de modelado:**

`fuentes/03_modelado_experimentacion.ipynb`

**Datos preparados:**

`datos/datos_limp/`

**Repositorio:**

https://github.com/lizlorenzana931109-art/FinPredict-Actividad5

---

**Proyecto:** FinPredict — Actividad 5  
**Etapa:** Modelado, optimización y seguimiento de experimentos  
**Herramienta de seguimiento:** MLflow Tracking 3.17.1  
**Estado:** Experimentos registrados, evaluados y respaldados.  
**Uso:** Evidencia académica de experimentación y trazabilidad MLOps.
