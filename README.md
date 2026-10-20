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

## 15. Arquitectura y flujo completo del sistema

El proyecto sigue un proceso de análisis predictivo que transforma registros comerciales en información para la planificación empresarial.

```text
Dataset Online Retail
        |
        v
Exploración y auditoría
        |
        v
Limpieza y clasificación de operaciones
        |
        v
Cálculo de ingresos comerciales diarios
        |
        v
Creación de variables temporales
        |
        v
División cronológica de los datos
        |
        v
Entrenamiento y validación de cinco modelos
        |
        v
Explicabilidad de Random Forest
        |
        v
Evaluación final de los enfoques seleccionados
        |
        v
Métricas de negocio y conclusiones
        |
        v
Exportación de resultados y dashboard Tableau
```

**Herramientas utilizadas:** Python, Pandas, NumPy, Scikit-learn, SHAP, Matplotlib, Google Colab, GitHub y Tableau.

La arquitectura corresponde a un prototipo analítico. No se ha desarrollado una aplicación de producción.

## 16. Dificultades encontradas y soluciones

Durante el desarrollo se identificaron diferentes problemas técnicos y metodológicos.

### 16.1. Cancelaciones y operaciones administrativas

El dataset contiene cancelaciones, descuentos y operaciones que no representan directamente ingresos comerciales ordinarios.

**Solución:** identificar las operaciones mediante sus códigos y características, conservar las cancelaciones correspondientes y documentar los criterios de inclusión y exclusión.

Esta decisión evita interpretar todas las líneas como ventas positivas, aunque las reglas aplicadas siguen siendo supuestos analíticos.

### 16.2. Jornada con valores excepcionales

El 9 de diciembre de 2011 presenta una operación comercial excepcional posteriormente cancelada. Además, la última jornada del dataset podría estar incompleta.

**Solución:** analizar las operaciones implicadas y excluir esta jornada del experimento predictivo, documentando la decisión para no interpretar su valor como un comportamiento ordinario.

### 16.3. Días sin registros

Existen fechas naturales sin observaciones en el dataset.

**Solución:** no convertir automáticamente esas fechas en ingresos cero. El modelo predice la siguiente jornada con registros, utilizando observaciones anteriores.

Esta elección limita la posibilidad de realizar previsiones para cualquier fecha natural.

### 16.4. Diferencia entre validación y prueba final

Random Forest obtuvo el mejor resultado durante la validación, pero su error aumentó durante la evaluación final.

**Solución:** mantener intacta la prueba final, comparar los resultados y seleccionar la media de siete jornadas como referencia operativa del experimento.

Esto demuestra la importancia de evaluar la capacidad de generalización antes de recomendar un modelo.

### 16.5. Entrenamiento de la red neuronal

El modelo MLP alcanzó el límite configurado de iteraciones sin converger completamente.

**Solución:** documentar la advertencia y conservarlo como experimento comparativo, sin presentarlo como un modelo optimizado.

### 16.6. Importación de datos en Tableau

Al importar los CSV aparecieron problemas de interpretación de valores decimales por la configuración regional.

**Solución:** exportar los archivos utilizando separador de punto y coma, coma decimal y codificación UTF-8 con BOM.

También se corrigió el cálculo de los indicadores MAE utilizando el promedio de los errores absolutos, en lugar de su suma.

## 17. Propuesta de despliegue y mantenimiento

### 17.1. Funcionamiento operativo

Se propone una ejecución **por lotes (batch)** después del cierre de cada jornada con transacciones.

El proceso realizaría las siguientes operaciones:

1. Recibir los datos comerciales actualizados.
2. Aplicar las mismas reglas de limpieza y clasificación.
3. Actualizar el histórico de importes diarios.
4. Calcular las variables predictoras a partir de jornadas anteriores.
5. Generar la estimación de la siguiente jornada con registros.
6. Guardar la predicción y actualizar el dashboard.

Para la referencia operativa seleccionada, la media de siete jornadas, no es necesario realizar entrenamiento periódico: basta con actualizar las observaciones disponibles.

### 17.2. Monitorización

Una vez conocido el importe real, se compararía con la predicción correspondiente.

Se monitorizarían:

- MAE y RMSE mediante ventanas temporales.
- Frecuencia de sobreestimaciones e infraestimaciones.
- Cambios relevantes en la distribución de ingresos.
- Aparición de nuevas anomalías o categorías de operaciones.
- Calidad, disponibilidad y retrasos de los datos.

Una degradación persistente de las métricas activaría una revisión del sistema.

### 17.3. Reentrenamiento y actualización

Si se incorporase un modelo entrenado, como Random Forest, se podría revisar su rendimiento mensualmente y considerar un reentrenamiento trimestral o antes si se detectara degradación significativa.

La frecuencia sería una propuesta inicial, no una periodicidad validada experimentalmente.

Cualquier actualización debería:

1. Guardar una versión del código y de los datos utilizados.
2. Mantener la trazabilidad de las variables y transformaciones.
3. Evaluar el modelo candidato con datos posteriores no utilizados en su entrenamiento.
4. Compararlo con la referencia operativa existente.
5. Sustituir el modelo únicamente si demuestra una mejora suficiente y estable.

### 17.4. Herramientas posibles

En un escenario empresarial podrían utilizarse tareas programadas de Python, almacenamiento centralizado de datos, registros de ejecución, herramientas de versionado y un dashboard actualizado periódicamente.

Una API o un contenedor Docker serían opciones de ampliación, pero no son necesarios para el prototipo desarrollado.

### 17.5. Limitación de la propuesta

El despliegue es exclusivamente conceptual. No se han implementado mecanismos automáticos de producción, reentrenamiento ni monitorización continua.
- Dashboard de Tableau: incorporado al repositorio.
- Resultados CSV: incorporados al repositorio.
- Notebook académico: ejecutado y documentado.
- Revisión de requisitos oficiales y documentación final del TFM: pendiente.
