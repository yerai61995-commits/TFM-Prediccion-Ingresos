# Predicción de Ingresos Comerciales mediante Machine Learning

**Proyecto 1 — Trabajo Fin de Máster de Data Science con Inteligencia Artificial**

**Centro de formación:** The Big School  
**Autor:** Yerai Marín Segura  
**Tecnologías:** Python · Pandas · NumPy · Scikit-learn · SHAP · Matplotlib · Google Colab · Tableau · GitHub

---

## 1. Descripción del proyecto

Este proyecto desarrolla y evalúa diferentes métodos de predicción de ingresos comerciales utilizando datos históricos de una empresa de comercio electrónico.

El objetivo es estimar los ingresos correspondientes a la siguiente jornada con registros comerciales, comparando métodos estadísticos sencillos con algoritmos de Machine Learning.

El trabajo abarca el proceso completo de análisis de datos:

1. Exploración y auditoría del dataset.
2. Limpieza y preparación de los registros comerciales.
3. Construcción de una serie temporal de ingresos.
4. Ingeniería de variables predictoras.
5. Entrenamiento y validación de diferentes métodos.
6. Evaluación de resultados sobre datos de prueba.
7. Interpretación del comportamiento de los modelos.
8. Análisis empresarial de los errores.
9. Generación de una previsión adicional.
10. Visualización de resultados mediante Tableau.

El proyecto tiene un enfoque académico y experimental. Su finalidad es estudiar la utilidad de diferentes métodos predictivos y su posible aplicación como herramientas de apoyo a la planificación empresarial.

**Las predicciones no constituyen garantías de ingresos ni previsiones contables oficiales.**

---

## 2. Objetivos

### Objetivo general

Desarrollar y evaluar un sistema de predicción de ingresos comerciales basado en datos históricos, identificando los métodos que presentan mejores resultados durante los periodos analizados.

### Objetivos específicos

- Analizar la calidad de los datos comerciales originales.
- Identificar y tratar cancelaciones, devoluciones, duplicados y operaciones administrativas.
- Construir una serie temporal de ingresos comerciales por jornada.
- Crear variables predictoras a partir de información histórica.
- Comparar métodos estadísticos y algoritmos de aprendizaje supervisado.
- Evaluar el rendimiento mediante MAE y RMSE.
- Interpretar las predicciones mediante técnicas de explicabilidad.
- Analizar los errores de infraestimación y sobreestimación.
- Estudiar sus posibles implicaciones empresariales mediante una simulación.
- Generar una previsión para la siguiente jornada registrada.
- Presentar los resultados mediante un dashboard de Tableau.

---

## 3. Dataset utilizado

Se utiliza el conjunto de datos **Online Retail**, disponible en el repositorio UCI Machine Learning Repository.

**Fuente oficial:** https://archive.ics.uci.edu/dataset/352/online+retail

**Referencia:** Chen, D. (2015). *Online Retail* [Dataset]. UCI Machine Learning Repository.

**DOI:** https://doi.org/10.24432/C5BW33

**Licencia:** Creative Commons Attribution 4.0 International (CC BY 4.0).

### Características principales

| Característica | Valor |
|---|---|
| Dataset | Online Retail |
| Archivo original | `Online Retail.xlsx` |
| Registros originales | 541.909 |
| Variables originales | 8 |
| Periodo | Diciembre de 2010 – diciembre de 2011 |
| Moneda | Libra esterlina (£) |
| Jornadas comerciales registradas | 305 |
| Jornadas consideradas inicialmente para modelado | 304 |
| Observaciones finales para modelado | 297 |

### Variables originales

| Variable | Descripción |
|---|---|
| `InvoiceNo` | Identificador de factura |
| `StockCode` | Código del producto o concepto |
| `Description` | Descripción de la operación |
| `Quantity` | Cantidad de unidades |
| `InvoiceDate` | Fecha y hora de la transacción |
| `UnitPrice` | Precio unitario |
| `CustomerID` | Identificador del cliente |
| `Country` | País |

