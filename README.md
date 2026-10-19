# Predicción de ingresos comerciales con Machine Learning

**Proyecto 1 del TFM de Data Science con IA — The Big School.**

## Objetivo
Estimar el importe comercial de la siguiente jornada **con registros de transacciones** usando un histórico de venta minorista. El resultado sirve como apoyo exploratorio para planificar recursos; no es una previsión contable garantizada.

## Datos
Fuente: `Online Retail.xlsx` (Online Retail). Datos transaccionales en libras esterlinas. El fichero original tiene **541.909 filas**. Antes del modelado se distingue entre ventas, cancelaciones, descuentos y conceptos administrativos. El dataset original **no se incluye en este paquete**: es necesario disponer de él junto al notebook y revisar su licencia y fuente de distribución antes de publicarlo.

## Metodología
1. Auditoría de faltantes, duplicados, cancelaciones y anomalías.
2. Cálculo del importe comercial diario con cantidades firmadas; inclusión de descuentos `D` y exclusión de `AMAZONFEE`, `BANK CHARGES` y `CRUK`.
3. Exclusión de la jornada final (09/12/2011) del modelado por posible incompletitud.
4. Variables predictoras: día de semana, mes, ingresos de observaciones anteriores 1, 2 y 7 y promedio de las siete anteriores. **Jornadas registradas no equivale a días naturales consecutivos.**
5. División temporal en entrenamiento (207), validación (45) y prueba (45).
6. Comparación de última jornada, media de siete jornadas, regresión lineal, Random Forest y MLP. Interpretación global y local del Random Forest mediante permutación y SHAP.

## Resultados registrados
| Modelo evaluado en prueba | MAE (£) | RMSE (£) |
|---|---:|---:|
| Media de siete jornadas | 14.015,88 | 18.744,80 |
| Random Forest | 18.924,47 | 23.767,55 |

Aunque Random Forest destacó en validación, la media de siete jornadas mostró menor error en la prueba final. **Estas cifras son resultados del trabajo previo y están pendientes de una reproducción integral en el entorno definitivo.** No se reajustan modelos en función de los datos de prueba.

## Ejecución
1. Instalar Python y las dependencias de `requirements.txt`.
2. Colocar `Online Retail.xlsx` en el directorio de ejecución del notebook (o ajustar la ruta de lectura).
3. Abrir `notebooks/01_analisis_exploratorio_documentado.ipynb` en Google Colab o Jupyter y ejecutar las celdas ordenadamente.
4. Los pasos que importan `from google.colab import files` son exclusivos de Colab; fuera de este entorno omitir las celdas de descarga (los CSV se generan de todos modos).
5. Los CSV para Tableau usan `sep=";"`, `decimal=","`, `encoding="utf-8-sig"`.

## Archivos
- `notebooks/`: original sin modificar y edición con explicaciones y salidas limpiadas.
- `dashboard/`: colocar aquí `TFM_Prediccion_Ingresos.twbx`.
- `datos/`, `resultados/`, `graficos/`: completar con los archivos verificables generados por el notebook.

## Limitaciones
No se imputan días sin actividad observada como cero; existe incertidumbre sobre duplicados, alcance comercial de algunos conceptos y estabilidad del patrón de demanda. El objetivo es predicción para una sola jornada con registro y antecedentes observados; no se evalúa previsión multi-paso. Los importes de error no deben interpretarse como pérdidas financieras.

## Estado de entrega
**Borrador estructurado:** pendiente de ejecución integral, comprobación de las salidas finales, incorporación de visualizaciones y revisión de las condiciones oficiales del TFM antes de publicar el repositorio.
