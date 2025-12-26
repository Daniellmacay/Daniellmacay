title: "Análisis Espacial: Cambio de Uso de Suelo en ANP"
description: "Evaluación de la pérdida de cobertura vegetal mediante Google Earth Engine y R."
feature-img: "assets/img/portfolio/portada-anp.jpg"  
img: "assets/img/portfolio/thumb-anp.jpg"
date: 2025-12-26
tags: [R, Google Earth Engine, GIS, Ciencia de Datos]
excerpt_separator: ---

Análisis multitemporal para detectar patrones de deforestación y urbanización dentro de Áreas Naturales Protegidas (ANP) utilizando imágenes satelitales y procesamiento en la nube.

###  Objetivo del Proyecto
Determinar la tasa de cambio anual de la cobertura vegetal y cuantificar la fragmentación del hábitat en el periodo 2015-2025. Este análisis es crítico para la toma de decisiones en estrategias de conservación y cumplimiento ESG.

###  Metodología y Herramientas
El flujo de trabajo automatizado integra **Google Earth Engine (GEE)** para la adquisición de datos y **R** para el análisis estadístico:

1.  **Adquisición:** Imágenes Sentinel-2 y Landsat 8 (Filtrado de nubes < 10%).
2.  **Pre-procesamiento:** Corrección atmosférica y cálculo de índices espectrales (NDVI).
3.  **Clasificación:** Algoritmo *Random Forest* supervisado.

#### Flujo de Trabajo (Diagrama)
<div class="mermaid">
graph LR
    A[Imágenes Satelitales] --> B(Procesamiento GEE);
    B --> C{Cálculo NDVI};
    C --> D[Clasificación Supervisada];
    D --> E[Matriz de Confusión];
    E --> F[Mapa Final & Estadísticas];
</div>

###  Modelado Matemático
Para evaluar la salud de la vegetación, se utilizó el Índice de Vegetación de Diferencia Normalizada ($NDVI$), calculado mediante la siguiente fórmula espectral:

$$NDVI = \frac{(NIR - Red)}{(NIR + Red)}$$

Donde $NIR$ es la banda infrarroja cercana y $Red$ la banda roja visible. Los valores resultantes permitieron segmentar las zonas de bosque denso frente a suelo desnudo.

###  Código Destacado (R)
Fragmento del script utilizado para calcular la matriz de transición de cambio:

```r
library(raster)
library(tidyverse)

# Cargar clasificaciones de dos periodos
t1 <- raster("cover_2015.tif")
t2 <- raster("cover_2025.tif")

# Calcular matriz de cambios
crosstab <- crosstab(t1, t2, long = TRUE) %>%
  mutate(change_type = case_when(
    layer.1 == 1 & layer.2 == 2 ~ "Deforestación",
    layer.1 == layer.2 ~ "Persistencia",
    TRUE ~ "Otros"
  ))
