# 🐔 Sistema de Detección y Conteo Automático de Gallinas Ponedoras

**Granja rural — Lima, Perú**

Proyecto de investigación de la **Maestría en Inteligencia Artificial (UNI)**, desarrollado en el
marco del curso **Proyecto de Investigación II (MIA 403)**.

El objetivo es construir la base de datos y el análisis exploratorio necesarios para un sistema que
**detecte y cuente gallinas ponedoras** a partir de grabaciones de video de los galpones, usando
técnicas de visión por computadora.

---

## 👥 Autor

- **Josemanuel Rossy Cañari Palante** — [@josemanuelcanarip-dev](https://github.com/josemanuelcanarip-dev)

---

## 🎯 Objetivo del repositorio

Este repositorio cubre la **etapa de preparación y análisis de datos** del proyecto:

1. **Inventariar y caracterizar** las grabaciones originales de los galpones.
2. **Extraer fotogramas** (frames) de los videos para construir el dataset de imágenes.
3. Realizar un **Análisis Exploratorio de Datos (EDA)** de videos y frames para evaluar calidad,
   iluminación, movimiento y redundancia antes de entrenar cualquier modelo.

---

## 📊 Dataset

- **Fuente:** captura propia en la visita de campo a los galpones.
- **Formato:** 10 videos `.mp4`, resolución **1920×1080**, codec **H.264**, ~**15 FPS**, ~**256 MB** cada uno.
- **Duración:** ~20.9 min por video (una grabación de 14.3 min).
- **Fechas y cámaras:**

| Fecha | Videos | Cámaras | Imágenes extraídas |
|---|---|---|---|
| 2024-08-05 | 2 | tp00007, tp00015 | 126 |
| 2026-07-27 | 1 | tp00000 | 63 |
| 2026-07-28 | 7 | tp00008 … tp00014 | 421 |
| **Total** | **10** | — | **610** |

- **Fotogramas:** se extrae 1 imagen cada **20 segundos** de cada video → **610 imágenes JPG**.
- **Nomenclatura:** `AAAAMMDD_HHMMSS_tpXXXXX` = fecha y hora de inicio + identificador de cámara.
  - Frame: `AAAAMMDD_HHMMSS_tpXXXXX_tSSSSs_N.jpg` (segundo + número de orden).

---

## 🗂️ Estructura del repositorio

```
11_proyecto_pollos/
├── data/
│   ├── raw/            # videos originales (.mp4)
│   └── processed/      # frames extraídos, una carpeta por video
├── notebooks/
│   ├── 11_procesarimagenes.ipynb   # extracción de frames de los videos
│   ├── 12_EDA_videos.ipynb         # EDA de los videos crudos
│   └── 13_EDA_frames.ipynb         # EDA de los frames extraídos
├── artifacts/
│   ├── annotate_pollo_template.json    # plantilla de anotación (VIA) para etiquetar gallinas
│   ├── eda_indice_frames.csv           # índice de frames con métricas
│   ├── eda_resumen_por_fecha.csv       # resumen de imágenes por fecha
│   └── eda_videos/                     # tablas CSV y figuras PNG del EDA de videos
│       ├── eda_config.json
│       ├── eda_inventario.csv
│       ├── eda_fotogramas.csv
│       ├── eda_transiciones.csv
│       ├── eda_resumen_por_video.csv
│       ├── eda_correlaciones.csv
│       ├── eda_denso.csv
│       └── fig01 … fig11 *.png
├── README.md
├── README1.md
├── requirements.txt
└── .gitignore
```

> **Nota:** las carpetas `src/` y `logs/` mencionadas en versiones previas del README aún no existen
> en el repositorio; se crearán en etapas posteriores (modelado y entrenamiento).

---

## 🔄 Flujo de trabajo

```
data/raw/*.mp4
      │
      │  11_procesarimagenes.ipynb  (frame cada 20 s)
      ▼
data/processed/<video>/<frame>.jpg
      │
      ├── 12_EDA_videos.ipynb    → artifacts/eda_videos/*
      └── 13_EDA_frames.ipynb    → artifacts/eda_indice_frames.csv,
                                    artifacts/eda_resumen_por_fecha.csv
```

| Notebook | Entrada | Salida | Descripción |
|---|---|---|---|
| `11_procesarimagenes.ipynb` | `data/raw` | `data/processed` | Extrae frames cada 20 s con OpenCV. |
| `12_EDA_videos.ipynb` | `data/raw` | `artifacts/eda_videos/` | Inventario, metadatos, brillo, contraste, nitidez, movimiento, estabilidad de cámara y cortes de escena. |
| `13_EDA_frames.ipynb` | `data/processed` | `artifacts/*.csv` | Conteo de frames, cobertura temporal, resolución, tamaño e iluminación. |

### ¿Qué responde el EDA de videos (`12_EDA_videos.ipynb`)?

- **¿Qué material hay?** → inventario, duración, resolución, codec, bitrate, almacenamiento.
- **¿Qué calidad tiene?** → iluminación, exposición, contraste, nitidez, color, ruido, compresión.
- **¿Qué sirve para detectar gallinas?** → movimiento, estabilidad de cámara, continuidad y
  redundancia entre fotogramas.

Las métricas se calculan con *seek* directo a los fotogramas de interés (sin decodificar los videos
completos) y se guardan como caché en `artifacts/eda_videos/` para reutilizarse.

---

## ⚙️ Requisitos

- **Python 3.13**
- Dependencias en `requirements.txt`:

  | Paquete | Versión |
  |---|---|
  | pandas | 2.3.3 |
  | numpy | 2.3.5 |
  | siuba | 0.4.0rc1 |
  | opencv-python | 5.0.0.93 |
  | matplotlib | 3.11.2 |

Instalación:

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# Linux / macOS
source .venv/bin/activate

pip install -r requirements.txt
```

---

## ▶️ Cómo ejecutar

1. Coloca los videos en `data/raw/` (no se versionan por su peso).
2. Ejecuta los notebooks **en orden**:
   1. `notebooks/11_procesarimagenes.ipynb` → genera los frames.
   2. `notebooks/12_EDA_videos.ipynb` → analiza los videos crudos.
   3. `notebooks/13_EDA_frames.ipynb` → analiza los frames.
3. Revisa los resultados en `artifacts/`.

> ⚠️ **Importante:** los notebooks fijan la ruta de trabajo con `os.chdir(...)` a una ruta local
> (`B:\...`). Al clonar el proyecto, **actualiza esa línea** o ejecuta desde la raíz del repositorio.

---

## 📁 Datos y control de versiones

- El `.gitignore` **excluye** el contenido de `data/raw/` (videos) y casi todo `data/processed/`
  (imágenes), conservando solo unas pocas imágenes de muestra para mantener la estructura.
- Los resultados del EDA en `artifacts/` **sí** se versionan (son ligeros).

---

## 📜 Licencia y uso

Uso **académico** — Universidad Nacional de Ingeniería (UNI). Proyecto de investigación de maestría.
