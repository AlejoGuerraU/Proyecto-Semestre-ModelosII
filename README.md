Video: https://youtu.be/hHPwQplVNoI

# Clasificación supervisada de fallas en motores de combustión interna

Proyecto académico del curso **Modelos y Simulación II** de la **Universidad de Antioquia**. Se estudia la clasificación de cuatro estados de un motor a partir de mediciones de operación, consumo y emisiones del conjunto EngineFaultDB. El objetivo es comparar seis clasificadores y evaluar si PCA y UMAP permiten reducir las variables manteniendo un desempeño similar.

El flujo utiliza Python, Jupyter/Google Colab y bibliotecas de análisis y aprendizaje automático, entre ellas pandas, NumPy, SciPy, scikit-learn, Matplotlib, Seaborn y UMAP-learn.

## Problema y clases

La variable objetivo es `Fault`. Cada registro corresponde a una observación etiquetada en una de estas categorías:

| Código | Estado |
| --- | --- |
| 0 | Funcionamiento normal |
| 1 | Mezcla aire-combustible rica |
| 2 | Mezcla aire-combustible pobre |
| 3 | Bajo voltaje |

El problema es multiclase supervisado: a partir de mediciones numéricas del motor, los modelos predicen la etiqueta `Fault`. La dificultad principal observada está en distinguir las clases 2 y 3. Los resultados describen el conjunto y la partición evaluados; no prueban causalidad ni garantizan generalización a otros motores o condiciones.

## Dataset

El archivo [`EngineFaultDB_Final.csv`](EngineFaultDB_Final.csv) está incluido en el repositorio; no se requiere descargarlo por separado. Contiene 55 999 filas, 15 columnas, 14 predictores numéricos y la etiqueta `Fault`.

| Clase | Observaciones | Proporción |
| --- | ---: | ---: |
| 0 — Normal | 16 000 | 28,57 % |
| 1 — Mezcla rica | 10 998 | 19,64 % |
| 2 — Mezcla pobre | 15 000 | 26,79 % |
| 3 — Bajo voltaje | 14 001 | 25,00 % |