El archivo original no se incluye en el repositorio. Para reproducir el análisis debe descargarse desde su fuente oficial, respetando las condiciones de atribución.

---

## 4. Exploración y limpieza de datos

Antes de construir los modelos predictivos se realizó un análisis exploratorio para identificar problemas de calidad y definir qué operaciones debían incluirse en el cálculo de ingresos comerciales.

### 4.1. Registros duplicados

Durante el análisis se identificaron **5.268 registros duplicados exactos** en el dataset original.

Se decidió no eliminarlos automáticamente porque no existía información suficiente para garantizar que todos representaran errores de registro.

Algunos registros aparentemente idénticos podrían corresponder a operaciones comerciales legítimas.

Esta decisión presenta una limitación: si determinados duplicados fueran errores reales, podrían estar afectando al cálculo de ingresos.

Para resolver completamente esta incertidumbre sería necesario disponer de información adicional sobre las operaciones originales.

### 4.2. Cancelaciones y devoluciones

Se identificaron operaciones con cantidades negativas y facturas de cancelación.

Se decidió conservar su efecto económico cuando cumplían los criterios comerciales establecidos.

El objetivo era evitar que las devoluciones y cancelaciones se interpretaran como ventas positivas o desaparecieran del cálculo de ingresos.

De esta manera, una devolución puede reducir el ingreso comercial agregado de la jornada correspondiente.

### 4.3. Precios no positivos

Se excluyeron del cálculo comercial las operaciones con precios iguales o inferiores a cero.

Estos registros no cumplían los criterios definidos para construir la variable de ingresos comerciales.

### 4.4. Operaciones administrativas

Se identificaron conceptos que no correspondían directamente a las operaciones comerciales que se pretendían analizar.

Se excluyeron los siguientes códigos:

- `AMAZONFEE`
- `BANK CHARGES`
- `CRUK`

Por otra parte, se mantuvieron los descuentos identificados mediante el código `D`, de acuerdo con las reglas comerciales establecidas.

### 4.5. Cálculo de los ingresos comerciales

Para cada registro incluido se calculó:

**Ingreso comercial = Cantidad × Precio unitario**

Posteriormente, los importes se agregaron por fecha para obtener una serie temporal.

### Resultado

| Indicador | Resultado |
|---|---:|
| Registros utilizados en el cálculo comercial | 539.305 |
| Ingresos comerciales agregados | 10.006.501,62 £ |
| Jornadas registradas | 305 |

Los ingresos obtenidos responden a las reglas de tratamiento definidas en el proyecto y no deben interpretarse necesariamente como una cifra contable oficial de la empresa.

---

## 5. Construcción de la serie temporal

Una vez calculados los ingresos comerciales, las operaciones se agruparon por jornada.

El conjunto de datos abarca un periodo de 374 días naturales, pero contiene registros comerciales correspondientes a 305 fechas.

Se decidió no convertir automáticamente los días sin registros en jornadas con ingresos cero.

La ausencia de registros no permite determinar por sí sola si un establecimiento permaneció cerrado, no registró ventas o presenta información ausente.

Por este motivo, el experimento trabaja con **jornadas registradas**, que no necesariamente coinciden con días naturales consecutivos.

### Tratamiento de la última jornada

La última fecha disponible es el **9 de diciembre de 2011**.

Durante el análisis se identificaron circunstancias que aconsejaban tratarla con precaución, incluida su posible incompletitud y la presencia de una operación excepcional.

Se decidió excluir esta jornada del modelado predictivo.

Por tanto:

| Etapa | Jornadas |
|---|---:|
| Serie comercial histórica | 305 |
| Serie tras excluir la última jornada | 304 |
| Serie final con variables predictoras disponibles | 297 |

Las siete primeras observaciones no disponen de todo el histórico necesario para construir determinadas variables predictoras.

---

## 6. Ingeniería de variables

Se construyeron seis variables predictoras:

