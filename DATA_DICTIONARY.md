# Diccionario de Datos y Metodología Técnica — Abyss Honduras

Este documento describe las fuentes satelitales, variables geoespaciales, fórmulas matemáticas del índice de riesgo y estructura del dataset histórico utilizado por **Abyss Honduras**.

---

## 1. Zonas Críticas Monitoreadas (9 Regiones)

| ID Zona (`zona`) | Nombre en Plataforma | Departamento | Latitud | Longitud | Puntos de Control |
| :--- | :--- | :--- | :---: | :---: | :---: |
| `Chamelecon_SPS` | Chamelecón (SPS) | Cortés | `15.43334` | `-88.02341` | 29 |
| `Rivera_Hernandez_SPS` | Rivera Hernández (SPS) | Cortés | `15.47227` | `-87.94778` | 25 |
| `Cofradia_SPS` | Cofradía (Occidente SPS) | Cortés | `15.41682` | `-88.16031` | 12 |
| `Rio_Ulua_El_Progreso` | Río Ulúa · El Progreso | Yoro | `15.39300` | `-87.81450` | 29 |
| `Tegucigalpa` | Tegucigalpa (Distrito Central) | Francisco Morazán | `14.08180` | `-87.20681` | 160 |
| `Choluteca` | Cuenca Baja Río Choluteca | Choluteca | `13.30560` | `-87.18330` | 18 |
| `La_Ceiba` | La Ceiba · Río Cangrejal | Atlántida | `15.75970` | `-86.78220` | 14 |
| `Tocoa_Bajo_Aguan` | Tocoa · Bajo Aguán | Colón | `15.68333` | `-86.00000` | 21 |
| `Santa_Rosa_de_Copan` | Santa Rosa de Copán | Copán | `14.77388` | `-88.77560` | 15 |

---

## 2. Esquema del Dataset Histórico (`data/Abyss_Historico_24Meses_Honduras.csv`)

El archivo contiene **225 registros** correspondientes a **25 meses consecutivos** (`2024-11` hasta `2026-11`) para las 9 zonas, extraídos mediante Google Earth Engine con un buffer de `500 m` alrededor de cada punto crítico a escala de `20 m`:

- **`zona`** *(string)*: Identificador único de la zona crítica.
- **`mes`** *(YYYY-MM)*: Mes de agregación temporal (`2024-11` a `2026-11`).
- **`VH_dB`** *(float)*: Retrodispersión media mensual de radar de apertura sintética **Sentinel-1 GRD** (`COPERNICUS/S1_GRD`, modo `IW`, polarización `VH`) en decibelios (`dB`). Valores más negativos indican mayor presencia de agua libre o suelo altamente saturado.
- **`NDWI`** *(float | null)*: Índice Diferencial de Agua Normalizado obtenido de **Sentinel-2 Surface Reflectance** (`COPERNICUS/S2_SR_HARMONIZED`, escenas con nubosidad `< 35%`), calculado como:
  $$\text{NDWI} = \frac{B_3 - B_8}{B_3 + B_8}$$
  donde $B_3$ es la banda verde (560 nm) y $B_8$ es el infrarrojo cercano (842 nm). Los meses con nubosidad persistente (`> 35%`) se conservan como `null` para evitar contaminación atmosférica.

---

## 3. Modelo de Fusión Multisatelital (Índice de Riesgo `0–100%`)

Para determinar la clasificación operativa (**SEGURO**, **PRECAUCIÓN** o **CRÍTICO**), la plataforma combina tres indicadores físicos normalizados:

1. **Componente Geotécnico — Deformación NISAR / InSAR (`50%`, máx. `50 pts`):**
   $$S_{\text{def}} = \min\left(50,\ \max\left(0,\ \frac{\Delta d}{24.0} \times 50\right)\right)$$
   - Umbral crítico estructural: **18.0 mm**.

2. **Componente Hidrológico Radar — Sentinel-1 VH (`30%`, máx. `30 pts`):**
   $$S_{\text{VH}} = \min\left(30,\ \max\left(0,\ \frac{-\text{VH}_{\text{dB}} - 11.5}{6.5} \times 30\right)\right)$$

3. **Componente Óptico Superficial — Sentinel-2 NDWI (`20%`, máx. `20 pts`):**
   $$S_{\text{NDWI}} = \min\left(20,\ \max\left(0,\ \frac{\text{NDWI} + 0.65}{0.85} \times 20\right)\right)$$
   *(Si el mes actual presenta nubosidad en Sentinel-2, el modelo utiliza automáticamente la última lectura válida registrada para mantener la continuidad operativa).*

4. **Índice Total y Semáforo Operativo:**
   $$R_{\text{total}} = \text{round}(S_{\text{def}} + S_{\text{VH}} + S_{\text{NDWI}})$$
   - **`0% – 34%` → SEGURO (`SAFE`)**: Monitoreo rutinario.
   - **`35% – 64%` → PRECAUCIÓN (`CAUTION`)**: Vigilancia activa de bordos, drenajes y niveles de cuenca.
   - **`65% – 100%` → CRÍTICO (`CRITICAL`)**: Alerta roja y activación de protocolos preventivos COPECO / SINAGER.
