# Clasificaciones Supervisadas

Contenidos: Incorporación de datos de campo y generación de datos de entrenamiento desde la interfaz de GE. Cálculo de mosaicos de índices del área de estudio. Muestreo a partir de los datos de campo. Separación en training/testing. Algoritmos disponibles para clasificación supervisada. Parametrización de los algoritmos. Ajustes. Validación utilizando matrices de confusión (accuracy, kappa). Aplicación del modelo y mapeo de resultados. Visualizar y exportar los resultados de  la clasificación.

## Preprocesamiento y generación del conjunto de datos

Algunos de los pasos requeridos para una clasificación ya fueron explicados en tutoriales anteriores. Estos son:

1.  Cargar vector del área de estudio
2.  Seleccionar colección de imágenes y filtrar por fecha, nubes,
    bandas, etc.
3.  Reducir colección a imágen (e.g. calcular mediana para cada banda).

```javascript
// Mantengo codigos de Tutorial 1 Seccion 2
// 1. area de estudio (de sección anterior)
var geometry = ee.FeatureCollection('ft:1NOdzgdcCiWZ6YcoEharXG_IYmW03G-ZJeUSZtoOB');

// 2. Seleccionar colecciones
// Seleccionar producto. Indicar el ImageCollection ID

var producto = ee.ImageCollection('LANDSAT/LC8_L1T_TOA');

// Definir bandas a seleccionar
var bandas = ['B2', 'B3', 'B4', 'B5', 'B6', 'B7'];

// Filtrar colección
var coleccion1 = producto
    // por área de estudio. Debe estar cargada el área en este caso en la variable “geometry”
    .filterBounds(geometry)
    //por rango de fechas
    .filterDate('2016-08-01', '2016-10-31')
    // por cobertura de nubes máxima
    .filterMetadata('CLOUD_COVER','less_than', 40)
    // por bandas (definidas más arriba)
    .select(bandas);

// ver detalles de colección y filtros aplicados
print("Coleccion seleccionada", coleccion1);

// 3. Convertir a Imagen. Aplicar reducción de mediana
var stack1 = coleccion1.median();

// ver imagen en mapa:
Map.addLayer (stack1, {bands: ['B5', 'B4', 'B3'], min: [0,0,0], max: [0.6,0.6,0.6] }, "Landsat 8 B5-B4-B3" );

// centrar en área de estudio
Map.centerObject(geometry, 8);
```

4.  Cargar o generar puntos (o polígonos) de entrenamiento y validación

Para el punto 4 se pueden generar nuevos puntos, cargarlos a partir de un vector disponible previamente, o obtenerlo de puntos ya generados en la plataforma GEE. Para el último caso, se puede copiar como ejemplo el siguiente código:

```javascript
var clase0 = ee.FeatureCollection(
[ee.Feature(ee.Geometry.Point([-61.69303894042969,-28.33279078149813]),{"clase": 0,"system:index": "0"}),
ee.Feature(ee.Geometry.Point([-62.17506408691406, -28.375693762957862]),{"clase": 0,"system:index": "1"}),
ee.Feature(ee.Geometry.Point([-62.31170654296875,-28.40529274756662]),{"clase": 0,"system:index": "2"}),
ee.Feature(ee.Geometry.Point([-62.15240478515625, -28.4451476995102]),{"clase": 0,"system:index": "3"}),
ee.Feature(ee.Geometry.Point([-62.02537536621094, -28.463862268869118]),{"clase": 0,"system:index": "4"}),
ee.Feature(ee.Geometry.Point([-61.820068359375, -28.46688043752887]),{"clase": 0,"system:index": "5"}),
ee.Feature(ee.Geometry.Point([-61.82487487792969, -28.47774513090882]),{"clase": 0,"system:index": "6"}),
ee.Feature(ee.Geometry.Point([-61.80015563964844, -28.46989851998662]),{"clase": 0,"system:index": "7"}),
ee.Feature(ee.Geometry.Point([-61.73561096191406, -28.461447671879828]),{"clase": 0,"system:index": "8"}),
ee.Feature(ee.Geometry.Point([-62.307586669921875, -28.34487788226893]),{"clase": 0,"system:index": "9"})]),

clase1 = ee.FeatureCollection(
[ee.Feature(ee.Geometry.Point([-62.36457824707031, -28.355150836534136]),{"clase": 1,"system:index": "0"}),
ee.Feature(ee.Geometry.Point([-62.33848571777344, -28.349712337487777]),{"clase": 1,"system:index": "1"}),
ee.Feature(ee.Geometry.Point([-61.98829650878906, -28.25177175656493]),{"clase": 1,"system:index": "2"}),
ee.Feature(ee.Geometry.Point([-61.95808410644531, -28.249352330644772]),{"clase": 1,"system:index": "3"}),
ee.Feature(ee.Geometry.Point([-61.37306213378906, -28.30921698045776]),{"clase": 1,"system:index": "4"}),
ee.Feature(ee.Geometry.Point([-61.96495056152344, -28.298335001920627]),{"clase": 1,"system:index": "5"}),
ee.Feature(ee.Geometry.Point([-62.16339111328125, -28.28745191050374]),{"clase": 1,"system:index": "6"}),
ee.Feature(ee.Geometry.Point([-62.1990966796875, -28.281405267834156]),{"clase": 1,"system:index": "7"}),
ee.Feature(ee.Geometry.Point([-61.5234375, -28.430656725969925]),{"clase": 1,"system:index": "8"}),
ee.Feature(ee.Geometry.Point([-61.92237854003906, -28.288661197831743]),{"clase": 1,"system:index": "9"})]);
```