| Variable | Descripción |
|---|---|
| `DiaSemana` | Día de la semana correspondiente a la jornada |
| `Mes` | Mes de la jornada |
| `IngresoAnterior1` | Ingresos de la jornada registrada anterior |
| `IngresoAnterior2` | Ingresos de dos jornadas registradas atrás |
| `IngresoAnterior7` | Ingresos de siete jornadas registradas atrás |
| `MediaAnterior7` | Media de los ingresos de las siete jornadas registradas anteriores |

La variable objetivo es el ingreso comercial de la jornada que se desea estimar.

### Prevención de fuga de información

Para construir las variables históricas se utilizaron desplazamientos temporales mediante `shift()`.

La media de siete jornadas se calculó utilizando:

```python
df["MediaAnterior7"] = (
    df["IngresoComercial"]
    .shift(1)
    .rolling(window=7)
    .mean()
)
```

El desplazamiento previo impide que el cálculo utilice el ingreso real de la misma jornada que se desea predecir.

Este procedimiento busca evitar la fuga de información (*data leakage*).

Las variables históricas se construyen utilizando observaciones anteriores dentro de la serie cronológicamente ordenada.

---

## 7. División de entrenamiento, validación y prueba

Se dividieron las 297 observaciones disponibles respetando el orden cronológico.

| Conjunto | Observaciones | Periodo |
|---|---:|---|
| Entrenamiento | 207 | 09/12/2010 – 24/08/2011 |
| Validación | 45 | 25/08/2011 – 17/10/2011 |
| Prueba final | 45 | 18/10/2011 – 08/12/2011 |

### Entrenamiento

Se utilizó para que los algoritmos aprendieran relaciones entre las variables predictoras y los ingresos comerciales.

### Validación

Se utilizó para comparar los métodos y examinar su rendimiento sobre observaciones posteriores al entrenamiento.

### Prueba final

Se utilizó para comprobar cómo se comportaban los métodos seleccionados en el último periodo disponible.

La división cronológica resulta importante porque el objetivo del proyecto es realizar predicciones utilizando información histórica anterior a la jornada estimada.

### Consideración metodológica

Inicialmente, Random Forest obtuvo el mejor resultado durante la validación.

Sin embargo, durante TEST la media de siete jornadas obtuvo un menor error y posteriormente se eligió como referencia operativa para generar la previsión adicional.

**Esta elección final estuvo influida por los resultados de TEST.**

Por tanto, aunque las métricas obtenidas describen correctamente el rendimiento observado, el periodo TEST no puede considerarse una evaluación completamente independiente de la selección final de la media de siete jornadas.

Esta circunstancia se reconoce como una limitación metodológica del experimento.

Una mejora consistiría en realizar validación temporal mediante varias ventanas y, posteriormente, evaluar el método seleccionado en un periodo nuevo e independiente.

---

## 8. Métodos predictivos comparados

Se evaluaron cinco enfoques.

### 8.1. Última jornada registrada

Utiliza el ingreso comercial de la jornada anterior como estimación de la siguiente.

Sirve como referencia básica de comparación.

### 8.2. Media de siete jornadas

Calcula el promedio de los ingresos comerciales de las siete jornadas registradas anteriores.

Es un método sencillo que permite suavizar las variaciones entre jornadas.

### 8.3. Regresión lineal

Modelo supervisado que estima los ingresos mediante una combinación lineal de las variables predictoras.

### 8.4. Random Forest Regressor

Algoritmo supervisado que combina múltiples árboles de decisión.

Se utilizó una configuración con:

- 100 árboles.
- Profundidad máxima de 5.
- Mínimo de 3 observaciones por hoja.
- Semilla aleatoria de 42.

### 8.5. Red neuronal MLP Regressor

Se utilizó una red neuronal multicapa con dos capas ocultas de 16 y 8 neuronas.

Las variables predictoras se escalaron previamente para facilitar su utilización por la red neuronal.

Durante el entrenamiento se produjo una advertencia de convergencia al alcanzar el límite configurado de 2.000 iteraciones.

