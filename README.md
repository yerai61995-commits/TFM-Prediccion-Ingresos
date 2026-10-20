# Predicción de ingresos comerciales mediante Machine Learning

**Proyecto 1 del TFM de Data Science con IA — The Big School**

## 1. Objetivo

Desarrollar y evaluar un sistema de predicción del importe comercial de la siguiente jornada con registros de transacciones, utilizando datos históricos de una empresa minorista.

El objetivo empresarial es proporcionar información que pueda apoyar la planificación operativa y la asignación de recursos. Las predicciones no constituyen garantías de ingresos ni previsiones contables oficiales.

## 2. Dataset

Se utiliza el conjunto de datos **Online Retail**, distribuido originalmente como `Online Retail.xlsx`.

- Registros originales: **541.909**.
- Periodo: diciembre de 2010 a diciembre de 2011.
- Moneda: libra esterlina (£).
- Jornadas con registros: **305**.
- Jornadas consideradas para el modelado: **304**.

El archivo original no se distribuye en este repositorio. Para reproducir el análisis es necesario obtenerlo de su fuente original y comprobar las condiciones de uso.

## 3. Preparación y calidad de datos

El procesamiento incluye:

1. Análisis de valores ausentes y duplicados.
2. Identificación de cancelaciones y cantidades negativas.
3. Revisión de importes anómalos y operaciones administrativas.
4. Inclusión de descuentos identificados mediante el código `D`.
5. Exclusión de los conceptos `AMAZONFEE`, `BANK CHARGES` y `CRUK`.
6. Agregación de las operaciones comerciales por fecha.

El importe comercial agregado de las 305 jornadas fue de **10.006.501,62 £**, bajo las reglas de tratamiento definidas.

Se excluye del modelado el 9 de diciembre de 2011 debido a la posible incompletitud de esa jornada y a una operación excepcional cancelada.

Los días sin registros no se convierten automáticamente en días con ingresos cero.

## 4. Ingeniería de variables

Se utilizan las siguientes variables predictoras:

- Día de la semana.
- Mes.
- Importe comercial de la jornada registrada anterior.
- Importe comercial de dos jornadas registradas atrás.
- Importe comercial de siete jornadas registradas atrás.
- Media de los importes de las siete jornadas registradas anteriores.

Estas variables permiten estimar una jornada utilizando información histórica conocida. Las jornadas con registros no equivalen necesariamente a días naturales consecutivos.

## 5. División de los datos

La división se realiza respetando el orden temporal:

| Conjunto | Observaciones |
|---|---:|
| Entrenamiento | 207 |
| Validación | 45 |
| Prueba final | 45 |

Se evita utilizar aleatoriamente observaciones futuras para entrenar el modelo.

La prueba final se reserva para evaluar el rendimiento de los enfoques seleccionados, sin reajustarlos a partir de esos resultados.

## 6. Modelos comparados

Se evalúan cinco enfoques:

1. Predicción basada en la última jornada registrada.
2. Media de siete jornadas anteriores.
3. Regresión lineal.
4. Random Forest Regressor.
5. Red neuronal MLP Regressor.

### Resultados de validación

| Modelo | MAE (£) |
|---|---:|
| Última jornada | 19.191,79 |
| Media de siete jornadas | 13.807,43 |
| Regresión lineal | 13.011,27 |
| Random Forest | **11.622,47** |
| Red neuronal MLP | 16.924,73 |

Random Forest obtiene el menor error absoluto medio durante la validación.

El MLP se incluye como experimento adicional; su entrenamiento presenta una advertencia de convergencia al alcanzar el límite configurado de iteraciones.

## 7. Explicabilidad

Se emplean técnicas para analizar el comportamiento de Random Forest:

- Importancia de variables proporcionada por el modelo.
- Importancia por permutación.
- Valores SHAP para explicar predicciones individuales.

Estas herramientas permiten interpretar qué variables contribuyen a las predicciones, sin establecer relaciones causales.

