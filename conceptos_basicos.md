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

Las listas las definimos con corchetes \[\].

```javascript
// Lista de números
var listDeNumeros = [0, 1, 1, 2, 3, 5];
print('Lista de Números:', listDeNumeros);
```
Las listas también pueden almacenar strings u otros objetos.

```javascript
// Make a list of strings.
var listDeStrings = ['a', 'b', 'c', 'd'];
print('Lista de strings:', listDeStrings);
```

## Objetos de ee.




## Tipos de datos
