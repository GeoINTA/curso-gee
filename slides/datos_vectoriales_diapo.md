<!-- $theme: default -->
<!-- footer: Taller GEE - MapBiomas - EEA Salta. Noviembre 2017 -->
<!-- page_number: true -->
![100% top bg](../images/mapbiomas-bg.jpg)

<br>

Unidad 1: Manejo de datos vectoriales
==

---

# Contenidos 

Manejo de datos vectoriales: Manejo de geometrías y generación de features. Creación y administración de colecciones de features. Carga y visualización de vectores utilizando Google Fusion Table (FT). Manejo de  iteraciones sobre colecciones de features. Exportar como tabla de datos. Realizar gráficos.

---

# Manejo de geometrías y generación de features

Earth Engine maneja datos vectoriales con el tipo [ee.Geometry](https://developers.google.com/earth-engine/api_docs#eegeometry). La especificación [GeoJSON](https://tools.ietf.org/html/rfc7946) describe en detalle el tipo de geometrías soportadas por Earth Engine, incluyendo:

-   **Point**: una lista de coordenadas en alguna proyección,
-   **LineString**: una lista de puntos,
-   **LinearRing**: una LineString cerrada
-   **Polygon**: una lista de LinearRings donde la primera es una cáscara y los anillos subsiguientes son agujeros.

Earth Engine también soporta **MultiPoint**, **MultiLineString** y **MultiPolygon**. GeoJSON GeometryCollection también es compatible, aunque tiene el nombre MultiGeometry dentro de **EE**.

---

# Creación de Geometrías

Vamos a crear y mapear una geometría

```javascript
var point = ee.Geometry.Point([-60.54, -31.85]);
```

[ee.Geometry.Point](https://developers.google.com/earth-engine/api_docs#eegeometrypoint) es la llamada a la API Earth Engine que recibe dos parámetros una lista
**ee.List([])** y el segundo que es opcional una proyección que puede ser especificada como código EPSG [[1]](#ftnt1). El valor predeterminado es EPSG:4326 (WGS84 Lat/Lon)

---

# Creación de Geometrías

La definición de esa geometría (según la definición [GeoJSON](https://tools.ietf.org/html/rfc7946)) sería:

```javascript
    {
      "geodesic": true, // WGS84 Lat/Lon
      "type": "Point",
      "coordinates": [
        -60.54,
        -31.85
      ]
    }
```
--- 
# Mapear e imprimir un objeto geometry

De esa forma los datos son __almacenados__ y __utilizados__ internamente por la librería. 
Para poder ver en el [Code Editor](https://code.earthengine.google.com/) el formato nativo podemos hacer **print(point);** y luego en la consola hacer clic en JSON.

También podemos agregar la geometría en el mapa utilizando la siguiente instrucción:

```javascript
Map.addLayer(point,{'color':'00FF11'} ,'Punto');
```

**Tips**: Si quiere modificar el color de la geometría el formato hexadecimal de [colores html](http://htmlcolorcodes.com/es/) puede obtenerse desde Google realizando la búsqueda con [picker color](https://www.google.com.ar/#q=picker+color).

---
# Geometría de línea

El resto de las geometrías se construyen de la misma forma, veamos:

```javascript
var lineString = ee.Geometry.LineString([
	[-63, -36], 
        [-60.54, -31.85], 
        [-58, -28], 
        [-63, -27]]);
```

[ee.Geometry.LineString](https://developers.google.com/earth-engine/api_docs#eegeometrylinestring) recibe una lista de puntos y parámetros opcionales.

```javascript
//Agreganos la geometría al mapa
Map.addLayer(lineString,{'color':'CC0011'} ,'Linea');
```
---
# TIPS: Centrar el mapa
Podemos centrar el mapa en una geometría o _feature_ particular, eso lo podemos hacer con Map.centerObject así:

```javascript
// 1er elemento el objeto georeferenciado
// 2do elemento el nivel de zoom 0 más lejos
Map.centerObject(lineString, 7);
```
Ver ejemplo [aquí](https://code.earthengine.google.com/68da9c6a1b71fac1e3c56a60a9f5da3a)

---
# Geometría de línea

```javascript
var linearRing = ee.Geometry.LinearRing(
    [[-63, -36.09],[-59.54,-31.85],
    [-58, -28], [-63, -25],
    [-64, -27],[-63, -36]]);
```

[ee.Geometry.LinearRing](https://developers.google.com/earth-engine/api_docs#eegeometrylinearring) recibe una lista de puntos que a diferencia de LineString comienza y termina con el mismo punto para poder cerrar el anillo. También tiene parámetros opcionales.

Y si, al mapa!!

```javascript
Map.addLayer(linearRing,{'color':'b227b0'} ,'Anillo');
```
---
# Geometría Rectángulo   

```javascript
var rectangle = ee.Geometry.Rectangle([-62,-33, -59,-31]);
```

[ee.Geometry.Rectangle](https://developers.google.com/earth-engine/api_docs#eegeometryrectangle) recibe una lista con esquinas mínimas y máximas del rectángulo, como una lista de dos puntos en formato de coordenadas GeoJSON 'Point' o una lista de dos ee.Geometry que describen un punto, o una lista de cuatro números en el orden __xMin, yMin , xMax, yMax__.

```javascript
Map.addLayer(rectangle,{'color':'fbff23'} ,'Rectángulo');
```
---
# Geometría Polígono

```javascript
var vertices = [ 
	[-62.935,-34.415], 
	[-61.745,-34.411], 
	[-61.388,-34.068],
	[-62.663,-34.075] ];

var poligono = ee.Geometry.Polygon( vertices );
```

[ee.Geometry.Polygon](https://developers.google.com/earth-engine/api_docs#eegeometrypolygon) recibe una lista de anillos que definen los límites del polígono. Puede ser una lista de coordenadas en el formato 'Polygon' de GeoJSON, o una lista de ee.Geometry que describe un LinearRing. El resto de los parámetros son similares al resto de las geometrías.

```javascript
Map.addLayer(poligono, {'color':'16a322'} ,'Polígono');
```

---
# Geometrías Multiparte

Una geometría individual puede consistir en múltiples geometrías. 

```javascript
var multiPoint = ee.Geometry.MultiPoint(
          [
            [-62.319,-32.856],
            [-62.528,-32.944],
            [-62.418,-33.109],
            [-62.193,-33.008],
            [-62.166,-32.805]
            ]);

Map.addLayer(multiPoint,{'color':'16a322'},'multiPoint');
```
Para dividir una Geometría de varias partes en sus geometrías constitutivas, use **geometry.geometries()**.

---
# Geometrías multiparte
Vamos a imprimir los puntos de  la geometría, así que al objeto multiPoint le vamos a pedir todas las geometrías que lo componen.

```javascript
    var las_geometrias = multiPoint.geometries();
    print(las_geometrias);
```

1.  ¿Qué tipo de dato es la variable **las\_geometrias**?
<!-- es lista -->
2.  ¿Cómo puedo recuperar una de las geometrías contenidas en
    **las\_geometrias**? 
<!-- lista.get(id) -->

---
# Desafío 1
Convierta los puntos existentes en multiPoint a un LinearRing.
![100% center](../images/puntos.png)

---
# Crear Geometrías desde el mapa

Existe una forma muy práctica de dibujar Geometrías desde el mismo mapa del Code Editor.

Veamos un ejemplo:

 - Las opciones para dibujar están ubicadas en el sector superior izquierdo del mapa. 
 - Las herramientas disponibles permiten activar el dibujado de geometrías múltiples de: puntos, líneas y polígonos. <br/>Para dejar de dibujar se hace clic en la mano de la izquierda. 

![100% ](../images/image18.png)

---
# Crear Geometrías desde el mapa

 - Una vez que se activa la herramienta esta se habilita para poder dibujar. 
 - Se asigna un color al azar y cada figura que se trace formará parte de una geometría múltiple. 

![100% center](../images/image28.png)

---
# Crear Geometrías desde el mapa

- Es posible incorporar desde la sección de Geometry Imports una nueva capa. 
- Esta se instancia como una nueva variable de la clase Geometry.XXXX. 

![100% center](../images/image1.png)

---
# Crear Geometrías desde el mapa

- Las capas de geometrías que se van incorporando serán ubicadas en la sección de Imports del editor de código fuente. 
- Estos objetos son mostrados de manera formateada. Pero haciendo clic en el ícono azul se muestra el código fuente correspondiente para la creación de la geometría. 

![100% center](../images/image7.png)

---

# Crear Geometrías desde el mapa

El código fuente generado puede copiarse y pegarse en el script que se está escribiendo. 

**Ojo**: En algunos Navegadores no copia (Firefox 49.0.2, por ejemplo.) 

![Selección\_512.png](../images/image24.png)

---

# Geometrías Geodésicas y planas

- Una geometría creada en Earth Engine es **geodésica** (_los bordes son la trayectoria más corta en la superficie de una esfera_) o **planar** (_los bordes son el camino más corto en un plano cartesiano bidimensional_).

En la configuración predeterminada de la instanciación de un objeto ee.Geometry.XXXXX este se crea como EPSG:4326, es decir, será una geometría geodésica.

---
# Geometrías Geodésicas y planas

```javascript
var PoligonoGeo = ee.Geometry.Polygon([
[
    [-71.411,-39.470], [-57.128,-39.402],
    [-57.304,-33.394], [-70.751,-33.358],
    [-71.411,-39.470]]
]);
```

Esa misma geometría puede ser expresada en el plano, ya sea cuando se crea o puede ser convertida:

```javascript
var PoligonoPlano = ee.Geometry(PoligonoGeo, null, false);
```

Veámos cómo se ven estas geometrías en el mapa:

```javascript
Map.centerObject(PoligonoGeo);
Map.addLayer(PoligonoGeo, {color: 'FF0000'}, 'Geodésico');
Map.addLayer(PoligonoPlano, {color: '000000'}, 'Plano');
```

---

# Operaciones con Geometrías


Earth Engine admite una amplia variedad de operaciones en objetos Geometry. Estos incluyen operaciones en geometrías individuales tales como calcular un buffer, centroide, bounding box, perímetro, envolvente convexa, etc.

---

# Operaciones con Geometrías

Utilizando la definición del polígono de la anterior vamos a realizar algunas operaciones de geometrías:

-   Cálculo de área en KM²

```javascript
print('Área: ', poligono.area().divide(1000 * 1000));
// Todos los valores de mediciones de distancias 
// vienen expresados en metros.
```

---
# Operaciones con Geometrías

-   Longitud de perímetro en KM

```javascript
print('Perímetro: ', poligono.perimeter().divide(1000));
```
-   Mostrar el GeoJSON 'type'.

```javascript
print('Geometry type: ', poligono.type());
```

-   Mostrar la lista de coordenadas.

```javascript
print('Coordenadas: ', poligono.coordinates());
```

-   Muestra true/false si las coordenadas son o no geodésicas.

```javascript
print('¿Está en coordenadas geodésicas? ', 
       poligono.geodesic());
```
---
# Operaciones con Geometrías

-   Muestra el BBOX de una geometría

```javascript
print('Bounding Box', poligono.bounds());
// bounds retorna el rectángulo que envuelve a la geometría en una geometría plana.
```

-   Calcular el buffer de un polígono

```javascript
var buffer = poligono.buffer(5000);
// La distancia del buffer está expresada en metros.
```

-   Calcular el centroide de un polígono..

```javascript
var centroid = poligono.centroid();
```

---

# Operaciones con Geometrías

Ahora podemos mapear algunas de estas operaciones:

```javascript
Map.addLayer(buffer, {'color':'0be51e'}, 'buffer');
Map.addLayer(poligono, {}, 'el polígono');
Map.addLayer(centroid, {'color':'e5280b'}, 'centroide');
Map.centerObject(buffer, 7);
```

---

# Operaciones con Geometrías

Las  operaciones que hemos realizado han sido todas con operadores unarios, donde a la geometría le pedimos (o le calculamos) algo. Ahora vamos a probar algunos operadores entre geometrías.

Comenzamos con dos geometrías de polígono que las he creado desde el mapa como se mostró previamente.

```javascript

var poli1 = ee.Geometry.Polygon(
     [[[-63.62113952636719, -25.129433436071757],
       [-63.572044372558594, -25.128811779454853],
       [-63.57135772705078, -25.054501051619468],
       [-63.622169494628906, -25.05512308589585]]]);

var poli2 = ee.Geometry.Polygon(
       [[[-63.645172119140625, -25.061965254565465],
         [-63.65753173828125, -25.13005508952488],
         [-63.59367370605469, -25.128811779454853],
         [-63.59367370605469, -25.061343255018567]]]);
```

--- 
# Operaciones con Geometrías

Calculamos la unión de las dos geometrías. Donde el primer parámetro es la geometría que se quiere unir y el segundo es un margen de error. **ErrorMargin** es la cantidad máxima de error tolerada al realizar cualquier reproyección necesaria, el valor está expresado en metros.


```javascript
var poli1Upoli2 = poli1.union(poli2, ee.ErrorMargin(1));

Map.addLayer(poli1Upoli2, {color: 'FF00FF'}, 'Unión');        

// Centramos el mapa en la unión
Map.centerObject(poli1Upoli2, 12);
```
---

# Operaciones con Geometrías
 

Calculamos la intersección de los dos polígonos con la función intersection que

```javascript
var intersection = poli1.intersection(poli2, ee.ErrorMargin(1));
Map.addLayer(intersection, {color: '00FF00'}, 'Intersección');
```
 

Calculamos la diferencia entre el polígono 1 y el polígono 2. Es el área de la primer geometría que no comparte con la segunda.

```javascript
var diff1 = poli1.difference(poli2, ee.ErrorMargin(1));
Map.addLayer(diff1, {color: 'FFFF00'}, 'Diferencia( P1 - P2)');
```

Calculamos la diferencia simétrica, esta se define como el área de la geometría A y el área de la geometría B excepción el área común a ambas.

```javascript
var dif_sim = poli1.symmetricDifference( poli2, ee.ErrorMargin(1));
Map.addLayer(dif_sim, {color: '000000'}, 'Diferencia Simétrica');

// Mapeamos las dos geometrías con las que hicimos los ejemplos.
Map.addLayer(poli1, {color: 'FF0000'}, 'Polígono 1');
Map.addLayer(poli2, {color: '0000FF'}, 'Polígono 2');
```

**Desafío 2:** Verifique si una de las rectas que pasan por los puntos **[-63.635, -25.051]** y **[-63.617, -25.146]** intersecta la intersección (valga la redundancia) de las geometrías utilizadas previamente (poli1 y poli2).

**Desafío 3:** Ahora compruebe si el punto definido abajo está contenido en la geometría que resultó de la diferencia simétrica.

```javascript
var punto = ee.Geometry.Point([-63.6084366, -25.0803128]);
```

## Creación de Features

Un Feature de Earth Engine se define como un GeoJSON Feature. En pocas palabras, es un objeto con una propiedad de tipo Geometry y una propiedad de properties que almacena un diccionario de otras propiedades.

Entonces, necesitamos un objeto Geometry y opcionalmente un diccionario con los atributos de ese Feature.

```javascript

// La geometría
var poligono = ee.Geometry.Polygon(
       [[[-63.33892822265625, -25.150878651548442],
         [-63.33824157714844, -25.17791290009134],
         [-63.31043243408203, -25.17760219565173],
         [-63.31043243408203, -25.15025710411473]]]);
```

La declaración del Feature:

```javascript
var miFeature = ee.Feature(poligono,
    {variable_1: 100,
     variable_2: 'Hola'});
```

Al igual que con las geometrías podemos enviarlos a la consola utilizando print o mostrarlos en el mapa.

```javascript    
print(miFeature);
Map.addLayer(miFeature, {}, 'Mi Feature!');
Map.centerObject(miFeature, 12);    
```

La geometría del Feature puede ser nula y se podría crear el Feature solo con un diccionario:

```javascript
var dict = {distancia: ee.Number(10).add(150), lugar: 'Chivilcoy'};
var featureSinGeo = ee.Feature(null, dict);
```

Los Features tienen las mismas funcionalidades para gestionar sus geometrías que los objetos Geometry. Además, poseen otros métodos setters & getters para el manejo de las propiedades.

```javascript
var feature_ejemplo = ee.Feature(
      ee.Geometry.Point([-63.2951545715332,-25.163930416282465]))
        .set('Nombre', 'Eldes Monte')
        .set('Altura', 100);

// Recupero una propiedad del feature
var nombre = feature_ejemplo.get('Nombre');
print(nombre);

// Asigno una nueva propiedad
feature_ejemplo = feature_ejemplo.set('Población', 75000);

// Sobreescribo un nuevo diccionario
var newDict = {Nombre: 'El Lote', Altura: 300};
var feature_ejemplo = feature_ejemplo.set(newDict);

// Se muestran los resultados
print(feature_ejemplo);

Map.addLayer(feature_ejemplo, {}, 'Ejemplo 2');

```

## Creación y administración de colecciones de features

Los grupos de features relacionados se pueden combinar en una [FeatureCollection](https://developers.google.com/earth-engine/api_docs#eefeaturecollection), para permitir operaciones adicionales en todo el conjunto tales como: filtrado, clasificación y renderizado. Además de simples features (geometría + propiedades), FeatureCollection también puede contener otras colecciones.

### 1. FeatureCollection a partir de lista de Features

Podemos construir un FeatureCollection a partir de una lista de features, donde estos pueden tener o no el mismo tipo de geometría.

```javascript

var features = [
    ee.Feature(ee.Geometry.Point(-62.709,-31.428), {Estación: 'La Francia'}),
    ee.Feature(ee.Geometry.Point(-61.248,-31.475), {Estación: 'Pilar'}),
    ee.Feature(ee.Geometry.Point(-61.765,-31.840), {Estación: 'Sastre'}),
    ee.Feature(ee.Geometry.Point(-62.534,-31.858), {Estación: 'Las Varas'})];

var fc_desdeUnaLista = ee.FeatureCollection(features);

print(fc_desdeUnaLista);
Map.addLayer(fc_desdeUnaLista, {}, 'FC_Puntos');
Map.centerObject(fc_desdeUnaLista);
```

### 2. FeatureCollection a partir de Google Fusion Table

También podemos incorporar un FeatureCollection a partir de un Google Fusion Table. Google Fusion Tables (GFT) es un servicio de Google que permite manejar tablas. Esto habilita la importación de archivos vectoriales a la plataforma Google Earth Engine (GEE) a través de archivos vectoriales en formato KML.

#### Crear un nuevo Fusion Table

Estas tablas las gestionamos desde [Google Drive](https://drive.google.com), la utilidad debe estar habilitada si no aparece en el menú NUEVO. La habilitación se realiza desde:

    NUEVO > Más > + Conectar más aplicaciones

![Selección\_536.png](../images/image15.png)

Una vez habilitada vamos a poder subir una nueva tabla a través de la opción NUEVO > Más > Google Fusion Table (o Tablas dinámicas de Google en español).


|  |  |
| - | - |
| ![Screenshot - 170617 - 16:58:15.png](../images/image22.png) | ![Screenshot - 170617 - 17:18:05.png](../images/image5.png) |


La creación del Fusion Table importamos desde el sistema de archivos un documento .kml y luego hacemos clic en Next.

![](../images/image2.png)


Nos va a mostrar un preview del archivo donde podremos verificar los nombres de los atributos y el tipo de geometría que estamos subiendo.
Luego, Next.

![](../images/image31.png)


En esta sección podremos editar algunos parámetros de la nueva tabla como el nombre, la descripción y algunos permisos básicos. Damos clic en Finish.

![](../images/image12.png)


Ahora si ya está disponible la nueva tabla. Si vamos a la solapa Map of geometry podemos ver desplegada sobre un mapa de Google la columna geometría que se subió con el archivo kml.

![](../images/image9.png)


En la opción Share se puede ajustar las opción de seguridad de la nueva capa que fue subida al GFT.

![](../images/image23.png)


Para poder importar este archivo a GEE necesitamos el ID de la tabla, para ello vamos a File y seleccionamos About this table. Ahí encontraremos el identificador (Id).

![](../images/image6.png)


También en la opción “Enlace para compartir” disponible en Share, tenemos disponible el identificador de la GFT a través del cual vamos a vincular la tabla con nuestro script.

![](../images/image10.png)


**Crear un FeatureCollection desde GFT**: Para cargar una FeatureCollection desde una Fusion Table, proporcione al constructor (ee.FeatureCollection) el ID de tabla agregado a **ft:**. Por ejemplo:

```javascript
var desdeFT = ee.FeatureCollection('ft:1ns9ErIEndlHyVe3hOB8tw_mqM_8f3sZxZX0ltICc');
print(desdeFT);
Map.addLayer(desdeFT, {}, 'Región Chaqueña');
Map.centerObject(desdeFT);
```

### 3.  Creación de un FeatureCollection a partir de Tablas

Es posible instanciar un FC utilizando tablas que tenemos almacenadas en un Assets. Para crear esta nueva tabla podemos utilizar un Shapefile o
un .zip que contenga todos los archivos que componen el Shapefile. El tamaño máximo permitido es 10GB.

| | |
| - | - |
| Los pasos para subir un Shapefile son seleccionar desde Assets NEW \> Table Upload.  | ![](../images/image25.png)              |


| | |
| - | - |
| Luego seleccionamos desde nuestro sistema de archivo el shapefile y todos los archivos <br/>que lo componen (podemos seleccionar el .zip también).   | ![](../images/image4.png)               |


| | |
| - | - |
| Por default toma el nombre del shp para la tabla pero se puede editar. Además, podemos indicar la <br>codificación de caracteres que posee el shp para no encontrarnos luego<br>con caracteres mal interpretados.<br>Esta opción está en Advanced.<br><br>Ok para finalizar. | ![](../images/image3.png)               |

El upload no es instantáneo y puede demorar algunos minutos dependiendo de la congestión de la plataforma y el tamaño que tenga el archivo. Podemos verificar el progreso desde la solapa TASK:


| | |
| - | - |
| Tarea en proceso.                    | ![](../images/image20.png)              |
| Tarea finalizada.                    | ![](../images/image8.png)               |

| | |
| - | - |
| Finalizada la carga la Tabla estará <br/> disponible en el Assets de nuestro usuario. | ![](../images/image30.png)              |

```javascript
var txt_assets = 'users/<usuario>/muestras';
var muestreos = ee.FeatureCollection(txt_assets);
print(muestreos);
Map.addLayer(muestreos, {}, 'Muestras');
```
        

### 4. FeatureCollection de una muestra al azar

Es posible crear un FeatureCollection a partir de generar una muestra al azar [ee.FeatureCollection.randomPoints](https://developers.google.com/earth-engine/api_docs#eefeaturecollectionrandompoints) de puntos dada una región.

```javascript

var region = ee.Geometry.Rectangle(-63.457, -25.155, -62.699, -24.714);
var randomPoints = ee.FeatureCollection.randomPoints( region,
    100, // cantidad de puntos
    123); // seed

print(randomPoints)

Map.centerObject(randomPoints);

Map.addLayer(randomPoints, {}, 'Puntos al azar');
```

### 5.  Creación de FeaturesCollection desde el Mapa

Al igual que mostramos anteriormente como dibujar geometrías, es posible definir FeaturesCollection dibujando desde el mapa.

![](../images/image17.png)        

En la configuración podemos darle un nombre al Layer y agregar propiedades con valores por defecto y definir el color.

![](../images/image29.png)

![](../images/image27.png)

Podemos incorporar esto FeaturesCollection desde la sección de Imports.

![](../images/image14.png)

## Operaciones Básicas sobre FeaturesCollection

Existen varios métodos para recuperar información y metadatos de un FC.

```javascript
var key = 'ft:1ExULsxnCc7x8AJQmD7bsg9iQKrKMVbkbOJi62XVy';

var muestreos = ee.FeatureCollection(key);
Map.addLayer(muestreos, {}, 'Áreas muestreadas');
Map.centerObject(muestreos);

print(muestreos.limit(1)); // Limitamos el # de features
print('Lista de atributos:',muestreos.first().propertyNames()); //Listar los atributos del primer feature
print('Cantidad: ', muestreos.size()); // # de features en la colección
```

Operaciones de agregación por columnas.

```javascript
// Contar cuántas instancias de cada clase hay
print('Clases Distintas:',  muestreos.aggregate_count_distinct('class'));

// Suma toda la columna area_ha
print('Superficie Total (Ha):', muestreos.aggregate_sum('area_ha'));
```

**Desafío 4**: Calcule el tamaño promedio de las parcelas muestreadas.


## Construcción de Filtros

Todos los objetos de tipo colección tienen una método de aplicación de filtros (.filter) que a menudo requieren como parámetro de un objeto del tipo [ee.Filter](https://developers.google.com/earth-engine/api_docs#eefilter).

**Importante:** los filtros aplican sobre las __instancias__, es decir, sobre las filas.

```javascript

var key = 'ft:1ExULsxnCc7x8AJQmD7bsg9iQKrKMVbkbOJi62XVy';
var muestreos = ee.FeatureCollection(key);
Map.addLayer(muestreos, {}, 'Áreas muestreadas');

var limites = ee.Geometry.Rectangle( [-60.501708984375, -26.754194629270284, -60.373992919921875, -26.84051574561839]);
var filtrados = muestreos.filterBounds(limites);

print('Cantidad de features después de filtrar:', filtrados.size());

Map.addLayer(filtrados, {color: '1ae008'}, 'Filtrados por región');

var filtradasXarea = filtrados.filter(ee.Filter.gt('area_ha', 10));

print('Parcelas de más de 10 ha:', filtradasXarea.size());
Map.addLayer(filtradasXarea, {color: 'f4df42'}, 'Más de 10  ha');

```

**Desafío 5**: Construya un filtro que permita filtrar las clases de tipo 131.

**Desafío 6**: ¿Cuántas parcelas fueron etiquetadas como ARBUSTAL en la descripción?

## Operador Selection

Selección de propiedades de un Feature para generar un nuevo FeatureCollection.

**Importante:** las selecciones aplican sobre las __columnas__, es decir, sobre los atributos de una colección de features o las bandas cuando veamos colecciones de imágenes.


```javascript

var fc_seleccion = filtrados.select( ['area_ha', 'class'], ['AREA','CLASE']);
print(fc_seleccion);
```

## Manejo de  iteraciones sobre colecciones de features

Existen varias opciones para poder iterar sobre un FeatureCollection, una forma simple de modificar cada uno de los Features de un FeatureCollection es utilizando la instrucción [ee.FeatureCollection.map](https://developers.google.com/earth-engine/api_docs#eefeaturecollectionmap). Esta instrucción permite recorrer cada Feature y generar un FeatureCollection nuevo.

Veamos un ejemplo simple, supongamos que queremos incorporar al FeatureCollection de las muestras un atributo que sea perímetro. Esto requiere que para cada elemento (de tipo Feature) de la colección hagamos el cálculo, eso sería:


```javascript

var get_perimetro = function(elemento){
        return elemento.set(
            {perimetro: elemento.geometry().perimeter()}
        );
}

var m_con_perimetro = muestreos.map(get_perimetro);
print(m_con_perimetro);

```

Otro ejemplo de map, supongamos que tenemos un CSV con las coordenadas expresadas en grados decimales y queremos generar la geometría para cada Feature del FeatureCollection:

```javascript

var key = 'ft:1t-2SIDNQHZji_6iSWww0pAbd_4i33l8o68NUh4an';

var muestreos = ee.FeatureCollection(key);

// Muestreo sin geometría

print( muestreos.limit(1) );

var agregar_geometria = function( elemento ){
    var geom = ee.Geometry.Point(
        [
            elemento.get('longitud'),
            elemento.get('latitud')
        ]);

    return elemento.setGeometry(geom);
  };

Map.addLayer( muestreos.map(agregar_geometria), {}, 'Muestras');
```

**Desafío 7**: Escriba una función de mapeo que para valores de class entre 20 y 23 completen un nuevo atributo llamado TIPO con el valor “Bosque” y en caso contrario complete con “No Bosque”. [Solución](https://code.earthengine.google.com/c41df28ae30874a0096ec677dfdfe58e).


Existe otra forma de recorrer un FeatureCollection que es con el método [iterate](https://developers.google.com/earth-engine/api_docs#eefeaturecollectioniterate).


```javascript
var key = 'ft:1ExULsxnCc7x8AJQmD7bsg9iQKrKMVbkbOJi62XVy';
var muestreos = ee.FeatureCollection(key);

var n_dict = ee.Dictionary({});

var contar_clases = function(feat, n_dict){
  return ee.Dictionary(n_dict).set(
    feat.get('class'),
    muestreos.filter(
      ee.Filter.eq('class', feat.get('class')))
    .aggregate_count('class')
    );
}

var n_class = muestreos.distinct(['class']).iterate(contar_clases, n_dict);
print(n_class);
```

## Exportar como tabla de datos

Para exportar un FeatureCollection a Google Drive se requiere la instrucción Export.table.toDrive()

![](../images/image26.png)

### KML

```javascript
Export.table.toDrive({
          collection: <Nombre del FC>,
          description:'Una descripción de la capa para encontrarlo en Drive',
          fileFormat: 'KML o KMZ'
});
```

### CSV

```javascript
Export.table.toDrive({
    collection: <Nombre del FC>,
    description:'Una descripción de la tabla para encontrarla en Drive',
    fileFormat: 'CSV'
});
```

Ejemplo:

```javascript
var key = 'ft:1t-2SIDNQHZji_6iSWww0pAbd_4i33l8o68NUh4an';
var muestreos = ee.FeatureCollection(key);

var mapear_clase = function( elemento ){
return elemento.set('tipo',
ee.Algorithms.If( ee.Number(elemento.get('class')).gte(20).and(ee.Number(elemento.get('class')).lte(23))  ,
      'Bosque',
      'No Bosque'));
};

var fc_tipo = muestreos.map( mapear_clase );

// Exportar a CSV
Export.table.toDrive({
collection: fc_tipo,
description: 'TablaBosqueNoBosque', //Es el nombre que tendrá el archivo
fileFormat: 'CSV'
});
```

Luego de exportar se incluirá una nueva tarea que hay que poner a
correr.

![](../images/image11.png)

Dependiendo de la cantidad de features en la colección el proceso puede
demorar mucho o poco y además también dependerá de la carga de trabajo
de la plataforma.

![](../images/image19.png)


## Realizar gráficos

En GEE es posible realizar diferentes tipos de gráficos utilizando la librería ui.Chart. Las opciones disponibles para FeaturesCollection son:

-   ui.Chart.feature.byFeature(features, xProperty, yProperties)
-   ui.Chart.feature.byProperty(features, xProperties, seriesProperty)
-   ui.Chart.feature.groups(features, xProperty, yProperty,
    seriesProperty)
-   ui.Chart.feature.histogram(features, property, maxBuckets,
    minBucketWidth, maxRaw)

Ejemplos:

Graficar Features por Histogramas


```javascript
var key = 'ft:1ExULsxnCc7x8AJQmD7bsg9iQKrKMVbkbOJi62XVy';

var muestreos = ee.FeatureCollection(key);

var histograma = ui.Chart.feature.histogram(muestreos, 'area_ha', 5);

print(histograma);
```

![](../images/image16.png)

Graficar Features por Grupos

```javascript

var key = 'ft:1ExULsxnCc7x8AJQmD7bsg9iQKrKMVbkbOJi62XVy';
var muestreos = ee.FeatureCollection(key);

// Agrego el perímetro
var get_perimetro = function(elemento){
return elemento.set({perimetro: elemento.geometry().perimeter()});
}

var m_con_perimetro = muestreos.map(get_perimetro);

// Agrego el tipo Bosque / No Bosque
var mapear_clase = function( elemento ){
return elemento.set('tipo',
ee.Algorithms.If( ee.Number(elemento.get('class')).gte(20).and(ee.Number(elemento.get('class')).lte(23))  ,
      'Bosque',
      'No Bosque'));
};
var fc_tipo = m_con_perimetro.map( mapear_clase );

var chart = ui.Chart.feature.groups(fc_tipo, 'area_ha', 'perimetro', 'tipo')
.setChartType('ScatterChart')
.setOptions({
  hAxis: {title: 'Area (Ha)'},
  vAxis: {title: 'Perímetro (m)'},
}).setSeriesNames(["Bosque", "No Bosque"]);

print(chart);

```

![](../images/image21.png)


Bibliografía
============

API | Google Earth Engine API. [https://developers.google.com/earth-engine/api\_docs](https://developers.google.com/earth-engine/api_docs)

* * * * *

[[1]](#ftnt_ref1) European Petroleum Survey Group