## 8. Evaluación final

Se comparan la media de siete jornadas y Random Forest sobre 45 observaciones de prueba.

| Modelo | MAE (£) | RMSE (£) |
|---|---:|---:|
| **Media de siete jornadas** | **14.015,88** | **18.744,80** |
| Random Forest | 18.924,47 | 23.767,55 |

Aunque Random Forest presenta un mejor resultado en validación, la media de siete jornadas obtiene menor error durante la prueba final.

Por tanto, se recomienda **la media de siete jornadas como referencia operativa para este experimento**, por su rendimiento observado y simplicidad.

Los errores de predicción no representan automáticamente pérdidas económicas reales.

## 9. Dashboard de Tableau

Se ha desarrollado un dashboard que incluye:

- Evolución histórica del importe comercial.
- Comparación entre ingresos reales y predicciones.
- Indicador MAE de la media de siete jornadas.
- Indicador MAE de Random Forest.

El archivo se encuentra en:

`dashboard/TFM_Prediccion_Ingresos.twbx`

## 10. Estructura del repositorio

```text
TFM-Prediccion-Ingresos/
├── notebooks/
│   ├── 01_analisis_exploratorio_final.ipynb
│   ├── 01_analisis_exploratorio_documentado.ipynb
│   └── 01_analisis_exploratorio_original.ipynb
├── dashboard/
│   └── TFM_Prediccion_Ingresos.twbx
├── resultados/
│   ├── ingresos_tableau.csv
│   └── predicciones_tableau.csv
├── README.md
└── requirements.txt
```

El archivo `01_analisis_exploratorio_final.ipynb` es la versión principal del análisis: incluye explicaciones, código y resultados de ejecución.

## 11. Cómo reproducir el proyecto

1. Descargar el repositorio y obtener `Online Retail.xlsx` de su fuente original.
2. Instalar las dependencias indicadas en `requirements.txt`.
3. Abrir `notebooks/01_analisis_exploratorio_final.ipynb` en Google Colab.
4. Subir `Online Retail.xlsx` al entorno de ejecución y comprobar que la ruta de lectura del notebook coincide con su ubicación.
5. Ejecutar las celdas en orden.
6. Revisar las métricas obtenidas y los archivos de resultados.

Los CSV destinados a Tableau utilizan separador `;`, coma decimal y codificación `utf-8-sig`.

Las celdas que utilizan `google.colab` son específicas de Colab y requieren adaptación u omisión al ejecutar el notebook en otro entorno.

El notebook final se ha ejecutado íntegramente en Google Colab y se han comprobado los resultados principales. Queda pendiente una prueba independiente de reproducibilidad en un entorno limpio.

## 12. Limitaciones

- No se dispone de confirmación sobre la actividad comercial de los días sin registros.
- Existe incertidumbre sobre la naturaleza de algunos duplicados.
- Las reglas de clasificación de las operaciones comerciales son decisiones analíticas documentadas.
- El periodo de observación es limitado.
- El experimento realiza predicciones de un paso con información histórica observada, no previsiones de múltiples jornadas futuras sin actualización.
- La diferencia entre validación y prueba evidencia posibles dificultades de generalización.

## 13. Aplicación empresarial

El sistema podría utilizarse para generar estimaciones diarias una vez actualizados los datos de ventas.

Un posible despliegue incluiría generación automatizada de predicciones, almacenamiento de resultados, seguimiento de MAE y RMSE, análisis del sesgo y revisión periódica del rendimiento.

No se ha implementado un servicio de producción; se presenta una propuesta conceptual de despliegue.

## 14. Estado del proyecto

- Análisis exploratorio y preparación de datos: completados.
- Modelado y comparación: completados.
- Evaluación final y explicabilidad: completadas.
- Dashboard de Tableau: incorporado al repositorio.
- Resultados CSV: incorporados al repositorio.
- Notebook académico: ejecutado y documentado.
- Revisión de requisitos oficiales y documentación final del TFM: pendiente.
