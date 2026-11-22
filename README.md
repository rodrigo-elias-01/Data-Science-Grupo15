# Factores asociados al acceso educativo en Paraguay y diferencias según departamento

Trabajo práctico final de **Data Science** Universidad Nacional de Asunción, Facultad Politécnica - **Grupo 15**

**Integrantes:** Rodrigo Elias Portillo Santacruz - Hugo Javier Sanchez Fidabel

## Problema

Paraguay presenta disparidades territoriales en educación entre el área metropolitana y el interior. Este proyecto analiza, con la Encuesta Permanente de Hogares Continua (EPHC) 2025, (i) si el nivel educativo y la asistencia escolar difieren entre departamentos y con qué magnitud, y (ii) si es posible predecir la no asistencia escolar de la población de 6 a 17 años y qué factores pesan más.

## Objetivos

**General:** analizar las diferencias en nivel educativo y acceso a la educación entre departamentos y evaluar la capacidad de predecir la no asistencia escolar (6 a 17 años).

**Específicos:**
1. **Fase 1:** describir años de estudio y composición por nivel educativo por departamento.
2. **Fase 2:** contrastar si existen diferencias significativas (y de qué tamaño) entre departamentos en años de estudio, asistencia y nivel educativo, y analizar la relación edad–años de estudio.
3. **Fase 3:** predecir la no asistencia escolar y determinar los predictores más influyentes.

## Datos y fuente

- **Fuente:** Instituto Nacional de Estadística (INE) Paraguay, *Encuesta Permanente de Hogares Continua (EPHC) Anual 2025*, registro de personas. Portal de microdatos: <https://www.ine.gov.py>.
- **Archivos en `data/`:**
  - `REG02_EPHC_ANUAL_2025.csv`: datos originales (55.934 filas × 213 columnas, separador `;`).
  - `EPHC_2025_limpio.csv`: resultado de la Fase 1 (51.861 filas × 16 columnas), entrada de las Fases 2 y 3.
- **Privacidad:** son microdatos públicos del INE, sin nombres ni documentos de identidad; solo se usan códigos de conglomerado, vivienda, hogar y persona.
- **Variables usadas:** `DPTO`, `P02` (edad), `añoest` (años de estudio), `ED0504` (nivel/grado aprobado), `ED08` (asistencia actual) y `ED10` (motivo de no asistencia), más las derivadas `NOMBRE_DPTO`, `nivel_educ_simplificado`, `asiste_actualmente` y `grupo_etario`.

## Estructura del repositorio

.
├── data
│   ├── EPHC_2025_limpio.csv              # datos limpios (salida de la Fase 1)
│   └── REG02_EPHC_ANUAL_2025.csv         # datos originales
├── notebooks
│   ├── notebook-etapa-1-grupo-15.ipynb   # Fase 1: comprensión, limpieza y EDA
│   ├── Notebook_fase_2_grupo_15.ipynb    # Fase 2: análisis inferencial
│   └── Notebook_fase3.ipynb              # Fase 3: análisis predictivo
├── output
│   ├── Informe-fase-1-grupo-15.pdf
│   ├── Informe-fase-2-grupo-15.pdf
│   ├── informe-fase-3-grupo-15.pdf
│   └── modelo_fase3.joblib       # modelo final entrenado (HistGradientBoosting)
├── README.md
├── requirements.txt              # dependencias
└── src                           # el codigo se encuentra en las notebooks
    └── README.md

## Tecnologías

Python 3.12 - pandas - NumPy - Matplotlib - Seaborn - SciPy - statsmodels - scikit-posthocs - scikit-learn - joblib - Jupyter / Google Colab. Versiones mínimas en [`requirements.txt`](requirements.txt).

## Instrucciones de ejecución

1. Clonar el repositorio e instalar las dependencias (se recomienda un entorno virtual):

   ```bash
   git clone https://github.com/rodrigo-elias-01/Data-Science-Grupo15.git
   cd Data-Science-Grupo15
   python3 -m venv .venv && source .venv/bin/activate
   pip install -r requirements.txt
   jupyter notebook
   ```

2. Ejecutar las celdas de cada notebook **de arriba hacia abajo**, en el orden indicado abajo.

### Orden de ejecución

