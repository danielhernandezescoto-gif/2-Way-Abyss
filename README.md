# Abyss Honduras — Sistema Predictivo Multisatelital de Riesgo Hídrico y Geotécnico (COPECO / SINAGER)

Plataforma geoespacial y analítica de alerta temprana diseñada para monitorear **9 zonas críticas en Honduras** (Valle de Sula, Río Ulúa, Tegucigalpa, Choluteca, La Ceiba, Bajo Aguán y Santa Rosa de Copán), integrando fusión multisatelital (**NISAR**, **Sentinel-1**, **Sentinel-2**) con pronóstico hidrometeorológico (**Open-Meteo** y **SMAP**) en ventanas de anticipación de **6 a 24 días**.

---

## Estructura del Repositorio

```text
Abyss_Honduras_GitHub_Completo/
├── index.html                                # Aplicación web principal (estructura modular)
├── abyss_honduras_standalone.html            # Versión autocontenida en un solo archivo (lista para abrir offline/sin servidor)
├── alerta_ciudadana_honduras.html            # Portal comunitario simplificado con semáforo de alerta y contacto al 9-1-1
├── README.md                                 # Documentación principal del proyecto y guía de despliegue
├── DATA_DICTIONARY.md                        # Diccionario técnico de variables, fórmulas y fuentes satelitales
├── css/
│   └── styles.css                            # Paletas de alto contraste para modos Azul, Oscuro y Claro
├── js/
│   ├── data_zones.js                         # Coordenadas, puntos de control y dataset real de 25 meses (GEE)
│   └── app.js                                # Motor lógico: fusión multisatelital, Leaflet, Chart.js y PDF AutoTable
├── data/
│   └── Abyss_Historico_24Meses_Honduras.csv  # Dataset histórico real (Nov 2024 – Nov 2026) extraído de Google Earth Engine
└── scripts/
    ├── descargar_datos_reales_nasa.py        # Consulta y descarga de escenas Sentinel-1 en NASA/ASF (asf_search)
    └── gee_historico_24_meses.js             # Script de Google Earth Engine para exportar series mensuales (VH y NDWI)
```

---

## Funcionalidades Principales

1. **Índice Compuesto de Riesgo Multisatelital (0–100%):**
   - **Deformación del terreno / bordos (NISAR / InSAR):** Peso del **50 %** (umbral crítico en `18.0 mm`).
   - **Saturación superficial por radar (Sentinel-1 VH en dB):** Peso del **30 %** (opera incluso bajo cobertura nubosa).
   - **Agua superficial óptica (Sentinel-2 NDWI):** Peso del **20 %** (con respaldo automático sobre la última lectura válida en meses nublados).
2. **Mapa Satelital Exclusivo de Honduras:**
   - Delimitado estrictamente al territorio hondureño (`12.85°N–16.65°N`, `-89.45°W–-83.05°W`) con etiquetas de métricas en vivo directamente sobre la imagen satelital.
3. **Línea de Tiempo Interactiva (`▶ Ver Evolución`):**
   - Simulación y reproducción paso a paso de los **6 pases satelitales cada 12 días** (`15 sep` a `14 nov 2026`).
4. **Historial Satelital Real de 2 Años (25 Meses · Nov 2024 – Nov 2026):**
   - Gráficas mensuales reales extraídas de **Google Earth Engine** (`COPERNICUS/S1_GRD` y `COPERNICUS/S2_SR_HARMONIZED`).
5. **Informe Ejecutivo PDF Tabular de 2 Páginas:**
   - Generación automática mediante `jsPDF` + `jsPDF-AutoTable` en orientación horizontal con semáforo de riesgo, pronóstico a +6 días, acciones prioritarias para COPECO y resumen estadístico de 2 años.
6. **Soporte Multi-Tema e Internacionalización:**
   - Temas **Azul**, **Oscuro** y **Claro** de alto contraste, más traducción completa **Español (ES) / Inglés (EN)**.

---

## Cómo Ejecutar y Desplegar

- **Uso inmediato en Windows o Android:** Abre `abyss_honduras_standalone.html` directamente en Chrome o Edge. Puedes instalarla como aplicación nativa desde el menú del navegador (*Instalar aplicación* / *Agregar a la pantalla principal*).
- **Despliegue en GitHub Pages:**
  1. Sube todo el contenido de esta carpeta a un repositorio nuevo en GitHub (por ejemplo `abyss-honduras`).
  2. Ve a **Settings → Pages → Build and deployment → Source: Deploy from a branch (`main` / `/root`)**.
  3. GitHub publicará automáticamente `index.html` como sitio web en vivo.