Por esta razón, el resultado de MLP debe interpretarse como un experimento comparativo y no como una configuración completamente optimizada.

---

## 9. Métricas de evaluación

Se utilizaron dos métricas principales.

### MAE — Error absoluto medio

El MAE mide la diferencia absoluta media entre los ingresos reales y los ingresos estimados.

Permite interpretar cuánto se equivoca, de media, un método predictivo.

### RMSE — Raíz del error cuadrático medio

El RMSE también evalúa las diferencias entre ingresos reales y estimados.

Sin embargo, penaliza más intensamente los errores grandes al elevarlos al cuadrado antes de calcular el promedio y su raíz.

En ambas métricas, un valor menor indica un mejor resultado para el conjunto evaluado.

---

## 10. Resultados de validación

Se obtuvieron los siguientes resultados:

| Método | MAE (£) | RMSE (£) |
|---|---:|---:|
| Última jornada | 19.191,79 | 25.526,91 |
| Media de siete jornadas | 13.807,43 | 18.918,57 |
| Regresión lineal | 13.011,27 | 18.102,27 |
| **Random Forest** | **11.622,47** | **17.537,00** |
| MLP | 16.924,73 | 23.965,48 |

**Random Forest obtuvo el mejor rendimiento durante la validación**, tanto en MAE como en RMSE.

Por este motivo, se consideró inicialmente el método más prometedor.

---

## 11. Explicabilidad de Random Forest

Se utilizaron diferentes técnicas para analizar el comportamiento del modelo.

### Importancia interna de variables

Se examinó la importancia asignada por Random Forest durante la construcción de los árboles.

La variable `DiaSemana` presentó la mayor importancia interna, con aproximadamente un **44,23 %**.

Este porcentaje no representa la proporción de ingresos causada por esa variable.

### Importancia por permutación

Se desordenaron los valores de cada variable para observar cómo cambiaba el error predictivo.

En el caso de `DiaSemana`, la alteración de sus valores incrementó el MAE de validación en aproximadamente 3.874 £ de media.

### SHAP

Se utilizó SHAP para interpretar predicciones individuales.

Esta técnica permite analizar cómo contribuye cada variable a una predicción respecto a un valor de referencia.

En una de las observaciones estudiadas, `DiaSemana` presentó una contribución positiva de aproximadamente 5.087 £.

Estos resultados describen el comportamiento predictivo del modelo y **no demuestran relaciones causales**.

---

## 12. Evaluación final sobre TEST

Se compararon la media de siete jornadas y Random Forest sobre las 45 observaciones de prueba.

| Método | MAE (£) | RMSE (£) |
|---|---:|---:|
| **Media de siete jornadas** | **14.015,88** | **18.744,80** |
| Random Forest | 18.924,47 | 23.767,55 |

### Interpretación

Aunque Random Forest había obtenido mejores resultados durante la validación, la media de siete jornadas consiguió un menor error en TEST.

El MAE de Random Forest fue aproximadamente un **35,02 % superior** al de la media de siete jornadas durante el periodo final.

Este resultado demuestra que un método de mayor complejidad no garantiza necesariamente mejores resultados predictivos.

No obstante, las conclusiones se limitan a los periodos evaluados.

No puede afirmarse que la media de siete jornadas vaya a superar siempre a Random Forest en otras empresas o periodos comerciales.

---

## 13. Análisis de errores empresariales

Además de MAE y RMSE, se analizaron los errores de infraestimación y sobreestimación.

### Infraestimación

Ocurre cuando los ingresos reales son superiores a los estimados.

### Sobreestimación

Ocurre cuando los ingresos estimados son superiores a los ingresos reales.

### Resultados

| Indicador | Media de 7 jornadas | Random Forest |
|---|---:|---:|
| Jornadas infraestimadas | 23 de 45 | 41 de 45 |
| Porcentaje de jornadas infraestimadas | 51,11 % | 91,11 % |
| Infraestimación acumulada | 378.730,16 £ | 839.841,56 £ |
| Sobreestimación acumulada | 251.984,62 £ | 11.759,59 £ |

