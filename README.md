# Objetos, clases e interfaces

Partiremos del siguiente código fuente:
```typescript
let rectangle = {
  name: "Rectangle",
  sides: 4,
};

let triangle = {
  name: "Triangle",
  sides: 3,
};

let hexagon = {
  name: "Hexagon",
  sides: 6,
};

const figures = [rectangle, triangle, hexagon];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name} and I have ${figure.sides} sides`);
});
```

## Objetos

Los objetos en Javascript representan un conjunto de propiedades y sus respectivos valores.
Se pueden crear de diferentes formas: 
  - Sintaxis literal
  - Constructores
  - Clases

Luego de crearlos se pueden modificar añadiendo o eliminando propiedades. 

Typescript, para gestionar el tipo de un objeto, se basa en la forma de dicho objeto, es decir, en la combinación particular de los nombre de las propiedades y sus respectivos tipos.

El código mostrado con anterioridad, si es compilado, teniendo habilitada la opción ```"declaration": true``` dentro de nuestro fichero tsconfig, generará un archivo dist/index.d.ts donde veremos lo siguiente: 

```typescript
declare let rectangle: {
    name: string;
    sides: number;
};
declare let triangle: {
    name: string;
    sides: number;
};
declare let hexagon: {
    name: string;
    sides: number;
};
declare const figures: {
    name: string;
    sides: number;
}[];

```

Como podemos observar, el compilador ha inferido correctamente los tipos de cada propiedad de los objetos. Además, como todos los objetos tienen la misma estructura, se ha inferido correctamente el tipo del array ```figures```.

Si añadieramos una nueva figura como la siguiente, el compilador emitirá un error debido a que el objeto pentagon no tiene definida una propiedad ```sides```. Pero ojo, no peta al crear al array, sino al intentar imprimir esa propiedad que no existe. 
```typescript
let pentagon = {
  name: "Pentagon",
};
```
Además, ahora el tipo del array será inferido como de tipo string, puesto que es la única propiedad en común de todos los objetos.
Esta inferencia se debe a que el compilador intenta evitar errores, por ello toma los tipos comunes de los objetos a almacenar en el array.

## Anotaciones de tipo en objetos

Cuanto utilizamos la sintaxis literal para definir un objeto, ya comprobamos que el compilador es capaz de inferir la forma del tipo de un objeto gracias a que se basa en los tipos de las propiedades y los valores literales asignados. Aunque hasta ahora no lo hemos hecho, se pueden tipar explícitamente estas propiedades para definir la forma del objeto:

```typescript
let rectangle = {
  name: "Rectangle",
  sides: 4,
};

let triangle = {
  name: "Triangle",
  sides: 3,
};

let hexagon = {
  name: "Hexagon",
  sides: 6,
};

let pentagon = {
  name: "Pentagon"
}

const figures: { name: string, sides: number} = [rectangle, triangle, hexagon, pentagon];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name} and I have ${figure.sides} sides`);
});
```

Como ahora si hemos tipado el array, se nos mostrará un error del compilador porque hay uno de los objetos que no contiene la forma esperada.

Todos los objetos deben tener todas las propiedades indicadas en el tipo del array para que el compilador no de un error, y si tienen propiedades de más, no se emitirán errores.

Por lo tanto si pentagon tuviera la siguiente forma, el compilador no emitiría ningún error: 

```typescript
let pentagon = {
  name: "Pentagon",
  sides: 5,
  color: blue
}
```

## Propiedades opcionales

Al igual que cuando vimos los parámetros opcionales, typescript nos permite indicar que alguna propiedad concreta de un objeto sea opcional.

