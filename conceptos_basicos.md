# Conceptos Básicos de GEE

En este tutorial vamos a ver los primeros conceptos de GEE y su utilización desde el [Code Editor](https://code.earthengine.google.com/).
Lo que vamos a ver en primer lugar son algunos de los tipos de datos y los principios básicos del módulo **ee.algo**.

## Hola Mundo!!
Abrir el [https://code.earthengine.google.com](https://code.earthengine.google.com), preferentemente en Chrome ya que trabaja mejor que en otros navegadores.

Vamos a probar el primer ejemplo de programación en cualquier lenguaje: Hola Mundo!

Vamos a copiar y pegar los ejemplos desde aquí al [Code Editor](https://code.earthengine.google.com/).

```javascript
print('Hola Mundo!');
```

### Comentarios
En un lenguaje de programación se llama comentario al texto que no es interpretado por el interprete (valga la redundancia). Estos comentarios ayudan a documentar el código fuente.

```javascript
// Comenté el print usando la doble barra
//print('Hola Mundo!');
```

## Tipos de datos básicos de JavaScript

### Strings
Variables de tipo textos o cadenas de caracteres, se utilizan las comillas simple o dobles para definir una cadena.

```javascript
// Usamos comillas simples o dobles para crear un string
var saludoString = 'Hola Mundo!!';
print( saludoString );
```
**Importante:** Tenga en cuenta que las variables se definen con la palabra clave **var**

### Números
En las variables podemos almacenar números:

```javascript
// Asignamos un número a una variable
var numero = 38;
print('El valor es:', numero);
```

### Lists

Las listas las definimos con corchetes \[ \].

```javascript
// Lista de números
var listDeNumeros = [0, 1, 1, 2, 3, 5];
print('Lista de Números:', listDeNumeros);
```
Las listas también pueden almacenar strings u otros objetos.

```javascript
// Lista de strings.
var listDeStrings = ['a', 'b', 'c', 'd'];
print('Lista de strings:', listDeStrings);
```

### Objetos
En JavaScript los objetos son diccionarios de pares **clave: valor**. Para crear un objeto (o diccionario) se utilizan las llaves \{ \}.

```javascript
// Usamos las llaves {} para hacer un diccionario de pares clave:valor
var object = {
  foo: 'bar',
  baz: 38,
  stuff: ['Algo', 'eso', 'otro más']
};
print('Diccionario:', object);
// Para acceder al valor de un diccionario se utiliza la clave y los corchetes [ ].
print('Imprime la clave foo:', object['foo']);
// También se puede accider utilizando la notación con punto.
print('Print stuff:', object.stuff);
```

### Funciones

Las funciones son otra forma de mejorar la legibilidad y reutilización del código mediante el agrupamiento de conjuntos de operaciones.
- Defina una función con la palabra clave **function**.
- Los nombres de las funciones comienzan con una letra y tienen un par de paréntesis al final.
- Las funciones a menudo toman parámetros que le dicen a la función qué hacer. Estos parámetros van dentro de los paréntesis ().
- El conjunto de declaraciones que componen la función van dentro de las llaves \{ \}. La palabra clave **return** indica cuál es la salida de la función.

Hay varias maneras de declarar una función, pero aquí vamos a usar algo como esto:

```javascript
// Ejemplo de cómo se define una función
var funcion_saludo = function( nombre ) {
  var mensaje = '¡Hola ' + nombre + '! ¿Cómo estas?';
  return mensaje;
};

// Invocamos a la función con su parámetro.
print( funcion_saludo( 'Juan' ) );
```

## Objetos de GEE

Ahora vamos a aprende cómo colocar primitivas y objetos JavaScript en los contenedores de Earth Engine para enviarlos al servidor y procesarlos en Google.

### Strings

Para definir un string, tenemos que agregarla al contenedor **ee.String()** y el contenedor será enviado a Earth Engine. Veamos:

```javascript
// Definimos un string y lo agregamos al contenedor
var unString = 'en la nube';
var eeString = ee.String(unString);
print('Donde estabas? ', eeString);
```

Piense en **ee.algo** como un contenedor para una cosa que existe en el servidor.

```javascript
// Defino una cadena en el servidor.
var serverString = ee.String('Está en el server.');
print('¿Donde está el String?', serverString);
```

Aunque el primer argumento para **print()** es sólo una cadena en el cliente, el segundo argumento es realmente enviado al servidor para ser evaluado, y luego devuelto.

### Números
Utilice **ee.Number()** para crear un objeto número en el servidor. Por ejemplo, utilice el método JavaScript **Math.E**  para crear un valor de la constante en el servidor:

```javascript
// Definimos un número en el servidor
var serverNumber = ee.Number(Math.E);
print('e = ', serverNumber);
```
Los métodos **ee.String()** y **ee.Number()** son constructores. Un constructor tiene sus argumentos (y posiblemente otros parámetros) ponemos estos en un contenedor, y retornamos el contenedor y su contenido como un objeto EE que manipulamos en nuestro código. Un constructor comienza siempre con **ee** y retorna un objeto Earth Engine.

### Métodos en un objeto EE

Para operar con un objeto de EE vamos a utilizar los métodos que son provistos según la clase de objeto con la que trabajemos. En el caso anterior estábamos utilizando ee.Number(). ¿Cómo hacemos para calcular el logaritmo?

```javascript
// Utilice una función incorporada para realizar una operación en el número.
var logE = serverNumber.log();
print('log(e) = ', logE);
```
### Listas

Podemos hacer una lista de JavaScript como un objeto **ee.List** en el servidor, ponemos un literal de JavaScript en un contenedor como con números y cadenas.

```javascript
// Ejemplos de listas, secuencia por extención
var eeList = ee.List([1, 2, 3, 4, 5]);

// Secuencia usando el método del objeto list
var secuencia = ee.List.sequence(1, 5);
print('Secuencia:', secuencia);
```

Podemos recuperar un elemento de la lista con el operador **get**. Recuerde que esta lista solo existe en el servidor, **ee** nos permite acceder a esos objetos solo a través de sus métodos.

```javascript
// Usamos un método de ee.List para recuperar un valor
var valor = secuencia.get(2);
print('Valor en la posición 2:', valor);
```

### Casting (Casteo en criollo :D)
A veces, Earth Engine no sabe el tipo de objeto que es retornado por un método. Nosotros sabemos que la variable de **valor** en el ejemplo anterior es un objeto numérico. Pero si tratamos de sumarle 4 usando el método **add()** de un **ee.Number**, obtendrá un error así:

![](images/image_intro01.png)



## Bibliografía
-  Introduction to JavaScript for Earth Engine. https://developers.google.com/earth-engine/tutorial_js_01
-  Earth Engine Objects. https://developers.google.com/earth-engine/tutorial_js_02
