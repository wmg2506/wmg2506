<p align="right">
  <a href="./README.md">English</a> · <strong>Español</strong>
</p>

# Auditoría de un modelo municipal de alerta temprana

> Un caso de gobierno del dato con criterio sénior: decidir si la evidencia justificaba un uso predictivo, no limitarse a elegir el algoritmo más sofisticado.

**Autora:** Marta González Vázquez  
**Contexto:** auditoría de datos y modelado para alerta temprana  
**Estado:** terminada · septiembre de 2026  
**Alcance:** 185 municipios  
**Tecnologías:** Python · pandas · scikit-learn · Jupyter · calidad del dato · validación temporal  
**Referencia técnica:** flujo EWS Madrid; FIES es el objetivo de experiencia de inseguridad alimentaria utilizado por el modelo auditado.

## Conclusión ejecutiva

El flujo auditado no produjo un modelo capaz de predecir de forma fiable FIES 2024 en municipios excluidos del entrenamiento. Las métricas originales elevadas describían principalmente el ajuste sobre datos conocidos y no demostraban capacidad de predicción futura.

No se guardó ningún modelo final porque ningún candidato superó una referencia sencilla que predice la mediana del entrenamiento.

## Qué se auditó

La revisión cubrió la cadena completa de modelado para dos objetivos continuos:

- probabilidad FIES moderada o severa;
- probabilidad FIES severa;
- linaje y significado documentado del objetivo;
- relación entre objetivos anuales y predictores mensuales;
- tratamiento de ausentes;
- reglas de corte temporal;
- separación entre entrenamiento y prueba;
- selección de modelos y evaluación fuera de muestra.

Aquí se publica únicamente evidencia metodológica agregada. Los datasets, credenciales, notebooks y materiales de la organización permanecen privados.

## Hallazgos principales

1. **El conjunto de prueba reservado no se utilizaba correctamente.** La evaluación original daba demasiado peso al resultado de entrenamiento.
2. **Existía una incompatibilidad de granularidad.** Cada municipio tenía 12 filas mensuales de predictores y un único objetivo FIES anual, lo que podía multiplicar artificialmente el objetivo.
3. **El relleno hacia atrás introducía información futura.** En 64.257 celdas —el 7,1 % de los ausentes originales— se utilizaron observaciones posteriores.
4. **El redondeo del objetivo eliminaba información.** Los dos objetivos continuos pasaron de 165 valores distintos a 33 y 13.
5. **La fórmula del objetivo no era verificable.** No se encontró documentación auditable sobre la construcción de las dos probabilidades FIES.

## Correcciones aplicadas

- Una fila analítica por municipio y año.
- Corte de predictores en el 31 de diciembre de 2023 para explicar FIES 2024.
- Uso de la matriz original y eliminación del relleno hacia atrás.
- Imputación aprendida únicamente dentro de cada partición de entrenamiento.
- Validación repetida con 5 folds y 5 repeticiones, utilizando las mismas particiones municipales para todos los modelos.
- Comparación obligatoria con un DummyRegressor basado en la mediana.
- Conservación de los objetivos continuos sin redondeo.

## Resultados de validación

| Objetivo | MAE baseline mediana | Mejor candidato | MAE candidato | Decisión |
|---|---:|---|---:|---|
| Moderada o severa | **6,688** | Ridge log1p | 6,828 | No validado |
| Severa | **1,227** | Ridge log1p | 1,467 | No validado |

Se compararon Random Forest, Gradient Boosting, modelos lineales regularizados y transformaciones logarítmicas del objetivo. Ninguno mejoró el baseline.

El sobreajuste fue especialmente visible en Gradient Boosting para el objetivo moderada-severa: MAE de entrenamiento **1,009** frente a MAE de validación **8,139**.

## Recomendaciones

1. **No presentar las estimaciones 2025 o 2026 como predicciones fiables** con la evidencia actual.
2. Conseguir observaciones FIES fiables de varios años y documentar la fórmula del objetivo antes de construir un modelo temporal predictivo.
3. Si solo existe FIES 2024, limitar el producto a distribuciones descriptivas, asociaciones y perfiles municipales.
4. Mantener como controles obligatorios los cortes temporales, el preprocesamiento dentro de cada fold y la comparación con un baseline.
5. Conservar trazabilidad del objetivo, imputaciones, particiones municipales, métricas y decisiones de modelado.

## Qué demuestra este caso

- detección de fuga de información, sobreajuste e incompatibilidad de grano;
- validación honesta frente a una referencia sencilla;
- corrección de un flujo completo desde datos hasta modelo;
- capacidad para detener un modelo no sustentado en lugar de optimizar una métrica engañosa;
- traducción de hallazgos técnicos en recomendaciones operativas.

---

**Marta González Vázquez**  
Senior IT & Operaciones Críticas · Calidad del dato · Analítica · IA aplicada

