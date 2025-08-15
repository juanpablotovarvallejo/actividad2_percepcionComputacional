## Actividad 2 — Percepción Computacional: Segmentación y estimación de áreas deforestadas

### Descripción
Este proyecto implementa un flujo básico de percepción computacional para cuantificar áreas deforestadas a partir de imágenes satelitales/fotográficas. A partir de cada imagen:
- Se convierte a escala de grises.
- Se enmascaran regiones muy brillantes (p. ej., nubes y texto) mediante un umbral fijo.
- Se segmenta por umbralización de Otsu.
- Se aplican operaciones morfológicas (apertura y erosión) para limpiar ruido.
- Se calcula el área deforestada contando píxeles blancos y convirtiendo a km² con un factor de calibración espacial.

El trabajo se realiza en el notebook `Actividad2_PercepcionComputacional.ipynb` y se incluye un conjunto de imágenes de ejemplo para distintos años.

### Estructura del repositorio
- `Actividad2_PercepcionComputacional.ipynb`: Notebook principal con todo el flujo de procesamiento.
- `frame_001.jpg` (año 2000), `frame_005.jpg` (2004), `frame_009.jpg` (2008), `frame_013.jpg` (2012), `frame_017.jpg` (2016): Imágenes de ejemplo.
- `README.md`: Este documento.
- `requirements.txt`: Dependencias del entorno (Python).

### Requisitos
- Python 3.9+ (recomendado 3.10 o 3.11)
- Sistema operativo con soporte para OpenCV (Linux/macOS/Windows)

Dependencias Python (ver `requirements.txt`):
- `opencv-python`
- `numpy`
- `matplotlib`
- `jupyterlab` (para ejecutar el notebook)

### Instalación
Se recomienda usar un entorno virtual.

```bash
# 1) Crear y activar un entorno virtual (Linux/macOS)
python3 -m venv .venv
source .venv/bin/activate

# En Windows (PowerShell)
# python -m venv .venv
# .venv\Scripts\Activate.ps1

# 2) Instalar dependencias
pip install -r requirements.txt
```

### Uso
1) Inicie JupyterLab y abra el notebook `Actividad2_PercepcionComputacional.ipynb`.
```bash
jupyter lab
```
2) Revise y, si es necesario, ajuste:
   - La lista de rutas de imágenes en la celda `image_paths`.
   - El factor de escala espacial `pixel_to_km`.
3) Ejecute las celdas en orden. Para cada imagen se mostrará una figura con cinco vistas:
   - Imagen original
   - Imagen enmascarada (se eliminan nubes/texto por brillo)
   - Imagen segmentada (Otsu)
   - Imagen tras apertura morfológica
   - Imagen erodada (final para conteo)
4) En la salida de cada bloque se imprimirá el área estimada en km², por ejemplo:
```
El área deforestada en el año 2000 es: X.XX km²
```

### Detalles del método
- Conversión a escala de grises: `cv2.cvtColor(..., cv2.COLOR_BGR2GRAY)`.
- Enmascaramiento de nubes/texto: se aplica una máscara binaria con umbral fijo sobre intensidades altas (ej. >160) y se anulan esos píxeles antes de segmentar.
- Segmentación: `cv2.threshold(..., cv2.THRESH_BINARY + cv2.THRESH_OTSU)`.
- Limpieza morfológica: apertura con `kernel = np.ones((2,2), np.uint8)` y posterior erosión con el mismo kernel.
- Cálculo de área: conteo de píxeles blancos en la imagen final y conversión a km² con `área_km2 = num_pixeles_blancos * (pixel_to_km)^2`.

### Calibración espacial (`pixel_to_km`)
El factor `pixel_to_km` convierte distancia en píxeles a kilómetros. En el notebook se usa por defecto:
```
pixel_to_km = 20 / 51  # 20 km por cada 51 píxeles
```
Para adaptar a otras imágenes:
- Mida en la imagen una referencia conocida (p. ej., una barra de escala o distancia entre dos puntos) y calcule `pixel_to_km = distancia_real_km / distancia_en_pixeles`.
- Revise la calidad de segmentación y ajuste el umbral de brillo o el tamaño del kernel si cambia la resolución o el contraste de las imágenes.

### Personalización y mejoras
- Ajustar el umbral de brillo (por defecto 160) para el enmascarado de nubes/texto según las características de iluminación.
- Modificar la estructura del kernel y el número de iteraciones en apertura/erosión para mejorar limpieza de ruido.
- Sustituir Otsu por umbrales adaptativos o modelos de segmentación más avanzados si la variabilidad de la escena es alta.
- Guardar resultados (máscaras/áreas) en archivos CSV o GeoTIFF si se requiere trazabilidad.

### Limitaciones
- Un umbral fijo puede no generalizar a escenas con iluminación heterogénea.
- La elección del kernel (2×2) y las iteraciones afectan el equilibrio entre eliminación de ruido y pérdida de detalle.
- El conteo de píxeles asume geometría plana y escala uniforme; para imágenes georreferenciadas, considere trabajar con resoluciones espaciales precisas y proyecciones adecuadas.

### Créditos y licencia
- Notebook y flujo desarrollados como parte de una actividad de Percepción Computacional.
- Imágenes de ejemplo incluidas en el repositorio.
- Licencia: no especificada. Si planea distribuir o reutilizar, añada un archivo `LICENSE` acorde (p. ej., MIT/Apache-2.0).

### Preguntas frecuentes
- ¿Puedo cambiar las imágenes? Sí, sustituya/añada rutas en `image_paths`.
- ¿Cómo cambio la escala? Actualice `pixel_to_km` conforme a su medición.
- ¿No se segmenta bien? Ajuste el umbral de brillo, pruebe umbral adaptativo, o incremente el kernel en las operaciones morfológicas.