Random Forest mostró una tendencia notable a infraestimar los ingresos durante TEST.

La media de siete jornadas presentó un comportamiento más equilibrado.

### Interpretación empresarial

Ambos tipos de error podrían tener consecuencias operativas distintas.

Por ejemplo, una infraestimación podría contribuir a una planificación insuficiente de personal o existencias.

Una sobreestimación podría provocar una asignación excesiva de recursos.

Sin embargo, **las desviaciones acumuladas no representan pérdidas económicas reales**.

Son diferencias entre ingresos observados y estimados.

Para cuantificar sus consecuencias económicas sería necesario disponer de información sobre las decisiones empresariales y sus costes efectivos.

---

## 14. Simulación de costes empresariales

Se planteó una simulación hipotética para estudiar cómo podrían compararse los métodos si los errores por exceso y por defecto tuvieran costes diferentes.

Se establecieron dos penalizaciones ilustrativas:

| Tipo de error | Penalización hipotética |
|---|---:|
| Infraestimación | 20 % |
| Sobreestimación | 5 % |

Los porcentajes no proceden de datos contables reales.

Representan un escenario en el que infraestimar ingresos se considera potencialmente más perjudicial que sobreestimarlos.

### Resultados de la simulación

| Método | Coste por infraestimación | Coste por sobreestimación | Coste total |
|---|---:|---:|---:|
| **Media de 7 jornadas** | 75.746,03 £ | 12.599,23 £ | **88.345,26 £** |
| Random Forest | 167.968,31 £ | 587,98 £ | 168.556,29 £ |

Bajo estos supuestos, la media de siete jornadas presentó un coste simulado **80.211,03 £ inferior** al de Random Forest.

### Limitaciones

Los resultados dependen directamente de las penalizaciones elegidas.

Modificar esos porcentajes podría cambiar qué método resulta más conveniente.

Por tanto, se propone como mejora realizar un **análisis de sensibilidad**, comparando distintas combinaciones de costes.

Para una aplicación empresarial real también sería necesario utilizar información sobre márgenes, personal, inventario y costes operativos.

---

## 15. Análisis de jornadas excepcionales

Se investigaron las jornadas de TEST en las que los modelos cometieron errores especialmente elevados.

Por ejemplo, el 14 de noviembre de 2011 se registraron:

| Indicador | Importe |
|---|---:|
| Ingreso comercial real | 113.179,86 £ |
| Predicción Media7 | 57.736,21 £ |
| Predicción Random Forest | 40.300,55 £ |

Ambos métodos infraestimaron considerablemente los ingresos.

La media de siete jornadas se aproximó más al resultado real.

Una posible explicación es la existencia de patrones comerciales extraordinarios que no fueron capturados suficientemente por los métodos predictivos.

Sin embargo, no se dispone de evidencia para atribuir ese comportamiento a una causa específica.

### Posibles mejoras

- Investigar las operaciones correspondientes a las jornadas de mayores errores.
- Incorporar variables relacionadas con promociones o campañas, si estuvieran disponibles.
- Estudiar la influencia de festivos y estacionalidad.
- Ampliar el histórico disponible.
- Evaluar el rendimiento específicamente en jornadas excepcionales.

---

## 16. Previsión de la siguiente jornada registrada

Una vez comparados los métodos, se realizó una previsión adicional utilizando la media de siete jornadas.

Se seleccionaron las últimas siete jornadas registradas del histórico utilizado para modelar.

| Fecha | Ingresos comerciales (£) |
|---|---:|
| 01/12/2011 | 51.410,95 |
| 02/12/2011 | 57.086,06 |
| 04/12/2011 | 24.565,78 |
| 05/12/2011 | 88.274,72 |
| 06/12/2011 | 54.228,37 |
| 07/12/2011 | 75.061,22 |
| 08/12/2011 | 81.417,78 |

### Resultado final

