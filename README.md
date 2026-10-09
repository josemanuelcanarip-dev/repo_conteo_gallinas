# 🐔 Sistema de Detección y Conteo Automático de Gallinas Ponedoras

**Visión por computadora y deep learning · Granja rural en Lima, Perú**

Repositorio de desarrollo de la tesis **«Sistema de detección y conteo automático de gallinas ponedoras en una granja rural en Lima, Perú, mediante visión por computadora y deep learning»**, de la Maestría en Ciencias con mención en Inteligencia Artificial de la **Universidad Nacional de Ingeniería (UNI)**.

El proyecto busca detectar gallinas ponedoras en imágenes y videos, estimar su cantidad y evaluar si el seguimiento multiobjeto mediante **DeepSORT** mejora el conteo frente a un enfoque basado en detecciones independientes por fotograma. Se contempla el uso de modelos de la familia **YOLO** y la evaluación de diferentes configuraciones de aumento de datos.

---

## 👥 Autor

- **Josemanuel Rossy Cañari Palante** — [@josemanuelcanarip-dev](https://github.com/josemanuelcanarip-dev)

---

## 🎯 Objetivos de la investigación

### Objetivo general

Desarrollar y evaluar un sistema de visión por computadora basado en la detección y seguimiento multiobjeto para el conteo automático de gallinas ponedoras en imágenes y videos capturados en una granja rural de Lima, utilizando métricas de detección, seguimiento y conteo.

### Objetivos específicos

1. Construir y documentar un conjunto de imágenes y videos de gallinas ponedoras capturados en una granja rural de Lima, incluyendo su anotación para entrenamiento y evaluación.
2. Evaluar el efecto de diferentes configuraciones de aumento de datos sobre el desempeño del modelo de detección para el conteo de gallinas ponedoras en una granja rural en Lima, Perú.
3. Evaluar si la incorporación de DeepSORT reduce el error de conteo y la duplicidad de identificadores frente a un conteo base sin seguimiento.

### Relación con el código

| Objetivo | Trabajo previsto en el repositorio | Avance documentado |
|---|---|---|
| Construir y documentar el dataset | Inventario de videos, extracción de fotogramas, EDA y anotación con cajas delimitadoras | Notebooks de extracción y EDA; plantilla de anotación VIA disponible. Anotación completa y particiones pendientes. |
| Evaluar el aumento de datos | Entrenamiento y comparación de configuraciones sin aumento y con transformaciones de imagen | Pendiente de implementación. |
| Evaluar DeepSORT | Integración detector–seguidor y comparación del conteo con y sin seguimiento | Pendiente de implementación. |

---

## 📊 Dataset


Datos de captura propia obtenidos durante visitas de campo a los galpones.

| Característica | Descripción |
|---|---|
| Videos | 10 archivos `.mp4` |
| Resolución | 1920 × 1080 píxeles |
| Códec | H.264 |
| Frecuencia | Aproximadamente 15 FPS |
| Tamaño | Aproximadamente 256 MB por video |
| Duración | Aproximadamente 20.9 minutos por video; una grabación de 14.3 minutos |
| Muestreo de imágenes | Un fotograma cada 20 segundos |
| Imágenes extraídas | 610 archivos JPG |

| Fecha | Videos | Identificadores de cámara | Imágenes extraídas |
|---|---:|---|---:|
| 2024-08-05 | 2 | `tp00007`, `tp00015` | 126 |
| 2026-07-27 | 1 | `tp00000` | 63 |
| 2026-07-28 | 7 | `tp00008` a `tp00014` | 421 |
| **Total** | **10** | — | **610** |

Estas cifras corresponden al inventario documentado en esta etapa; deben actualizarse cuando cambien los videos o el intervalo de extracción. Las 610 imágenes extraídas no equivalen todavía a 610 imágenes anotadas.

### Nomenclatura y trazabilidad

- **Video:** `AAAAMMDD_HHMMSS_tpXXXXX.mp4`.
- **Fotograma:** `AAAAMMDD_HHMMSS_tpXXXXX_tSSSSs_N.jpg`.
- `AAAAMMDD_HHMMSS`: fecha y hora de inicio de la grabación.
- `tpXXXXX`: identificador de cámara.
- `tSSSSs`: segundo de extracción dentro del video.
- `N`: número de orden del fotograma.

Ejemplo: `20240805_215845_tp00007_t0020s_2.jpg` identifica el segundo fotograma, extraído a los 20 segundos del video correspondiente.

### Anotación y partición previstas

El plan de tesis contempla anotar las gallinas visibles mediante **cajas delimitadoras** con **VGG Image Annotator (VIA)**. La plantilla disponible es `artifacts/annotate_pollo_template.json`.

La preparación para modelado incluirá revisar las anotaciones, convertirlas al formato del detector y definir los subconjuntos de entrenamiento, validación y prueba.

**Criterio propuesto para la implementación:** separar los datos por grabación o sesión de captura, evitando que fotogramas muy similares de una misma secuencia queden repartidos entre entrenamiento y evaluación. Documentar la partición y aplicar el aumento de datos únicamente al entrenamiento.

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
├── src/ # codigo fuente
├── logs/ # logs
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