| Orden | Notebook | Entrada | Salida |
|---|---|---|---|
| 1 | `notebooks/notebook-etapa-1-grupo-15.ipynb` | Datos originales del INE | `EPHC_2025_limpio.csv` y gráficos |
| 2 | `notebooks/Notebook_fase_2_grupo_15.ipynb` | `data/EPHC_2025_limpio.csv` | Resultados de los 4 contrastes |
| 3 | `notebooks/Notebook_fase3.ipynb` | `data/EPHC_2025_limpio.csv` | Métricas y modelo en `output/` |

### Origen de los datos al ejecutar

- Los notebooks se desarrollaron en Google Colab y **leen los datos desde URL**: el de la Fase 1 descarga el archivo original desde Google Drive, y los de las Fases 2 y 3 leen `EPHC_2025_limpio.csv` desde este repositorio en GitHub (`.../raw/refs/heads/main/data/EPHC_2025_limpio.csv`).
- **Para ejecutar sin conexión**, reemplazar en la celda de carga la URL por la ruta local, por ejemplo `pd.read_csv('../data/EPHC_2025_limpio.csv')` (Fases 2 y 3) y `pd.read_csv('../data/REG02_EPHC_ANUAL_2025.csv', sep=';', low_memory=False)` (Fase 1).
- El notebook de la Fase 3 guarda sus resultados en la carpeta `output/` con rutas relativas; conviene ejecutarlo con el directorio de trabajo en la raíz del repositorio (o crear `notebooks/output/` si se ejecuta desde `notebooks/`).

### Reproducibilidad

- **Semilla aleatoria:** `random_state=42` en la partición de datos, las búsquedas de hiperparámetros y los modelos de la Fase 3; las simulaciones bootstrap de la Fase 2 son estocásticas y sus intervalos pueden variar levemente entre ejecuciones.
- **Entorno de referencia:** Python 3.12. `output/modelo_fase3.joblib` debe cargarse con una versión de scikit-learn compatible con la que lo generó.

## Metodología (resumen)

- **Fase 1:** limpieza (faltantes estructurales vs. códigos de no respuesta, eliminación de 4.073 filas sin `ED0504`, variables derivadas) y análisis univariado y bivariado.
- **Fase 2:** Shapiro-Wilk y Levene para verificar supuestos; Kruskal-Wallis con post hoc de Dunn; χ² de independencia con V de Cramér y residuos ajustados (Benjamini-Hochberg); correlación de Spearman; IC por Agresti-Coull, Wilson y bootstrap. α = 0,05.
- **Fase 3:** clasificación binaria de no asistencia (6–17 años, n = 11.796; 3,59 % de positivos). `Pipeline` con imputación, estandarización y One-Hot; partición `GroupShuffleSplit` 80/20 por UPM y `GroupKFold` (k = 5) para evitar fuga de información; modelos: línea base, regresión logística, Random Forest e `HistGradientBoostingClassifier`, optimizados por F1.

## Síntesis de resultados

- **Fase 1:** años de estudio con media 8,38 y mediana 8; marcada heterogeneidad territorial: educación terciaria de 35,4 % en Asunción y 25,1 % en Central frente a 12–17 % en la mayoría de los demás departamentos (15,2 % en Caazapá). Asociación edad–años de estudio débil (Spearman = 0,18).
- **Fase 2:** se rechazan las cuatro hipótesis nulas (p ≈ 0), pero los tamaños del efecto son pequeños (V de Cramér = 0,036 en asistencia y 0,091 en nivel educativo; ρ = 0,18): hay significancia estadística con relevancia práctica moderada. Las brechas más claras son Asunción y Central frente a Caazapá, San Pedro e Itapúa.
- **Fase 3:** el mejor modelo (HistGradientBoosting) obtiene en prueba exactitud 0,971, precision 0,654, recall 0,400, F1 0,496 y ROC-AUC 0,922, frente a una línea base de AUC 0,50. La edad es el predictor dominante (caída de AUC por permutación de 0,44), seguida por años de estudio (0,09); el departamento aporta casi nada una vez conocidos ambos. El modelo sirve como herramienta de cribado y no debe usarse para excluir automáticamente a nadie.

## Informes

Los informes de cada fase están en [`output/`](output/).

## Licencia y uso de los datos

Proyecto con fines académicos. Los datos pertenecen al INE Paraguay y se redistribuyen únicamente para reproducir el análisis.