**Ingreso comercial estimado: 61.720,70 £**

La previsión corresponde a la siguiente jornada registrada después del último día considerado para el modelado.

No se asigna una fecha concreta porque el método utiliza jornadas con registros y no determina cuándo ocurrirá la próxima jornada comercial.

Esta previsión es un ejercicio histórico y no constituye una predicción de ingresos actuales.

Además, la elección de la media de siete jornadas para esta previsión estuvo influida por los resultados de TEST, circunstancia reconocida entre las limitaciones metodológicas.

---

## 17. Dashboard de Tableau

Se desarrolló un dashboard para comunicar los resultados del proyecto.

Incluye:

- Evolución histórica de los ingresos comerciales.
- Comparación entre ingresos reales y predicciones.
- Indicador MAE de la media de siete jornadas.
- Indicador MAE de Random Forest.

El archivo del dashboard se encuentra en:

`dashboard/TFM_Prediccion_Ingresos.twbx`

Los archivos CSV empleados para la visualización se exportaron utilizando:

- Separador de columnas: `;`
- Separador decimal: `,`
- Codificación: `utf-8-sig`

Estos ajustes facilitaron el tratamiento de los datos con la configuración regional española.

---

## 18. Estructura del repositorio

```text
TFM-Prediccion-Ingresos/
│
├── notebooks/
│   ├── 01_analisis_exploratorio_completo.ipynb
│   └── 01_analisis_exploratorio_original.ipynb
│
├── dashboard/
│   └── TFM_Prediccion_Ingresos.twbx
│
├── resultados/
│   ├── ingresos_tableau.csv
│   ├── predicciones_tableau.csv
│   └── prediccion_siguiente_jornada.csv
│
├── documentacion/
│   └── Bitacora_Proyecto1_DEFINITIVA_22oct2026.pdf
│
├── README.md
└── requirements.txt
```

**Notebook principal:** `notebooks/01_analisis_exploratorio_completo.ipynb`

La estructura descrita corresponde a la documentación del proyecto. Para reproducirlo, debe comprobarse que los archivos estén disponibles en las rutas indicadas.

---

## 19. Cómo reproducir el proyecto

### Requisitos

- Python.
- Dependencias especificadas en `requirements.txt`.
- Google Colab o un entorno compatible con Jupyter.
- Dataset original `Online Retail.xlsx`.

### Procedimiento

1. Descargar o clonar el repositorio.
2. Obtener `Online Retail.xlsx` desde UCI Machine Learning Repository.
3. Preparar el entorno e instalar las dependencias necesarias.
4. Abrir el notebook principal:

   `notebooks/01_analisis_exploratorio_completo.ipynb`

5. Cargar el dataset original y comprobar que la ruta de lectura coincide con su ubicación.
6. Ejecutar las celdas en orden.
7. Revisar las métricas de validación y TEST.
8. Comprobar los archivos CSV generados.
9. Abrir el dashboard de Tableau para consultar las visualizaciones.

### Consideraciones

El notebook utiliza algunas funcionalidades específicas de Google Colab, especialmente para la carga y descarga de archivos.

Si se ejecuta en otro entorno, esas instrucciones pueden requerir adaptación.

Los resultados de referencia se encuentran documentados en este README y en los archivos del proyecto.

---

## 20. Arquitectura del proyecto

```text
Online Retail.xlsx
       |
       v
Exploración y auditoría del dataset
       |
       v
Limpieza y clasificación de operaciones
       |
       v
Cálculo de ingresos comerciales
       |
       v
Agregación por jornada registrada
       |
       v
Ingeniería de variables temporales
       |
       v
División cronológica de datos
       |
       v
Entrenamiento y validación de métodos
       |
       v
Evaluación final en TEST
       |
       v
Análisis de errores e interpretabilidad
       |
       v
Simulación de costes empresariales
       |
       v
Previsión de la siguiente jornada
       |
       v
Exportación de resultados
       |
       v
Dashboard de Tableau
```

La arquitectura corresponde a un prototipo analítico académico.