Los predictores cubren cuatro grupos: admisión y control (`MAP`, `TPS`); desempeño y velocidad (`Force`, `Power`, `RPM`, `Speed`); consumo (`Consumption L/H`, `Consumption L/100KM`); y emisiones y mezcla (`CO`, `HC`, `CO2`, `O2`, `Lambda`, `AFR`). Según el informe, EngineFaultDB se obtuvo de un motor de encendido por chispa C14NE bajo condiciones de laboratorio. El informe cita el trabajo de [Vergara et al. (2023)](https://doi.org/10.1109/ACCESS.2023.3331316) como referencia del conjunto.

La exploración guardada reporta cero valores faltantes y una fila duplicada (un par de filas idénticas). No se documenta que esa fila se haya eliminado. No se requiere codificar variables categóricas: las entradas son numéricas. Los modelos que lo necesitan escalan las variables dentro de sus pipelines; no se describe imputación. La búsqueda de características redundantes identifica correlaciones Pearson de 0,99999 entre `Lambda` y `AFR`, y de 0,99689 entre `RPM` y `Speed`.

## Estructura del repositorio

```text
.
├── README.md
├── Analisis_exploratorio.ipynb
├── EngineFaultDB_Final.csv
├── Informe final.pdf
├── Modelos2.pdf
├── Primer informe.pdf
└── entrenamiento de modelos/
    ├── Distribuciones variables por clase.ipynb
    ├── high_correlation_pairs.csv
    ├── KNN_EngineFaultDB.ipynb
    ├── MLP_EngineFaultDB_Colab.ipynb
    ├── modelo_parametrico.ipynb
    ├── predictor_correlations_pearson.csv
    ├── prueba.ipynb
    ├── SVM_EngineFaultDB_Colab.ipynb
    ├── TREES.ipynb
    ├── univariate_discrimination.csv
    └── univariate_feature_analysis.py
```

- [`Analisis_exploratorio.ipynb`](Analisis_exploratorio.ipynb): inspección de datos, clases, distribuciones, correlaciones, valores atípicos y pruebas estadísticas.
- [`entrenamiento de modelos/`](entrenamiento%20de%20modelos/): notebooks de clasificación, análisis por clase y archivos tabulares de correlación y discriminación univariada.
- [`univariate_discrimination.csv`](entrenamiento%20de%20modelos/univariate_discrimination.csv): resultados ANOVA por predictor (`F`, `eta_squared` y `p_value`); [`predictor_correlations_pearson.csv`](entrenamiento%20de%20modelos/predictor_correlations_pearson.csv): matriz de correlación entre predictores; [`high_correlation_pairs.csv`](entrenamiento%20de%20modelos/high_correlation_pairs.csv): pares con `|r| >= 0,95`.
- Los tres PDF son informes académicos disponibles; [`Informe final.pdf`](Informe%20final.pdf) contiene la metodología y los resultados finales. [`Primer informe.pdf`](Primer%20informe.pdf) y [`Modelos2.pdf`](Modelos2.pdf) son documentos previos del proyecto.
- [`univariate_feature_analysis.py`](entrenamiento%20de%20modelos/univariate_feature_analysis.py): calcula ANOVA de una vía y eta-cuadrado por predictor, además de correlaciones Pearson entre predictores. Por defecto crea `resultados/analisis_individual/` y guarda allí tres CSV.

## Tecnologías y dependencias

- **Lenguaje:** Python 3.
- **Bibliotecas:** pandas, NumPy, SciPy, scikit-learn, Matplotlib, Seaborn y `umap-learn`.
- **Entorno de notebooks:** Jupyter Notebook/JupyterLab o Google Colab. Los notebooks de clasificación que usan `google.colab.files` están preparados para Colab.
- **Dependencias declaradas:** no hay `requirements.txt`, archivo Conda ni otro manifiesto de entorno. Las versiones no están fijadas. Una salida guardada de `TREES.ipynb` registra Python 3.13, `umap-learn` 0.5.12, NumPy 2.4.6, SciPy 1.16.3 y scikit-learn 1.6.1; son versiones observadas en esa ejecución, no una configuración bloqueada para todo el repositorio.

## Instalación y configuración

Para explorar los datos o ejecutar el script desde una copia local:

```bash
git clone https://github.com/AlejoGuerraU/Proyecto-Semestre-ModelosII.git
cd Proyecto-Semestre-ModelosII
python -m venv .venv
```

Activa el entorno y luego instala las bibliotecas:

```powershell
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
```

```bash
# Linux o macOS
source .venv/bin/activate
```

```bash
python -m pip install --upgrade pip
python -m pip install numpy pandas scipy scikit-learn matplotlib seaborn umap-learn jupyterlab ipykernel
```

Inicia JupyterLab desde la raíz del repositorio para que el notebook exploratorio encuentre el CSV mediante su ruta relativa:

```bash
jupyter lab
```

En Google Colab, abre el notebook deseado desde GitHub o cárgalo en Colab. Sube `EngineFaultDB_Final.csv` al entorno de ejecución cuando el notebook lo solicite. `Analisis_exploratorio.ipynb` y `Distribuciones variables por clase.ipynb` no incluyen una celda de carga: en Colab, carga el CSV manualmente antes de ejecutar sus celdas. Algunos notebooks instalan `umap-learn` durante la ejecución; se requiere acceso a Internet para esa instalación si el paquete no está disponible.

## Metodología implementada

1. **Exploración:** se revisan la estructura, distribución de `Fault`, relaciones entre variables, valores atípicos y diferencias por clase. El notebook exploratorio incluye además una prueba t entre las clases 0 y 1.
2. **Preprocesamiento:** los modelos lineales, KNN, SVM y MLP aplican `StandardScaler` dentro de un `Pipeline`. En la búsqueda de LDA se comparan las 14 variables con un conjunto de 12 que excluye `AFR` y `Speed`. SVM usa `class_weight="balanced"`. El experimento principal de modelos no documenta una imputación ni una eliminación de la fila duplicada.
3. **Partición y ajuste:** los notebooks principales usan una división estratificada 80/20 con semilla 42 (44 799 observaciones de entrenamiento y 11 200 de prueba). La optimización usa validación cruzada estratificada de cinco pliegues y F1 macro como criterio de selección. SVM ajusta hiperparámetros inicialmente con una submuestra de 15 000 observaciones de entrenamiento y luego se ajusta con todo el conjunto de entrenamiento.
4. **Modelos:** análisis discriminante lineal (LDA), análisis discriminante cuadrático (QDA), K vecinos más cercanos (KNN), Random Forest (RF), SVM con kernel RBF y perceptrón multicapa (MLP).
5. **Evaluación:** se reportan exactitud, precisión macro, exhaustividad macro, F1 macro y métricas por clase. Los intervalos de confianza del test se estiman mediante bootstrap cuando están disponibles.
6. **Reducción de dimensionalidad:** PCA conserva seis componentes para alcanzar al menos 95 % de varianza explicada; UMAP genera una representación de dos dimensiones. La comparación central del informe aplica ambas técnicas a KNN y RF. Los notebooks de SVM y MLP también contienen experimentos adicionales de reducción y no deben confundirse con esa comparación central.
7. **Análisis de variables:** el script calcula ANOVA y eta-cuadrado entre las cuatro clases, matriz Pearson y pares con correlación absoluta de al menos 0,95. Los CSV ya incluidos son resultados tabulares de este análisis.

## Ejecución y reproducción

### Recorrido recomendado

1. Ejecuta [`Analisis_exploratorio.ipynb`](Analisis_exploratorio.ipynb) desde la raíz para revisar calidad, clases y relaciones del CSV.
2. Si necesitas regenerar el análisis univariado y las correlaciones, ejecuta desde la raíz:

   ```bash
   python "entrenamiento de modelos/univariate_feature_analysis.py"
   ```

   Por defecto, los resultados se escriben en `resultados/analisis_individual/`; los CSV existentes en `entrenamiento de modelos/` son archivos del repositorio y no se sobrescriben con esta ruta predeterminada.

3. Ejecuta de principio a fin los notebooks principales de clasificación. Cada uno carga el CSV por separado; no dependen de la salida de otro notebook. Para respetar la configuración guardada, se recomienda Google Colab:

   | Notebook | Propósito y requisitos | Salidas destacadas |
   | --- | --- | --- |
   | [`modelo_parametrico.ipynb`](entrenamiento%20de%20modelos/modelo_parametrico.ipynb) | Compara LDA y QDA con 14 y 12 variables; requiere el CSV en el directorio de trabajo o cargarlo cuando lo solicite. | Métricas, intervalos, gráficas y matrices en el notebook. |
   | [`KNN_EngineFaultDB.ipynb`](entrenamiento%20de%20modelos/KNN_EngineFaultDB.ipynb) | Busca hiperparámetros de KNN, evalúa el modelo robusto y compara KNN con PCA y UMAP; requiere el CSV. | `knn_resultados_malla.csv`, `knn_resumen_final.csv` y gráficas. |
   | [`TREES.ipynb`](entrenamiento%20de%20modelos/TREES.ipynb) | Ajusta Random Forest y compara variables originales, PCA y UMAP; requiere el CSV y usa el selector de archivos de Colab. | Métricas, matrices y gráficas en el notebook. |
   | [`SVM_EngineFaultDB_Colab.ipynb`](entrenamiento%20de%20modelos/SVM_EngineFaultDB_Colab.ipynb) | Ajusta SVM con kernels lineal, RBF y polinómico; incluye comparaciones adicionales con PCA y UMAP. Requiere Colab para la carga de archivos. | `svm_grid_results.csv` y gráficas PNG en el entorno de ejecución. |
   | [`MLP_EngineFaultDB_Colab.ipynb`](entrenamiento%20de%20modelos/MLP_EngineFaultDB_Colab.ipynb) | Ajusta MLP; incluye PCA, UMAP y una prueba adicional de submuestreo de la clase 2. Requiere Colab para la carga de archivos. | CSV de malla/métricas/comparación y `matriz_confusion_mlp.png`. |

   Los archivos que generan estos notebooks se guardan en el directorio de trabajo de la sesión (en Colab, normalmente temporal) y no están todos versionados en el repositorio. Para repetir resultados, ejecuta las celdas en orden desde el inicio y conserva la semilla 42. El `GridSearchCV` de KNN realiza primero una búsqueda amplia y después una búsqueda restringida para elegir la configuración robusta (`n_neighbors=15`, distancia euclidiana y pesos uniformes), utilizada en el informe.

4. Para gráficas exploratorias opcionales, ejecuta [`Distribuciones variables por clase.ipynb`](entrenamiento%20de%20modelos/Distribuciones%20variables%20por%20clase.ipynb). El notebook [`prueba.ipynb`](entrenamiento%20de%20modelos/prueba.ipynb) contiene pruebas alternativas de SVM/MLP, clasificación jerárquica y separación de las clases 2 y 3; no forma parte del recorrido que produce los resultados principales del informe.
5. Contrasta las salidas con [`Informe final.pdf`](Informe%20final.pdf). No se guardan modelos entrenados serializados, por lo que deben volver a ajustarse al ejecutar los notebooks.

### Límites de ejecución

- `TREES.ipynb`, `SVM_EngineFaultDB_Colab.ipynb` y `MLP_EngineFaultDB_Colab.ipynb` importan `google.colab.files`; están preparados para Colab y requieren adaptar la carga de datos para ejecutarse directamente en Jupyter local. `prueba.ipynb` usa la ruta fija `/content/EngineFaultDB_Final.csv`.
- Para KNN+UMAP y RF+PCA/UMAP, el informe indica que la transformación se ajustó sobre todo el conjunto de entrenamiento antes de la validación cruzada. Por tanto, esos valores de validación pueden estar afectados por fuga entre pliegues. La partición de prueba se mantiene aparte, pero esta limitación debe tenerse en cuenta al interpretar la validación cruzada de las representaciones reducidas.
- En las salidas del informe no se guardaron intervalos bootstrap de test para KNN+PCA ni KNN+UMAP.

## Resultados principales

La tabla resume las configuraciones y métricas de test del informe final. `F1 CV` corresponde a la media de validación cruzada reportada sobre el conjunto de entrenamiento; los valores de test son puntuales.

| Modelo | Configuración seleccionada | Exactitud test | F1 macro CV | F1 macro test |
| --- | --- | ---: | ---: | ---: |
| LDA | 12 variables (sin `AFR`, `Speed`) | 0,5594 | 0,5555 | 0,5616 |
| QDA | 14 variables | 0,6733 | 0,6526 | 0,6572 |
| KNN | 14 variables, 15 vecinos | 0,7461 | 0,7586 | **0,7546** |
| Random Forest | 14 variables | 0,7442 | **0,7602** | 0,7517 |
| SVM RBF | 14 variables | 0,7462 | 0,7582 | 0,7513 |
| MLP | 14 variables | 0,7459 | 0,7552 | 0,7217 |

KNN obtuvo el mayor F1 macro puntual en test, mientras RF tuvo el mayor F1 macro medio en validación cruzada. Los intervalos bootstrap de test de KNN, RF y SVM se solapan; el informe no realiza una prueba pareada que permita declarar un modelo superior. Las clases 0 y 1 se clasifican con F1 cercano a uno en varios modelos; las clases 2 y 3 concentran la mayor confusión.

| Modelo y representación | Dimensiones | F1 macro test | F1 macro CV |
| --- | ---: | ---: | ---: |
| KNN original | 14 | 0,7546 [0,7483–0,7612] | 0,7586 |
| KNN + PCA | 6 | 0,7518 | 0,7592 |
| KNN + UMAP | 2 | 0,7488 | 0,7586 |
| RF original | 14 | 0,7517 [0,7453–0,7579] | 0,7602 |
| RF + PCA | 6 | 0,7536 [0,7468–0,7602] | 0,7588 |
| RF + UMAP | 2 | 0,7434 [0,7370–0,7504] | 0,7585 |

PCA mantiene resultados cercanos al espacio original con seis componentes. UMAP reduce a dos dimensiones con pérdidas pequeñas en F1 macro de test. No se guardaron intervalos de test para KNN reducido; el pequeño aumento puntual de RF con PCA no demuestra una mejora estadísticamente significativa.

## Informe y autores

El [informe final](Informe%20final.pdf) presenta la metodología, fundamentación y análisis detallado de resultados. También están disponibles los documentos previos [`Primer informe.pdf`](Primer%20informe.pdf) y [`Modelos2.pdf`](Modelos2.pdf).

Autores verificados en el informe final: **Juan David González Rodríguez**, **Cristian Sánchez García** y **Alejandro Guerra Urrego**, estudiantes de Ingeniería de Sistemas de la Universidad de Antioquia.

## Licencia y referencia de los datos

No se encontró un archivo de licencia en el repositorio ni una condición de redistribución del dataset. Aunque el CSV está incluido aquí, verifica las condiciones de uso de la fuente antes de redistribuirlo fuera del proyecto académico. El informe final cita a Vergara et al., “EngineFaultDB: A novel dataset for automotive engine fault classification and baseline results”, *IEEE Access*, 2023, [doi:10.1109/ACCESS.2023.3331316](https://doi.org/10.1109/ACCESS.2023.3331316).