```typescript
let rectangle = {
  name: "Rectangle",
  sides: 4,
};

let triangle = {
  name: "Triangle",
  sides: 3,
};

let hexagon = {
  name: "Hexagon",
  sides: 6,
};

let pentagon = {
  name: "Pentagon",
  sides: 5,
  color: "blue",
};

// Como podemos ver, hemos indicado que la propiedad color sea opcional, es decir, si alguno de los objetos del array no tiene esa propiedad, no se emitiría error.

const figures: { name: string, sides: number, color?: string }[] =
  [rectangle, triangle, hexagon, pentagon];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides and I am ${figure.color}`);
});
```

La salida del programa sería la siguiente: 

```typescript
I am a Rectangle, I have 4 sides and I am undefined
I am a Triangle, I have 3 sides and I am undefined
I am a Hexagon, I have 6 sides and I am undefined
I am a Pentagon, I have 5 sides and I am blue
```

## Definición de métodos en objetos

Además de propiedades, un objeto puede contener métodos.

```typescript
let rectangle = {
  name: "Rectangle",
  sides: 4,
  area: (base, height) => base * height,
};

let triangle = {
  name: "Triangle",
  sides: 3,
  area: (base, height) => (base * height) / 2,
};

let circle = {
  name: "Circle",
  area: (radius) => Math.PI * Math.pow(radius, 2),
};

const figures: {
  name: string,
  sides?: number,
  area(...params: number[]): number
}[] = [rectangle, triangle, circle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides and my area is ${figure.area(3, 5)}`);
});
```

En este caso concreto hemos definido un método área para cada una de las figuras, donde ese método recibe uno o varios numeros y devuelve un numero como resultado. 

Si ese método lo definiésemos como opcional, y alguno de los objetos no lo tuviera, se produciría un error pero porque se invoca al método dentro del forEach.

```typescript
let rectangle = {
  name: "Rectangle",
  sides: 4,
  area: (base, height) => base * height,
};

let triangle = {
  name: "Triangle",
  sides: 3,
  area: (base, height) => (base * height) / 2,
};

let circle = {
  name: "Circle",
  area: (radius) => Math.PI * Math.pow(radius, 2),
};

let pentagon = {
  name: "Pentagon",
  sides: 5,
};

const figures: {
  name: string,
  sides?: number,
  area?(...params: number[]): number
}[] = [rectangle, triangle, circle, pentagon];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides and my area is ${figure.area(3, 5)}`);
});
```

Si activamos la función ```"strictNullChecks": true``` en nuestro tsconfig nos dará un error el compilador en casos como este, donde hemos definido un método como opcional y se intenta invocar desde un objeto que no tiene definido dicho método, con lo cual podría ser undefined.

## Alias de tipos

También se pueden utilizar alias de tipos para definir la forma de un objeto: 

```typescript
type Figure = {
  name: string,
  sides?: number,
  area(...params: number[]): number
};

let rectangle: Figure = {
  name: "Rectangle",
  sides: 4,
  area: (base, height) => base * height,
};

let triangle = {
  name: "Triangle",
  sides: 3,
  area: (base, height) => (base * height) / 2,
};

let circle = {
  name: "Circle",
  area: (radius) => Math.PI * Math.pow(radius, 2),
};

