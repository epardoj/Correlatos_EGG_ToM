# Correlatos_EGG_ToM
# Datos de Eye Tracking, EEG y Teoría de la Mente en niños sordos y oyentes

## 📋 Descripción general

Este repositorio contiene la base de datos y el código de análisis de un estudio
sobre **Teoría de la Mente (ToM)** en niños sordos y oyentes, evaluada mediante
métricas de **eye tracking** (seguimiento ocular) y **EEG**
(electroencefalograma).

La base de datos registra, para cada participante, su desempeño en **cuatro
tareas de Teoría de la Mente** junto con un conjunto de indicadores
conductuales-oculomotores y electrofisiológicos asociados.

- **Archivo principal:** `datos_completos_.xlsx` (hoja `Sheet1`)
- **Script de análisis:** `Correlatos.Rmd`
- **Unidad de análisis:** sujeto × tarea (diseño de medidas repetidas)
- **N total de registros:** 150 (35 sujetos × 4 tareas)
- **N de sujetos:** 35 → 15 niños sordos (SOR) y 20 niños oyentes (OY)
- **Rango de edad:** 9–12 años

---

## 🗂️ Estructura de la base de datos

Cada fila corresponde a **un participante en una tarea específica**. Como cada
sujeto completó las 4 tareas, cada `ID_Sujeto` aparece 4 veces.

### Variables de identificación y sociodemográficas

| Variable | Tipo | Descripción | Codificación |
|---|---|---|---|
| `ID_Sujeto` | Texto | Identificador único del participante | `SOR1`–`SOR15`, `OY1`–`OY20` |
| `Tipo_Participante` | Categórica | Grupo de pertenencia | `1` = Oyente (OY); `2` = Sordo (SOR) |
| `Tarea` | Categórica | Tarea de ToM administrada | `1`, `2`, `3`, `4` |
| `Genero` | Categórica | Género del participante | `1` = Masculino; `2` = Femenino |
| `Edad` | Numérica | Edad en años | 9–12 |

### Métricas de Eye Tracking

| Variable | Descripción |
|---|---|
| `Tasa_Fijaciones` | Tasa de fijaciones oculares |
| `Prop_Tiempo_Fijando` | Proporción del tiempo de tarea en fijación |
| `Duracion_Prom_Global` | Duración promedio global de las fijaciones (s) |
| `Tiempo_Primer_Contacto` | Tiempo hasta el primer contacto visual (ms) |
| `Duracion_Primer_Contacto` | Duración del primer contacto visual (s) |
| `Tiempo_Tarea` | Tiempo total empleado en la tarea (s) |
| `Tasa_Sacadas` | Tasa de sacadas oculares |
| `Prop_Regresivas` | Proporción de sacadas regresivas |
| `Amplitud_Sacada` | Amplitud media de las sacadas |
| `Media_Pupila` | Diámetro pupilar medio (esfuerzo cognitivo) |
| `Variabilidad_pupila` | Variabilidad del diámetro pupilar |
| `Tasa_parpadeo` | Tasa de parpadeo |
| `Latencia_Media` | Latencia media de respuesta ocular (ms) |
| `Variabilidad_latencia` | Variabilidad de la latencia |

### Métricas de EEG

| Variable | Descripción |
|---|---|
| `media_potMU` | Potencia media de la banda Mu (μV²) |
| `DesvEst_potMU` | Desviación estándar de la potencia Mu (μV²) |
| `media_SupMU` | Media de la supresión Mu |
| `DesvEst_SupMU` | Desviación estándar de la supresión Mu |
| `media_Gamma` | Potencia media de la banda Gamma (μV²) |
| `DesvEst_Gamma` | Desviación estándar de la banda Gamma (μV²) |

### Variable de resultado

| Variable | Descripción | Codificación |
|---|---|---|
| `Respuesta` | Respuesta correcta/incorrecta en la tarea ToM | `0` = Incorrecto; `1` = Correcto |

---

## 🧮 Variables derivadas (generadas en `Correlatos.Rmd`)

El script estandariza las métricas (puntuaciones *z*) y construye **tres índices
compuestos** a partir de las métricas de eye tracking:

| Índice compuesto | Fórmula | Interpretación |
|---|---|---|
| `Esfuerzo_cognitivo` | `(Media_Pupila_z + Duracion_Prom_Global_z + Tasa_parpadeo_z) / 3` | Mayor valor = mayor esfuerzo cognitivo |
| `Velocidad_visomotora` | `(Tasa_Fijaciones_z + Tasa_Sacadas_z + Amplitud_Sacada_z + Tiempo_Primer_Contacto_z) / 4` | Mayor valor = mayor velocidad/exploración visomotora |
| `Eficiencia_estrategica` | `(Prop_Regresivas_z + Variabilidad_pupila_z + Variabilidad_latencia_z) / 3` | Mayor valor = mayor eficiencia estratégica |

> **Nota:** para que valores altos indiquen mayor esfuerzo/velocidad, se
> invirtió el signo de `Tasa_parpadeo_z` y `Tiempo_Primer_Contacto_z` antes de
> construir los índices. Las métricas de EEG también se estandarizaron
> (`media_Gamma_z`, `media_SupMU_z`, `DesvEst_Gamma_z`, `DesvEst_SupMU_z`).

---

## 🔍 Análisis incluido

El archivo `Correlatos.Rmd` realiza, para cada grupo (oyentes y sordos) y cada
tarea de ToM, **matrices de correlación de Pearson** entre:

1. La respuesta ToM (`Respuesta`) y los índices de eye tracking.
2. La respuesta ToM y las métricas de EEG estandarizadas.

Los resultados se presentan como tablas (`flextable`) y paneles de correlación
(`psych::pairs.panels`), e incluyen un módulo opcional de interpretación
automatizada mediante un modelo de lenguaje (`ellmer` / Groq).

---

## ⚠️ Notas de calidad de datos

- **Valores atípicos extremos:** se observan valores muy altos en
  `media_potMU`, `DesvEst_potMU` y `DesvEst_Gamma` (especialmente en SOR7,
  OY15 y OY16), que podrían requerir winsorización o revisión.
- **Proporciones > 1:** `Prop_Tiempo_Fijando` presenta valores > 1 en algunos
  registros.
- **Posibles duplicados:** SOR7 (Tarea 1) y SOR10 (Tarea 1) comparten valores
  idénticos en varias métricas; conviene verificar si es un artefacto de
  registro.
- **Ceros que podrían ser ausencia de dato:** algunas métricas presentan `0`
  (p. ej. `Tiempo_Tarea` en SOR1 Tarea 2 y 3).
- **Muestra pequeña:** N = 35 (15 sordos, 20 oyentes), lo que limita la
  potencia de las correlaciones por grupo y tarea.

---

## 🛠️ Requisitos

El análisis se ejecuta en **R** con los siguientes paquetes:

install.packages(c("readxl", "gtsummary", "flextable", "dplyr",
                   "apaTables", "corrplot", "psych", "correlation", "ellmer"))

## Licencia

- El **código** de este repositorio (incluyendo `Correlatos.Rmd`) está bajo la licencia **MIT**.
- Los **datos** (`datos_completos_.xlsx` y el diccionario de metadatos) están bajo la licencia **Creative Commons Attribution 4.0 International (CC BY 4.0)**.