No se ha implementado una plataforma de predicción en producción.

---

## 21. Principales dificultades encontradas

Durante el desarrollo surgieron diferentes problemas técnicos y metodológicos.

### Tratamiento de cancelaciones

Fue necesario diferenciar ventas, cancelaciones, devoluciones y operaciones administrativas para construir una variable de ingresos comerciales coherente.

### Registros duplicados

Se detectaron duplicados cuya naturaleza no pudo verificarse completamente.

Se decidió no eliminarlos de manera automática, documentando la incertidumbre.

### Jornada final incompleta

Se excluyó del modelado el 9 de diciembre de 2011 por su posible incompletitud y características excepcionales.

### Jornadas sin registros

Se decidió no asignar automáticamente ingresos cero a fechas sin transacciones registradas.

Esta decisión condiciona el tipo de previsión que puede realizarse.

### Diferencias entre validación y TEST

Random Forest obtuvo mejores resultados en validación, pero la media de siete jornadas funcionó mejor en TEST.

Se identificó la necesidad de mejorar la evaluación temporal y reconocer el posible sesgo de selección del método final.

### Entrenamiento de MLP

La red neuronal alcanzó el límite de iteraciones sin converger completamente.

Se documentó esta advertencia y se conservaron sus resultados como experimento comparativo.

### Importación de datos en Tableau

Se adaptó el formato de los CSV a la configuración regional española para facilitar la interpretación de números decimales.

También se revisaron los indicadores MAE para calcularlos mediante el promedio de los errores absolutos.

---

## 22. Limitaciones del proyecto

El trabajo presenta varias limitaciones que deben considerarse antes de cualquier aplicación empresarial.

### Antigüedad de los datos

El dataset corresponde a 2010–2011 y no representa necesariamente los patrones comerciales actuales.

### Periodo histórico limitado

Se dispone de un periodo relativamente reducido para estudiar comportamientos comerciales y variaciones estacionales.

### Jornadas sin registros

No siempre es posible distinguir entre cierres, ausencia de ventas y registros faltantes.

### Incertidumbre sobre algunos duplicados

No se pudo verificar de manera definitiva la naturaleza de todos los registros duplicados.

### Selección del método después de TEST

La media de siete jornadas se eligió como referencia final tras observar su mejor resultado en TEST, introduciendo una limitación de independencia en la evaluación del método finalmente seleccionado.

### Errores en jornadas excepcionales

Los métodos presentaron errores relevantes en determinadas jornadas con ingresos elevados.

### Variables disponibles

El modelo no dispone de información específica sobre promociones, campañas comerciales, inventario u otros posibles factores explicativos.

### Alcance de la predicción

El experimento realiza predicciones de una jornada utilizando información histórica observada.

No se ha desarrollado ni evaluado un sistema de predicción de múltiples jornadas futuras sin disponer de los ingresos intermedios.

### Simulación económica hipotética

Las penalizaciones utilizadas no representan costes empresariales observados.

---

## 23. Propuestas de mejora

Como continuación del proyecto se plantean las siguientes mejoras:

1. Incorporar datos comerciales más recientes.
2. Ampliar el histórico temporal.
3. Construir un calendario comercial completo.
4. Diferenciar días de cierre, días sin ventas y fechas con datos ausentes.
5. Aplicar validación temporal mediante varias ventanas.
6. Evaluar el método seleccionado sobre un nuevo periodo independiente.
7. Investigar los errores de las jornadas excepcionales.
8. Incorporar nuevas variables disponibles antes de cada predicción.
9. Realizar un análisis de sensibilidad de los costes simulados.
10. Estudiar el rendimiento de métodos adicionales únicamente bajo un protocolo de evaluación adecuado.

Estas mejoras buscan aumentar la fiabilidad, la interpretabilidad y la utilidad empresarial del sistema.

---

## 24. Posible aplicación empresarial

El proyecto puede servir como base para desarrollar un sistema de apoyo a la planificación comercial.

