# TP1 - Ciencia de Datos: Mantenimiento Predictivo (Industria 4.0)

Trabajo Práctico 1 de **Ciencia de Datos** — Universidad Tecnológica Nacional, Facultad Regional Santa Fe (Ingeniería en Sistemas de Información, 2026).

El proyecto analiza un conjunto de datos sintéticos de **mantenimiento predictivo** que simula el monitoreo de máquinas herramienta en una línea de producción, y entrena modelos de clasificación para **predecir la ocurrencia de fallas** en los equipos.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/bautylazza/TP1-CDD/blob/main/TP_1_CD.ipynb)

## Autores

- Bautista Lazzarini
- Mario Poccia
- Santiago Nicle
- Franco Mazzuchini

**Docentes:** Mariel Ale y Mauro Pacchiotti.

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `TP_1_CD.ipynb` | Notebook con la solución completa de las dos partes del TP. |
| `README.md` | Este documento. |

> El dataset (`i40 (datos).csv`) no está incluido en el repositorio. Para ejecutar el notebook hay que colocarlo en el mismo directorio (o subirlo al entorno de Colab).

## Dataset

14.521 registros y 9 columnas, cada uno correspondiente a un equipo distinto.

| Variable | Descripción |
|---|---|
| `idx` | Identificador de la fila. |
| `parent_device_id` | Identificador del equipo físico. |
| `product_type` | Calidad del producto procesado: `L` (baja), `M` (media) o `H` (alta). |
| `air_temp [K]` | Temperatura ambiente, en Kelvin. |
| `process_temp [K]` | Temperatura del proceso de fabricación, en Kelvin. |
| `speed [RPM]` | Velocidad de rotación de la herramienta. |
| `torque [Nm]` | Par motor en Newton-metro (relación inversa con la velocidad). |
| `tool_wear [min]` | Desgaste acumulado de la herramienta, en minutos. |
| `target` | Variable objetivo: `normal` o `failure`. |

## Desarrollo

### Parte 1 - Análisis exploratorio

- Estadística descriptiva, medidas de **tendencia central** (media, media recortada, mediana, moda) y de **dispersión** (varianza, desvío estándar, desviación media absoluta, rango, cuartiles, IQR, percentiles).
- **Distribuciones**: histogramas, boxplots y curvas de densidad (KDE).
- **Proporciones**: gráficos de torta y de barras para `product_type` y `target`.
- **Relaciones entre variables**: matriz de correlación, matriz de dispersión (pairplot) y análisis bivariado (por ejemplo, torque vs. velocidad segmentado por falla).

### Parte 2 - Procesamiento y modelado

**Preprocesamiento**

1. Eliminación de `idx` y `parent_device_id` (identificadores sin valor predictivo).
2. Imputación por la media de los 40 valores faltantes de `air_temp [K]`.
3. Corrección de valores erróneos en `speed [RPM]` (valores de -1, físicamente imposibles), reemplazados por la mediana.
4. Codificación: `target` mapeado a 0/1 y `product_type` con One-Hot Encoding (`drop='first'`).
5. Análisis de correlación (Pearson) para detectar multicolinealidad.
6. Los outliers de `speed` y `torque` se conservan, ya que en mantenimiento predictivo son señal de falla; por eso se usa `RobustScaler` para normalizar.
7. Verificación del balance de clases: 51,5 % de fallas y 48,5 % de equipos normales, es decir, un conjunto prácticamente balanceado que no requiere remuestreo.

El dataset procesado se exporta a `mantenimiento_procesado.csv`.

**Entrenamiento y evaluación**

- División entrenamiento/prueba: 75 % / 25 %, estratificada y con `random_state=42`.
- Búsqueda de hiperparámetros con `GridSearchCV`.
- Métricas: accuracy, precision, recall, F1-score y matriz de confusión.

## Resultados

| Modelo | Mejores hiperparámetros | Accuracy | F1 (falla) |
|---|---|---|---|
| Regresión Logística | `C = 0.1` | 0.84 | 0.84 |
| k-NN | `n_neighbors = 3` | 0.95 | 0.95 |
| Gradient Boosting (ensamblado) | `learning_rate = 0.2`, `max_depth = 7`, `n_estimators = 200` | **0.98** | **0.98** |

Los resultados corresponden a 3.631 muestras de prueba. El modelo ensamblado de **Gradient Boosting** obtiene el mejor rendimiento (recall de 0.99 sobre la clase de falla). Los modelos no lineales superan ampliamente a la regresión logística, lo que sugiere relaciones no lineales entre las variables del proceso.

## Tecnologías

Python 3, Jupyter / Google Colab, `pandas`, `numpy`, `scipy`, `statsmodels`, `matplotlib`, `seaborn` y `scikit-learn`.

## Cómo ejecutarlo

1. Abrir el notebook en Google Colab con el botón de arriba, o clonar el repositorio:
   ```bash
   git clone https://github.com/bautylazza/TP1-CDD.git
   cd TP1-CDD
   ```
2. Instalar las dependencias:
   ```bash
   pip install numpy pandas scipy statsmodels matplotlib seaborn scikit-learn jupyter
   ```
3. Copiar `i40 (datos).csv` en el directorio del notebook.
4. Ejecutar `TP_1_CD.ipynb` de principio a fin.
