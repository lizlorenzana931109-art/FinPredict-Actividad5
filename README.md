# FinPredict | Actividad 5
### Preparación de datos, Machine Learning y gestión de experimentos con MLflow

**Proyecto académico con enfoque empresarial | FinComercial, S.A.**

FinPredict es un proyecto de inteligencia artificial orientado a la **predicción temprana del incumplimiento en créditos hipotecarios**, mediante el análisis de información financiera, comportamiento de pagos y características contractuales.

El objetivo es explorar cómo los modelos de Machine Learning pueden apoyar la identificación anticipada de créditos con mayor riesgo y facilitar la priorización de estrategias preventivas, siempre bajo supervisión humana.

Este repositorio documenta el ciclo completo de experimentación: desde la **generación de un dataset sintético desde cero** hasta la preparación de datos, entrenamiento, optimización, evaluación y seguimiento de modelos con MLflow.

---

## 1. Construcción del dataset desde cero

Una característica central de este proyecto es que **no utilizamos un dataset descargado ni información real de clientes**.

Diseñamos y generamos nuestros propios datos sintéticos, simulando la estructura de información utilizada en la administración de créditos hipotecarios.

Se construyeron tres fuentes relacionadas:

| Fuente | Registros | Contenido |
|---|---:|---|
| Contratos | 50,000 | Características generales de créditos hipotecarios. |
| Obligaciones | 1,475,182 | Importes exigibles y calendario de obligaciones. |
| Movimientos | 1,584,222 | Pagos y movimientos financieros simulados. |

A partir de estas fuentes construimos una tabla analítica con **390,661 observaciones**, organizadas temporalmente para estudiar el comportamiento crediticio y predecir eventos de incumplimiento.

El proceso incluyó reglas de generación de datos, relaciones entre tablas, agregaciones financieras y construcción de la variable objetivo.

## 2. Preparación y calidad de datos

Para reproducir problemas habituales de calidad de información, incorporamos intencionalmente incidencias al dataset sintético.

Posteriormente desarrollamos un proceso de limpieza y validación que contempló:

- Identificación y eliminación de registros duplicados.
- Tratamiento de valores faltantes.
- Estandarización de variables categóricas.
- Detección y tratamiento de importes inválidos.
- Normalización de fechas.
- Exclusión de identificadores personales sintéticos del dataset de modelado.
- Generación de controles de calidad y archivos de trazabilidad.

**Resultado:** dataset preparado de 390,661 observaciones y 34 columnas, incluyendo variables auxiliares de auditoría.

Para la experimentación se utilizaron 14 variables predictoras financieras y operativas.

## 3. Entrenamiento y optimización

Se desarrollaron dos modelos supervisados de clasificación:

**Regresión logística:** modelo de referencia, con menor complejidad y mayor facilidad de interpretación.

**Random Forest:** modelo basado en ensambles de árboles de decisión, capaz de representar relaciones no lineales entre las variables.

Ambos fueron optimizados mediante `GridSearchCV` y evaluados utilizando **tres particiones temporales expansivas**, con el objetivo de respetar la secuencia de los datos y reducir riesgos de fuga temporal.

La métrica principal para seleccionar el modelo fue **Average Precision (PR-AUC)**.

## 4. Resultados comparativos

| Métrica de validación | Regresión logística | Random Forest |
|---|---:|---:|
| PR-AUC | 0.8567 | **0.8631** |
| ROC-AUC | 0.9844 | **0.9855** |
| Recall | 0.6661 | **0.9284** |
| F1-score | **0.7452** | 0.6843 |

**Modelo seleccionado: Random Forest**, debido a su mejor PR-AUC promedio de validación.

### Evaluación final independiente

El modelo seleccionado fue evaluado en un conjunto reservado de **40,141 observaciones**, sin utilizarlo para la selección de hiperparámetros.

| Indicador | Resultado |
|---|---:|
| PR-AUC | 0.8641 |
| ROC-AUC | 0.9870 |
| Recall | 93.21 % |
| Precision | 53.77 % |
| F1-score | 0.6819 |
| Incumplimientos correctamente identificados | 2,127 |
| Falsas alertas | 1,829 |

