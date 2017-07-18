# Introducción a Google Earth Engine (GEE)

## Día 1

Descripción general Google Earth Engine (Componentes de GE. Code Editor, Explorer y Clientes API).Mostrar un script complejo con un producto final.

Colecciones disponibles. Herramientas de clasificación. Ventajas y limitaciones frente a otras plataformas.

Nociones básicas de Code Editor: Introducción al lenguaje Javascript. Visión General del Code Editor. API Javascript de GEE. Convenciones: Guía de Estilo. Acceso a las funcionalidades de la API. Consultar la documentación de las funciones. Tipo de datos. (Listas, Diccionarios, ). Funciones definidas por el usuario.

### Inicio del tutorial

  * Manejo de datos vectoriales: Manejo de geometrías y generación de features. Creación y administración de colecciones de features. Carga y visualización de vectores utilizando Google Fusion Table (FT). Manejo de iteraciones sobre colecciones de features. Exportar como tabla de datos. Realizar gráficos.
  * Manejo de datos rasters: Seleccionar colecciones, filtros por áreas, por fechas y por nubes. Construir máscaras. Visualización. Cómo exportar imágenes (ventajas y limitaciones del servicio). Funciones de agregación. Cálculos de índices (NDVI, spectral unmixing e indicadores MapBiomas ndfi, por ejemplo, etc.). Generación de expresiones. Extracción de información a partir de features (agregación por medias, máximos, mínimos, etc.). Exportar como tabla de datos. Realizar gráficos.

## Día 2

### Casos de uso: Tutorial

#### Caso 1: Clasificaciones supervisadas

Incorporación de datos de campo y generación de datos de entrenamiento desde la interfaz de GE. Cálculo de mosaicos de índices del área de estudio. Muestreo a partir de los datos de campo. Separación en training/testing. Algoritmos disponibles para clasificación supervisada. Parametrización de los algoritmos. Ajustes. Validación utilizando matrices de confusión (accuracy, kappa). Aplicación del modelo y mapeo de resultados. Visualizar y exportar los resultados de  la clasificación.

#### Caso 2: Análisis de datos territorial

Dada una carta de la región a la que pertenece el alumno realizar las siguientes actividades:

 1. Generar un conjunto muestreado en la carta de interés o importar puntos que ud haya traído para el curso.
 2. Construir una colección de imágenes que a su criterio sean apropiadas para las fechas de los relevamientos. Recuerde que las colecciones son parte de objetos ImageCollection y estos contienen las diferentes funcionalidades para realizar filtrados.
 3. Construya un mosaico del área de estudio donde estén incluidas todas las variables (índices, bandas, reducciones, etc.) que ud considere necesarias para la clasificación.
 4. Extraiga información a partir de los puntos de muestreo y analicela. Realice gráficos para determinar bandas de mayor separabilidad y umbrales de separación.
 5. Genere reglas de decisión a partir del análisis realizado en el punto anterior.
 6. Aplicar las reglas generadas para realizar una clasificación.
 7. Visualizar los resultados.
