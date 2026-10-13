# Workshop 3 — Segmentación de clientes con Clustering

**Equipo:** Cristian Cabarcas, Samuel Herrera, Jacobo Pava y Alexander Vargas

Segmentación de los 200 clientes del dataset **Mall Customer Segmentation Data** comparando tres métodos de clustering no supervisado (K-Means, Agglomerative y DBSCAN). Los segmentos resultantes se usan como variable objetivo para entrenar un clasificador que asigna el segmento a clientes nuevos.

**Pregunta de negocio:** ¿en cuántos grupos "naturales" se dividen los clientes del centro comercial y qué caracteriza a cada uno?

## Archivos

| Archivo | Contenido |
|---|---|
| `Workshop3_Clustering_def.ipynb` | Notebook con el código, los resultados y las respuestas a todas las preguntas del workshop |
| `Workshop3_Clustering_def.html` | El mismo notebook ya ejecutado, para leerlo en el navegador sin instalar nada |
| `Mall_Customers.csv` | Dataset (200 clientes, 5 columnas); opcional, el notebook puede descargarlo de Kaggle |
| `Workshop3.md` | Enunciado del workshop |

## Cómo ejecutarlo

1. Instalar las dependencias:

   ```bash
   pip install pandas numpy matplotlib seaborn scipy scikit-learn kagglehub jupyter
   ```

   En Google Colab todas estas librerías ya vienen instaladas.

2. Abrir `Workshop3_Clustering_def.ipynb` en Jupyter o en Google Colab y ejecutar todas las celdas en orden.

**Carga de los datos:** no hace falta Google Drive. Si `Mall_Customers.csv` está en la misma carpeta que el notebook, se usa ese archivo; si no, el notebook lo descarga directamente de [Kaggle](https://www.kaggle.com/datasets/vjchoudhary7/customer-segmentation-tutorial-in-python) con `kagglehub`. La primera celda de carga imprime de dónde se leyó el dataset.

El notebook se ejecuta completo sin errores. Los resultados se verificaron con scikit-learn 1.5 a 1.9, y en todas esas versiones los clusters salen con la misma numeración. En la Fase 7 hay una celda que comprueba que cada número de cluster tenga el perfil descrito en los textos; si imprime una advertencia, hay que reasignar los números en las respuestas.

> **Nota sobre el dataset:** en esta versión del CSV la columna de género se llama `Gender`, no `Genre` como dice el enunciado. El notebook la renombra al cargar los datos.

## Estructura del notebook

| Fase | Contenido |
|---|---|
| 0 | Exploración, codificación de `Genre` (0/1) y estandarización |
| 1 | K-Means: método del codo, Silhouette para k = 2 a 8 y perfiles de k = 5, 6 y 8 |
| 2 | Agglomerative (Ward): dendrograma, tabla de fusiones y Silhouette por k |
| 3 | DBSCAN: búsqueda de `eps` y `min_samples`, perfil de clusters y ruido |
| 4 | Visualización PCA en 3D de los tres métodos |
| 5–6 | Comparación final y elección de las clases naturales |
| 7 | Perfilado de los segmentos (medias, medianas, rangos, género y boxplots) |
| 8–10 | KNN para predecir el segmento: validación cruzada, evaluación y análisis de errores |
| — | Reflexión final y recomendaciones de marketing |

## Resultados principales

| Método | Parámetros | Clusters | Ruido | Silhouette |
|---|---|---:|---:|---:|
| **K-Means (elegido)** | k = 6 | 6 | N/A | **0.331** |
| Agglomerative | k = 6, linkage = "ward" | 6 | N/A | 0.310 |
| DBSCAN | eps = 0.9, min_samples = 8 | 4 | 25 | 0.269 |

**Segmentos encontrados (K-Means, k = 6):**

| Cluster | Clientes | Perfil |
|---:|---:|---|
| 0 | 41 | Mujeres maduras de ingreso medio y gasto moderado-bajo |
| 1 | 38 | Hombres jóvenes de gasto alto, con ingreso muy variado |
| 2 | 34 | Mujeres jóvenes de ingreso bajo-medio; gasto medio-alto en promedio, pero muy disperso |
| 3 | 35 | Hombres maduros de ingreso medio y gasto bajo-moderado |
| 4 | 23 | Premium: ingreso y gasto altos (91% mujeres) |
| 5 | 29 | Ingreso alto y gasto bajo: el segmento con más potencial sin aprovechar |

**Clasificador:** KNN con 9 vecinos (elegido con validación cruzada sobre train), **97.5% de accuracy** en test (39 de 40 clientes).

## Decisiones clave

- **k = 6 y no el mayor Silhouette (k = 8):** con k = 8 los clusters son 4 comportamientos repetidos en hombres y mujeres. El codo no es claro, así que la elección se apoya en el dendrograma, el Silhouette y la interpretabilidad de los perfiles.
- **K-Means y no DBSCAN:** DBSCAN junta a todas las mujeres en un solo cluster y deja como ruido a clientes de gasto bajo e ingreso medio-alto, un segmento importante para el negocio.
- **Agglomerative como alternativa:** da segmentos bastante parecidos (índice de Rand ajustado 0.65, coincidencia moderada a alta), con la diferencia de que su grupo premium es mixto en género.

## Limitaciones

- Solo 200 clientes y 4 variables.
- Los clusters se solapan en las fronteras (Silhouette máximo ≈ 0.33 con 6 clusters).
- El género, al ser binario, tiene mucho peso en la segmentación.
- El accuracy del clasificador se mide sobre solo 40 clientes de test.

## Fuente del dataset

[Mall Customer Segmentation Data — Kaggle](https://www.kaggle.com/datasets/vjchoudhary7/customer-segmentation-tutorial-in-python)