En una empresa real, un funcionamiento posible sería:

1. Recibir los datos comerciales al finalizar cada jornada.
2. Aplicar las reglas de limpieza establecidas.
3. Actualizar la serie histórica de ingresos.
4. Calcular las variables utilizando únicamente información disponible.
5. Generar una nueva estimación.
6. Almacenar la predicción.
7. Incorporar el resultado al dashboard.
8. Comparar posteriormente la predicción con el ingreso real.

### Monitorización

Se propone realizar un seguimiento periódico de:

- MAE.
- RMSE.
- Frecuencia de infraestimaciones.
- Frecuencia de sobreestimaciones.
- Errores en jornadas excepcionales.
- Cambios en los patrones comerciales.
- Calidad y disponibilidad de los datos.

### Actualización de modelos

En caso de utilizar modelos entrenados, como Random Forest, podrían compararse periódicamente nuevas configuraciones utilizando datos temporales apropiados.

La media de siete jornadas no requiere entrenamiento periódico: necesita actualizar los ingresos históricos disponibles.

### Alcance actual

**El despliegue es conceptual.**

No se han implementado automatizaciones productivas, una API comercial, monitorización continua ni reentrenamiento automático.

Estas posibilidades constituyen líneas de desarrollo futuro.

---

## 25. Estado del proyecto

| Componente | Estado documentado |
|---|---|
| Análisis exploratorio | Completado |
| Limpieza y preparación | Completado |
| Construcción de la serie temporal | Completado |
| Ingeniería de variables | Completado |
| Entrenamiento y validación | Completado |
| Evaluación de TEST | Completado |
| Explicabilidad | Completado |
| Análisis empresarial de errores | Completado |
| Simulación de costes | Completado |
| Previsión adicional | Completado |
| Dashboard de Tableau | Desarrollado |
| Documentación y reproducibilidad | Documentadas; sujetas a verificación final de archivos y ejecución |
| Despliegue empresarial automatizado | No implementado |

---

## 26. Conclusiones

El proyecto permitió desarrollar un proceso completo de análisis predictivo aplicado a ingresos comerciales.

Durante la validación, Random Forest obtuvo el menor error absoluto medio, pero en la prueba final la media de siete jornadas presentó mejores resultados.

Esto demuestra que **un algoritmo de mayor complejidad no garantiza necesariamente una mayor precisión predictiva**.

También se observó que Random Forest tendía a infraestimar los ingresos durante el periodo final y que ambos métodos cometían errores importantes en determinadas jornadas excepcionales.

Desde una perspectiva empresarial, el análisis permitió estudiar cómo diferentes errores predictivos podrían influir en la planificación de recursos.

Sin embargo, los costes calculados fueron hipotéticos y no deben interpretarse como pérdidas económicas reales.

La media de siete jornadas se seleccionó como referencia operativa provisional para el experimento, reconociendo que dicha elección estuvo influida por los resultados de TEST.

**La principal conclusión es que la utilidad de un sistema predictivo depende no solamente de la complejidad del algoritmo, sino también de la calidad de los datos, la metodología de evaluación, la interpretación de los errores y las necesidades reales de la empresa.**

Antes de su utilización empresarial sería necesario incorporar información actualizada, reforzar la validación temporal y comprobar su rendimiento sobre nuevos datos independientes.

---

## 27. Referencias

**Dataset**

Chen, D. (2015). *Online Retail*. UCI Machine Learning Repository.

https://doi.org/10.24432/C5BW33

**Herramientas y documentación técnica**

- Python: https://www.python.org/
- Pandas: https://pandas.pydata.org/
- NumPy: https://numpy.org/
- Scikit-learn: https://scikit-learn.org/
- SHAP: https://shap.readthedocs.io/
- Matplotlib: https://matplotlib.org/
- Google Colab: https://colab.research.google.com/
- Tableau: https://www.tableau.com/

---

**Proyecto académico — Trabajo Fin de Máster de Data Science con Inteligencia Artificial — The Big School.**
