# Análisis de clientes de NovaRetail+

## Objetivo

Identificar y evaluar qué variables del comportamiento de los clientes y de la plataforma de comercio electrónico están más asociadas con el ingreso anual que cada cliente genera a la empresa (`ingreso_anual`), como base descriptiva para orientar decisiones comerciales y de fidelización.

El análisis es **exploratorio y descriptivo**: mide asociaciones entre variables y no establece relaciones de causa y efecto.

## Dataset

El dataset contiene **15,000 registros** (un cliente por fila) y **12 columnas**:

| Columna | Tipo | Descripción |
|---|---|---|
| `id_cliente` | string | Identificador único del cliente |
| `edad` | numérica | Edad del cliente |
| `nivel_ingreso` | numérica | Ingreso anual estimado del cliente |
| `visitas_mes` | numérica | Visitas a la app/web en el mes |
| `compras_mes` | numérica | Compras realizadas en el mes |
| `gasto_publicidad_dirigida` | numérica | Gasto en anuncios asignado al usuario |
| `satisfaccion` | numérica (1–5) | Calificación de satisfacción |
| `miembro_premium` | binaria (0/1) | Suscripción mensual premium |
| `abandono` | binaria (0/1) | Si el cliente abandonó la plataforma |
| `tipo_dispositivo` | categórica | móvil / escritorio / tablet |
| `region` | categórica | norte / sur / este / oeste |
| `ingreso_anual` | numérica | Ingreso anual que el cliente genera a la empresa |

> `nivel_ingreso` (ingreso estimado del cliente) e `ingreso_anual` (ingreso para la empresa) son variables distintas.

## Etapas del análisis

1. **Carga y exploración inicial:** importación de librerías, revisión de la estructura del dataset y de los tipos de datos.
2. **Estadísticas descriptivas:** `describe()` de las variables numéricas, `unique()` de las binarias y `value_counts()` de las categóricas.
3. **Correlación entre variables numéricas:** heatmap de correlación (Pearson) y comparación Pearson vs. Spearman para las variables con asociación destacada.
4. **Visualización de relaciones:** scatterplots de los pares con correlación moderada o fuerte (|r| ≥ 0.30).
5. **Variables binarias y categóricas:** correlación point-biserial (`miembro_premium` y `abandono` frente a las variables numéricas) y V de Cramér (`tipo_dispositivo` vs. `region`).
6. **Interpretación para el negocio:** hallazgos con evidencia visual y numérica, lo que no se puede afirmar y su implicación.

### Criterio de interpretación de la correlación

| Magnitud (valor absoluto) | Clasificación |
|---|---|
| < 0.30 | Débil |
| 0.30 – 0.50 | Moderada |
| > 0.50 | Fuerte |

## Hallazgos principales

1. **Compras mensuales e ingreso anual (0.967 en Pearson y Spearman):** es la asociación más alta del análisis. En el scatterplot, los clientes con 0 compras aparecen con `ingreso_anual` = 0. Por su redundancia, habrá que decidir cuál variable conservar si se usan en un modelo.
2. **Clientes sin compras en el mes:** el percentil 25 de `compras_mes` es 0, es decir, al menos una cuarta parte de los clientes no registra compras en el mes, aunque todos tienen al menos una visita.
3. **Visitas, gasto publicitario, compras e ingreso:**
   * `visitas_mes` y `gasto_publicidad_dirigida`: asociación fuerte pero dispersa (0.58 Pearson / 0.56 Spearman).
   * `visitas_mes` con `compras_mes` (0.35 / 0.33) y con `ingreso_anual` (0.34 / 0.32): asociación moderada.
   * `gasto_publicidad_dirigida` con `compras_mes` (0.21 / 0.19) e `ingreso_anual` (0.20 / 0.18): asociación débil.
4. **Abandono:** la asociación más alta es con `miembro_premium` (-0.12), y es débil. Con las demás variables numéricas el valor absoluto no supera 0.024.
5. **Dispositivo y región:** `móvil` es el dispositivo más frecuente (65.5%), seguido de `escritorio` (24.8%) y `tablet` (9.7%). La V de Cramér entre `tipo_dispositivo` y `region` es ≈ 0.012 (asociación prácticamente nula).
6. **Variables sin asociación lineal relevante:** `edad`, `nivel_ingreso` y `satisfaccion` tienen correlaciones cercanas a 0 (valor absoluto menor a 0.06) con el resto de variables.

### Calidad de los datos

Las variables numéricas, binarias y categóricas revisadas tienen 15,000 valores, sin nulos. Las binarias contienen únicamente los valores 0 y 1, y las categóricas no presentan categorías inesperadas. `ingreso_anual` muestra asimetría hacia la derecha (media ≈ 36.6, mediana ≈ 30.7, máximo ≈ 244.7).

## Limitaciones

* **Correlación no implica causalidad.** Todos los resultados son asociaciones.
* **Pearson mide solo asociación lineal.** Los valores cercanos a 0 no descartan relaciones no lineales ni efectos que aparezcan solo en ciertos segmentos.
* **Redundancia entre `compras_mes` e `ingreso_anual`.** No se conoce cómo se construyó `ingreso_anual`; usar ambas variables en un modelo puede generar multicolinealidad.
* **`compras_mes` refleja un solo mes.** Los clientes sin compras podrían haber comprado en otros periodos.
* **No se segmentó la información.** No se compararon compras, ingreso, visitas ni abandono por dispositivo, región o membresía premium.
* **Grupos pequeños.** `tablet` tiene 1,462 registros, por lo que sus resultados podrían ser menos estables al segmentar.
* **Unidades desconocidas** de `ingreso_anual`, `nivel_ingreso` y `gasto_publicidad_dirigida`.
* **Lectura visual de gráficos.** Algunos detalles de los scatterplots se describieron por inspección visual y no con cálculos exactos.

## Próximos pasos

1. **Segmentación adicional:** comparar `compras_mes`, `ingreso_anual` y `abandono` por dispositivo y región, y calcular la tasa de abandono por grupo (premium, dispositivo, región), revisando el tamaño de cada grupo.
2. **Segmento con visitas y sin compras:** dimensionar la proporción exacta de clientes con `compras_mes` = 0 y compararlos con quienes sí compran.
3. **Uso en modelos:** definir qué variable conservar entre `compras_mes` e `ingreso_anual` y explorar relaciones no lineales.
4. **Análisis causal (opcional):** si el negocio necesita saber si una acción cambia un resultado, se requeriría un experimento controlado (por ejemplo, una prueba A/B).

## Cómo ejecutar el notebook

El notebook lee los datos desde la ruta `/datasets/`, que existe en el entorno preconfigurado de TripleTen. Fuera de ese entorno hay que ajustar la ruta de carga en la sección inicial.

### Google Colab

1. Sube el notebook y los datos a tu Google Drive.
2. Ejecuta la celda de montaje de Drive (`drive.mount('/content/drive')`).
3. Modifica la ruta del archivo CSV si tu estructura de carpetas es distinta.

### Guía de reproducción

1. Instala las librerías necesarias: `pandas`, `numpy`, `matplotlib`, `seaborn` y `scipy`.
2. Ejecuta las celdas en orden, desde la carga del dataset hasta la sección de interpretación, para que las variables queden bien tipificadas antes del análisis estadístico.

## Tecnologías

* **Python** como lenguaje principal.
* **Pandas y NumPy** para manipulación y análisis de datos.
* **Matplotlib y Seaborn** para visualizaciones (heatmap y scatterplots).
* **SciPy** para point-biserial y chi-cuadrado (V de Cramér).
* **Jupyter Notebook / Google Colab** como entorno de ejecución.