const figures: Figure[] = [rectangle, triangle, circle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides and my area is ${figure.area(3, 5).toFixed(2)}`);
});
```

En este caso hemos definido el tipo ```Figure```, el cual usamos para tipar algunos objetos y el array. Aún así, aunque algunos objetos no se hayan definido con el alias de tipo nuevo, pueden estar en el array de figuras sin que se emita ningún error puesto que tienen la misma forma.

Lo que si daría un error es si tipamos un objeto con nuestro alias de tipo e intentamos ponerle alguna propiedad de más al objeto.

```typescript
type Figure = {
  name: string,
  sides?: number,
  area(...params: number[]): number
};

let rectangle: Figure = {
  name: "Rectangle",
  sides: 4,
  area: (base, height) => base * height,
  color: "blue",
};
```

Nos dirá que la propiedad color no existe en el tipo de dato ```Figure```.

Para esto, como siempre, hay una opción de typescript que podemos activar de la siguiente forma ```"suppressExcessPropertyErrors": true```, y podríamos evitarnos dicho error.


## Uniones de tipo en objetos
```typescript
type TwoDimensionalFigure = {
  name: string,
  sides?: number,
  area(...params: number[]): number
};

type ThreeDimensionalFigure = {
  name: string,
  volume(...params: number[]): number
}

let rectangle: TwoDimensionalFigure = {
  name: "Rectangle",
  sides: 4,
  area: (base, height) => base * height,
};

let cube: ThreeDimensionalFigure = {
  name: "Cube",
  volume: (base, height, depth) => base * height * depth,
};

const figures: (TwoDimensionalFigure | ThreeDimensionalFigure)[] =
  [rectangle, cube];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}`);
});
```

En este caso hemos definido dos alias de tipo, uno para figuras de dos dimensiones y otro para las de tres dimensiones.

Teniendo esto en cuenta deberemos saber que la única propiedad común a ambos tipos y la única accesible es la propiedad ```name```, y por lo tanto, si accedemos a cualquier otra propiedad se nos informará de un error. 

Por último, cabe mencionar que, cuando se define una unión de tipos que involucra diferentes formas de tipos de objetos, el tipo de cada propiedad de la unión de tipos, esto es, solo las comunes, es también una unión de tipos.

## Uso de guardianes de tipos

En algunos de los guardianes de tipos utilizados con anterioridad, hacíamos uso de ```typeof``` para comprobar los tipos de datos.
Sin embargo, cuando trabajamos con guardianes de tipos en objetos, esto no nos sirve, dado que siempre nos devolverá ```object```.

Teniendo en cuenta lo anterior, se puede aplicar in en lugar de typeof para establecer guardianes de tipos con objetos, lo que permite diferenciar formas diferentes de tipos de objetos llevando a cabo comprobaciones sobre la existencia de propiedades concretas en los mismos:

```typescript
type TwoDimensionalFigure = {
  name: string,
  sides?: number,
  area(...params: number[]): number
};

type ThreeDimensionalFigure = {
  name: string,
  volume(...params: number[]): number
}

let rectangle: TwoDimensionalFigure = {
  name: "Rectangle",
  sides: 4,
  area: (base, height) => base * height,
};

let cube: ThreeDimensionalFigure = {
  name: "Cube",
  volume: (base, height, depth) => base * height * depth,
};

const figures: (TwoDimensionalFigure | ThreeDimensionalFigure)[] =
  [rectangle, cube];

// Como podemos ver en el forEach, hacemos uso de 'in' para ver si la propiedad a usar pertenece al objeto que se está utilizando en ese momento

figures.forEach((figure) => {
  if ("area" in figure) {
    console.log(`I am a ${figure.name} and my area is ${figure.area(5, 4).toFixed(2)}`);
  } else if ("volume" in figure) {
    console.log(`I am a ${figure.name} and my volume is ${figure.volume(5, 4, 7).toFixed(2)}`);
  }
});
```

Por último, cabe mencionar que, como método alternativo al uso de in, se puede utilizar una función para establecer un guardián de tipos con objetos:

```typescript
type TwoDimensionalFigure = {
  name: string,
  sides?: number,
  area(...params: number[]): number
};

type ThreeDimensionalFigure = {
  name: string,
  volume(...params: number[]): number
}

let rectangle: TwoDimensionalFigure = {
  name: "Rectangle",
  sides: 4,
  area: (base, height) => base * height,
};

let cube: ThreeDimensionalFigure = {
  name: "Cube",
  volume: (base, height, depth) => base * height * depth,
};

const figures: (TwoDimensionalFigure | ThreeDimensionalFigure)[] =
  [rectangle, cube];

// En esta función hacemos uso de 'is', y el resultado se basa en una evaluación de un predicado de tipos. Por lo tanto, podemos establecer el tipo que la función pretende comprobar. Si el resultado de la invocación a la función con el parámetro myObj resulta ser true, entonces el compilador tratará a myObj como una figura de dos dimensiones