Los resultados muestran una capacidad prometedora para identificar eventos de incumplimiento en el entorno simulado. No obstante, también evidencian una carga potencial de revisión operativa, por lo que no se recomienda automatizar decisiones sobre clientes.

## 5. Seguimiento de experimentos con MLflow

Implementamos **MLflow Tracking 3.17.1** para registrar y consultar los experimentos.

Se documentaron:

- Ejecuciones de regresión logística y Random Forest.
- Hiperparámetros evaluados y configuración óptima.
- Métricas de validación y evaluación final.
- Resultados de GridSearchCV.
- Pipelines entrenados y serializados con Joblib.
- Matriz de confusión y artefactos de evaluación.

Las evidencias visuales y los resultados de optimización están disponibles en:

`evidencias/mlflow/`

Los pipelines se conservaron como artefactos de MLflow; no se implementó un despliegue productivo ni un registro formal de versiones en MLflow Model Registry.

## 6. Estructura del repositorio

| Directorio | Contenido |
|---|---|
| `datos/datos_ini/` | Fuentes sintéticas, dataset inicial y manifiestos. |
| `datos/datos_limp/` | Datos preparados y conjuntos de desarrollo y evaluación. |
| `fuentes/01_construccion_dataset.ipynb` | Generación y construcción de los datos sintéticos. |
| `fuentes/02_preparacion_datos.ipynb` | Limpieza, controles de calidad y preparación. |
| `fuentes/03_modelado_experimentacion.ipynb` | Entrenamiento, GridSearchCV, evaluación y MLflow. |
| `evidencias/mlflow/` | Capturas de seguimiento y artefactos documentados. |
| `resultados` | CSV con resultados de optimización. |
| `documentacion/` | Informe comparativo, evaluación de riesgos y estrategia MLOps. |

## 7. Herramientas utilizadas

- **Python:** lenguaje principal de desarrollo.
- **Google Colab:** entorno de ejecución.
- **pandas y NumPy:** transformación y análisis de datos.
- **scikit-learn:** pipelines, entrenamiento, evaluación y GridSearchCV.
- **Matplotlib:** visualización y análisis de resultados.
- **MLflow:** seguimiento de experimentos y artefactos.
- **GitHub:** control de versiones y documentación técnica.
- **Google Drive:** respaldo de modelos y experimentos.

## 8. Enfoque empresarial, ética y MLOps

La selección de un modelo no depende exclusivamente de obtener la métrica más alta.

En FinPredict también consideramos el equilibrio entre capacidad predictiva, complejidad técnica, falsos positivos, trazabilidad y viabilidad operativa.

El proyecto identifica como prioridades para una futura implementación:

- Validación con datos reales, representativos y autorizados.
- Evaluación de sesgos y desempeño por segmentos.
- Explicabilidad y documentación de las predicciones.
- Calibración de probabilidades y definición de umbrales operativos.
- Monitoreo de datos, desempeño y deriva del modelo.
- Versionamiento y mecanismos controlados de reentrenamiento.
- Protección de información financiera y supervisión humana.

**Recomendación:** continuar hacia una validación piloto controlada. Los resultados obtenidos con datos sintéticos no constituyen autorización para el uso productivo ni demuestran por sí mismos beneficios financieros reales.

## 9. Uso de inteligencia artificial generativa

Durante el proyecto se utilizó **ChatGPT como herramienta de apoyo** para estructurar y revisar código, explorar alternativas de modelado, resolver dudas de implementación, organizar la documentación y mejorar la presentación de resultados.

Las métricas reportadas proceden de las ejecuciones experimentales realizadas en Google Colab y registradas mediante MLflow.

La revisión de resultados, las decisiones metodológicas y la responsabilidad de la entrega corresponden a la autora.

---

## Autora

**Lizbeth Bruno Lorenzana**

Maestría en Inteligencia Artificial  
Asignatura: Gestión de Proyectos de Inteligencia Artificial  
Proyecto académico: FinPredict — FinComercial, S.A.

**Repositorio:** https://github.com/lizlorenzana931109-art/FinPredict-Actividad5

*FinPredict demuestra el ciclo experimental de una solución predictiva, desde el diseño de datos sintéticos hasta una recomendación técnica y empresarial sustentada en evidencia.*