Ahora vamos a combinar las dos variables **clase0** y **clase1** en un único FeatureCollection:

```javascript
// Unir muestras por clase en un único FeatureCollection
var samples = clase0.merge( clase1 );

// ver caracteristicas de FeatureCollection
print ("muestras", samples);
```

## Clasificaciones Supervisadas

Las clasificaciones supervisadas se realizan cuando se tienen definidas las clases y se dispone  de información de casos correspondientes a esas clases (información de campo). Los distintos clasificadores se entrenan con la información de campo disponible a partir del comportamiento en las distintas bandas consideradas. La evaluación del resultado de una clasificación requiere disponer de datos independientes a los utilizados para el entrenamiento.  

### Separación en train/testing

Separación del conjunto de datos para entrenamiento y validación. La plataforma permite generar atributos (llamado “random” en este caso) con números al azar mediante la función randomColumn y agregarlos al FeatureCollection que contiene el set de datos. Genera valores entre 0 y 1. Esta requiere indicar el FeatureCollection y un valor inicial para generar los números al azar (“seed”):

```javascript
// Separacion de set de datos (polígonos) para entrenamiento y validación
// generación de atributo con números al azar (columna "random")
// para hacer muestreo

var seed = 2015;
samples = samples.randomColumn('random', seed);
```

Esto nos permitirá hacer un muestreo, seleccionando filas que contengan cierto rango de números generados al azar (Mayores o menores a cierto valor umbral). Para entrenar el algoritmo de clasificación se requiere extraer información de las imágenes para los polígonos seleccionados (similar a la etracción de información visto en secciones anteriores), incluyendo en las salidas los atributos clase y “random”:

```javascript
// extraccion de información incluyendo atributos clase y "random"
var set_datos = stack1.sampleRegions({
  collection: samples,
  properties: ['clase','random'],
  scale: 30
});
```
El resultado de la extracción es una Feature Collection que contiene información de cada banda y cada polígono:

![](images/clas_image2.png)

Posteriormente se subdivide el FeatureCollection con el set de datos en Entrenamiento (“training”) y Validación (“testing”). Se selecciona un umbral de separación de los valores al azar generados entre 0 y 1 (atributo “random”). En este caso, se seleccionan los valores mayores o iguales a 0.6 para entrenamiento y los menores a 0.6 para validación. Se pueden ver en consola los nuevos set de datos generados:

```javascript
// Separación entre Entrenamiento y validación. Identificar umbral de separación
var training = set_datos.filterMetadata('random', 'not_less_than', 0.6);
var testing = set_datos.filterMetadata('random', 'less_than', 0.6);

print ("Set de datos entrenamiento", training);
print ("Set de datos validación", testing);
```
Entrenamiento. Aquí debemos seleccionar el algoritmo de clasificación, el set de datos de entrenamiento (“training”), el atributo de separación en clases (“clase”) y las bandas seleccionadas. En este caso usamos el algoritmo Random Forest:

```javascript
// Entrenamiento
var bandas_sel = ['B7','B6','B5', 'B4', 'B3'];
var trained = ee.Classifier.randomForest().train(training, 'clase', bandas_sel);
```

Una vez entrenado el modelo, se lo aplica a una imagen y se genera la clasificación.

```javascript
// clasificación con el modelo entrenado
var classified = stack1.select(bandas).classify(trained).clip(geometry);
Map.addLayer(classified, {min:0, max:1, palette: ['339820', 'e6f0c2']}, 'Clasificacion');
print (classified);
```
La clasificación puede ser exportada:

```javascript
// Exportar imagen de clasificación
Export.image.toDrive({
  image:classified,
  description: 'clasificacion',
  scale: 30,
  region: geometry
});
```

Generación de Matriz de Confusión y resultados. La herramienta permite calcular la matriz de confusión, y estimar exactitud general, de usuario y de productor e indice Kappa.

```javascript
// Generación de matriz de confusión y resultados
var validation = testing.classify(trained);
var errorMatrix = validation.errorMatrix('clase', 'classification');

print('Matriz de Confusión:', errorMatrix);
print('Exactitud General:', errorMatrix.accuracy());
print('Indice Kappa:', errorMatrix.kappa());
print('Exactitudes de Usuario:', errorMatrix.consumersAccuracy());
print('Exactitudes de Productor:', errorMatrix.producersAccuracy());

// exportar matriz de confusion como csv
var err = ee.FeatureCollection( ee.Feature(null, {
  'matrix': errorMatrix.getInfo()
}));
print (err);

Export.table.toDrive({
    'collection': err,
    'description': 'CM_tutorial_2',
    'fileNamePrefix': 'CM_tutorial_2',
    'fileFormat': 'CSV'}
);

// convertir matriz de confusion a array
var cfmt2x2 =  errorMatrix.array().toList().map(
  function(item){
        item = ee.List(item);
        return ee.Feature(null, {Bosque: item.get(0), NoBosque: item.get(1)})

  });

print(cfmt2x2);
```


## Actividad Propuesta:

Modifique el script para que funcione con CART y almacene el árbol generado en una nueva variable utilizando la función explain. Luego
realice una nueva clasificación con el árbol ya generado y utilizando la funcionalidad provista por ee.Classifier.decisionTree en la región cubierta por el siguiente polígono:

```javascript
var nueva_zona = ee.Geometry.Rectangle([-64.456, -24.417, -61.369, -26.214]);
```

1. ¿Qué pasa si incremento el número de árboles de un RandomForest?
2. ¿Qué pasa si limito la profundidad en CART?