function isTwoDimensionalFigure(myObj: any): myObj is TwoDimensionalFigure {
  return myObj.area !== undefined;
}

// Esta función es igual pero aplicada a figuras de tres dimensiones
function isThreeDimensionalFigure(myObj: any): myObj is ThreeDimensionalFigure {
  return myObj.volume !== undefined;
}

figures.forEach((figure) => {
  if (isTwoDimensionalFigure(figure)) {
    console.log(`I am a ${figure.name} and my area is ${figure.area(5, 4).toFixed(2)}`);
  } else if (isThreeDimensionalFigure(figure)) {
    console.log(`I am a ${figure.name} and my volume is ${figure.volume(5, 4, 7).toFixed(2)}`);
  }
});
```

## Intersecciones de tipos

A diferencia de una unión de tipos, donde solo son accesibles las características comunes a todos los tipos que forman parte de la unión, en una intersección de tipos son accesibles todas las características de los tipos que forman parte de la intersección, ya sean comunes o no comunes:

```typescript
type TwoDimensionalFigure = {
  name: string,
  area(...params: number[]): number
};

type TwoDimensionalFigureWithSides = {
  name: string,
  sides: number,
};

const rectangle = {
  name: "Rectangle",
  sides: 4,
  area: (base, height) => base * height,
};

const triangle = {
  name: "Triangle",
  sides: 3,
  area: (base, height) => base * height / 2,
};

const figures: (TwoDimensionalFigure & TwoDimensionalFigureWithSides)[] =
  [rectangle, triangle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides and my area is ${figure.area(5, 4).toFixed(2)}`);
});
```

En este caso todo funciona bien, pero si en alguna de las figuras no se definiera alguna de las propiedades el compilador informaría de un error.

```typescript
const circle = {
  name: "Circle",
  area: (radius) => Math.PI * Math.pow(radius, 2),
};
```

En este caso el objeto circle no contiene la propiedad sides, por lo que el compilador informará del error debido a que todos los objetos del array deberán tener todas las propiedades de la intersección de tipos.

Aquí pongo algunas propiedades con las que hay que tener cuidado a la hora de crear intersecciones de tipos.

Existen tres casos a diferenciar:

  - **Propiedades con el mismo nombre y tipo.** En realidad, no implica problema alguno. Un ejemplo es la propiedad name de tipo string en el ejemplo anterior.
  - **Propiedades con el mismo nombre y tipo diferente.** Se mantiene el nombre de la propiedad, pero se intersecta el tipo. De este modo, si tenemos una propiedad name de tipo string en un objeto y una propiedad name de tipo number en el otro objeto, la propiedad name de la intersección será de tipo string & number, lo que resulta en lo que se denomina un tipo imposible, es decir, un tipo al que no se le pueden asignar valores. Lo anterior siempre sucede que se intersectan tipos básicos como string o number. Para solucionar este tipo de situaciones bastaría con cambiar el nombre de la propiedad name en los diferentes objetos, o hacer que dicha propiedad pase a ser un objeto en sí misma en todos los objetos.
  - **Métodos con el mismo nombre.** Se mantiene el nombre del método y se crea una función cuya signatura es una intersección de las signaturas de los métodos definidos en cada objeto. Es difícil predecir el comportamiento de un método intersectado, pero suele ser similar al de un método cuyos tipos han sido sobrecargados. Por último, la implementación de un método intersectado debe preservar la compatibilidad con los métodos definidos en cada objeto que forma parte de la intersección. Para ello, los parámetros de una función intersectada suelen definirse mediante uniones de tipos y el valor de retorno suele tomar el tipo de datos any. Además, se suelen utilizar guardianes de tipos en el cuerpo de la función intersectada para establecer las relaciones entre los tipos de los parámetros y los tipos del resultado.

## Clases

