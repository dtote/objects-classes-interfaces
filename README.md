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

const figures: { name: string, sides: number}[] = [rectangle, triangle, hexagon, pentagon];

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

Como ya comentamos con anterioridad, los objetos aparte de con sintaxis literal, también se pueden crear mediante el uso de constructores o clases.

En este caso, veremos como definir un constructor denominado ```TwoDimensionalFigure```, el cual nos ayudará a crear objetos:

```typescript
let TwoDimensionalFigure = function(name: string, sides: number,
    area: (...params: number[]) => number) {
  this.name = name;
  this.sides = sides;
  this.area = area;
};

let rectangle =
  new TwoDimensionalFigure("Rectangle", 4, (base, height) => base * height);

let triangle =
  new TwoDimensionalFigure("Triangle", 3, (base, height) => base * height / 2);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides and my area is ${figure.area(5, 4).toFixed(2)}`);
});
```

Este ejemplo nos daría problemas si tuvieramos activada la opción del compilador ```noImplicitAny```. Esto se debe a que al usar el new, el compilador infiere de manera implícita que los objetos creados y asignados a las variables son de tipo any. 
De esto deducimos que el uso de constructores no es una muy buena práctica para construir objetos.

Ahora veremos un ejemplo para crear objetos como los del anterior ejemplo pero con una clase: 

```typescript
class TwoDimensionalFigure {
  name: string;
  sides: number;
  area: (...params: number[]) => number;

  constructor(name: string, sides: number,
      area: (...params: number[]) => number) {
    this.name = name;
    this.sides = sides;
    this.area = area;
  }
}

let rectangle =
  new TwoDimensionalFigure("Rectangle", 4, (base, height) => base * height);

let triangle =
  new TwoDimensionalFigure("Triangle", 3, (base, height) => base * height / 2);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides and my area is ${figure.area(5, 4).toFixed(2)}`);
});
```

El equivalente a la clase creada pero en Javascript sería la siguiente: 

```typescript
class TwoDimensionalFigure {
    constructor(name, sides, area) {
        this.name = name;
        this.sides = sides;
        this.area = area;
    }
}
```

## Tipos de acceso a las propiedades

TypeScript soporta el uso de las siguientes palabras clave para definir el nivel de acceso a una propiedad de la clase:

- **public**: Se puede acceder libremente a la propiedad o método de la clase. Es el acceso por defecto.
- **private**: Solo se puede acceder desde la clase donde se define la propiedad o método.
- **protected**: Solo se puede acceder desde la clase donde se define la propiedad o método o desde clases hijas.
El siguiente ejemplo ilustra lo anterior:

```typescript
class TwoDimensionalFigure {
  public name: string;
  private sides: number;
  public area: (...params: number[]) => number;

  constructor(name: string, sides: number,
      area: (...params: number[]) => number) {
    this.name = name;
    this.sides = sides;
    this.area = area;
  }

  getNumberOfSides() {
    return this.sides;
  }
}

let rectangle =
  new TwoDimensionalFigure("Rectangle", 4, (base, height) => base * height);

let triangle =
  new TwoDimensionalFigure("Triangle", 3, (base, height) => base * height / 2);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides ` +
              `and my area is ${figure.area(5, 4).toFixed(2)}`);
});
```

La propiedad sides es privada, por lo tanto dará un error, y la unica manera de accederla es a través de la función getNumberOfSides, el cual es público por defecto.

Si miramos el codigo correspondiente a esto en Javascript, sería algo como lo siguiente: 

```typescript
class TwoDimensionalFigure {
    constructor(name, sides, area) {
        this.name = name;
        this.sides = sides;
        this.area = area;
    }
    getNumberOfSides() {
        return this.sides;
    }
}
```

Cabe mencionar en este punto que una buena práctica consiste en tener inicializadas todas las propiedades definidas en una clase, por lo que se recomienda habilitar la opción strictPropertyInitialization del compilador de TypeScript, la cual solo tiene efecto si también está habilitada la opción strictNullChecks. De este modo, en el caso de que lo anterior suceda, el compilador informará de los errores correspondientes.

## Propiedades de solo lectura

Además de poder definir el nivel de acceso de una propiedad tal y como se ha descrito anteriormente, una propiedad puede marcarse como de solo lectura mediante readonly:

```typescript
class TwoDimensionalFigure {
  public readonly name: string;
  private sides: number;
  public area: (...params: number[]) => number;

  constructor(name: string, sides: number,
      area: (...params: number[]) => number) {
    this.name = name;
    this.sides = sides;
    this.area = area;
  }

  getNumberOfSides() {
    return this.sides;
  }
}

let rectangle =
  new TwoDimensionalFigure("Rectangle", 4, (base, height) => base * height);

let triangle =
  new TwoDimensionalFigure("Triangle", 3, (base, height) => base * height / 2);

let figures = [rectangle, triangle];

rectangle.name = "circle";

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.getNumberOfSides()} sides ` +
              `and my area is ${figure.area(5, 4).toFixed(2)}`);
});
```

Si intentamos compilar este código da error, porque name está definido como solo de lectura y se le intenta asignar un valor.
El constructor de una clase es el único capaz de inicializar una propiedad definida como de solo lectura.

## Constructores simplificados

La definición de un constructor de una clase puede simplificarse, siempre y cuando deseemos que nuestra clase contenga las mismas propiedades que las definidas como parámetros del constructor. Para utilizar esa sintaxis simplificada, lo único que hay que hacer es utilizar las palabras reservadas vistas en secciones anteriores a la hora de definir los parámetros del constructor:

```typescript
class TwoDimensionalFigure {
  constructor(public readonly name: string, private sides: number,
      public area: (...params: number[]) => number) {
  }

  getNumberOfSides() {
    return this.sides;
  }
}

let rectangle =
  new TwoDimensionalFigure("Rectangle", 4, (base, height) => base * height);

let triangle =
  new TwoDimensionalFigure("Triangle", 3, (base, height) => base * height / 2);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.getNumberOfSides()} sides ` +
              `and my area is ${figure.area(5, 4).toFixed(2)}`);
});
```

## Herencia de clases 

Typescript permite la herencia de clases. Aunque aquí definimos varias clases en el mismo fichero, es recomendable poner un fichero por cada clase.

```typescript
class TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
  }
}

class Rectangle extends TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
    super(name, sides, color);
  }

  getArea(base: number, height: number) {
    return base * height;
  }
}

class Triangle extends TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
    super(name, sides, color);
  }

  getArea(base: number, height: number) {
    return base * height / 2;
  }
}

let rectangle =
  new Rectangle("RedRectangle", 4, "red");

let triangle =
  new Triangle("BlueTriangle", 3, "blue");

// El tipo inferido es (Triangle | Rectangle)[]
let figures = [rectangle, triangle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides ` +
              `and my area is ${figure.getArea(5, 4).toFixed(2)}`);
});
```

Un ejemplo diferente pero asignando al array ```figures``` un objeto de la clase ```TwoDimensionalFigure```:

```typescript
class TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
  }
}

class Rectangle extends TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
    super(name, sides, color);
  }

  getArea(base: number, height: number) {
    return base * height;
  }
}

class Triangle extends TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
    super(name, sides, color);
  }

  getArea(base: number, height: number) {
    return base * height / 2;
  }
}

let rectangle =
  new Rectangle("RedRectangle", 4, "red");

let triangle =
  new Triangle("BlueTriangle", 3, "blue");

let twoDimensionalFigure =
  new TwoDimensionalFigure("anyBlackFigure", 8, "black");

// El tipo inferido es (twoDimensionalFigure | Rectangle | Triangle)[] pero como las propiedades de una unión de tipos vienen dadas por las propiedades comunes a todos los tipos de la unión, esas propiedades, precisamente, son las de TwoDimensionalFigure. Es por ello que el tipo inferido en este caso particular es TwoDimensionalFigure[].
let figures = [twoDimensionalFigure, rectangle, triangle];

figures.forEach((figure) => {
  if (figure instanceof Rectangle || figure instanceof Triangle) {
    console.log(`I am a ${figure.name}, I have ${figure.sides} sides ` +
              `and my area is ${figure.getArea(5, 4).toFixed(2)}`);
  }
});
```

Si nos fijamos en el ejemplo también hemos tenido que incluir un guardián de tipos, a través del uso de ```instanceof```, para poder deshacer la unión de tipos e invocar al método ```getArea```, el cual sí está presente en las clases hijas. (Rectangle | Triangle) es el tipo ingeido para la variable figure dentro de la sentencia ```if```.

## Clases Abstractas 

Una clase abstracta en TypeScript, al igual que sucede en otros lenguajes, permite modelar situaciones en las que se requiere de un conjunto de características comunes que deben ser implementadas por las clases que heredan de dicha clase abstracta.

```typescript
abstract class TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
  }

  abstract getArea(...params: number[]): number;
}

class Rectangle extends TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
    super(name, sides, color);
  }

  /* getArea(base: number, height: number) {
    return base * height;
  } */
}

class Triangle extends TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
    super(name, sides, color);
  }

  getArea(base: number, height: number) {
    return base * height / 2;
  }
}

let rectangle =
  new Rectangle("RedRectangle", 4, "red");

let triangle =
  new Triangle("BlueTriangle", 3, "blue");

let twoDimensionalFigure =
  new TwoDimensionalFigure("anyBlackFigure", 8, "black");

let figures = [twoDimensionalFigure, rectangle, triangle];

figures.forEach((figure) => {
  if (figure instanceof Rectangle || figure instanceof Triangle) {
    console.log(`I am a ${figure.name}, I have ${figure.sides} sides ` +
              `and my area is ${figure.getArea(5, 4).toFixed(2)}`);
  }
});
```

Como hemos comentado la definición del método ```getArea``` y este debe ser implementado por todas las subclases de la clase abstracta, pues nos dará un error.
También hay otro error debido a que estamos intentando instanciar una clase abstracta. Esto se puede solucionar descomentando el método, quitando la instancia de la clase abstracta y además podríamos tratar de tipar el array de ```figures``` con ```TwoDimensionalFigure```.

Con esto último conseguimos que no sea necesario el guardián de tipos utilizado en ejemplos anteriores al invocar al método del area: 

```typescript
abstract class TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
  }

  abstract getArea(...params: number[]): number;
}

class Rectangle extends TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
    super(name, sides, color);
  }

  getArea(base: number, height: number) {
    return base * height;
  }
}

class Triangle extends TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string) {
    super(name, sides, color);
  }

  getArea(base: number, height: number) {
    return base * height / 2;
  }
}

let rectangle =
  new Rectangle("RedRectangle", 4, "red");

let triangle =
  new Triangle("BlueTriangle", 3, "blue");

let figures: TwoDimensionalFigure[] = [rectangle, triangle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides ` +
              `and my area is ${figure.getArea(5, 4).toFixed(2)}`);
});
```

## Interfaces 

En TypeScript, una interfaz permite describir la forma de un objeto. La forma de los objetos instanciados a partir de una clase que implemente dicha interfaz deberá coincidir con la forma especificada mediante la interfaz. Una interfaz tiene el mismo propósito que las formas de tipos vistas al principio de este capítulo.

```typescript
interface TwoDimensionalFigure {
  name: string;
  sides: number;
  color: string;
  getArea(): number;
}

class Rectangle implements TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height;
  }
}

class Triangle implements TwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height / 2;
  }
}

let rectangle =
  new Rectangle("RedRectangle", 4, "red", 10, 5);

let triangle =
  new Triangle("BlueTriangle", 3, "blue", 5, 9);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  console.log(`I am a ${figure.name}, I have ${figure.sides} sides ` +
              `and my area is ${figure.getArea().toFixed(2)}`);
});
```
Ahora, TwoDimensionalFigure es una interfaz. Las clases que implementan (implements) dicha interfaz, que son Rectangle y Triangle, deben definir todas y cada una de las propiedades y métodos definidos en la interfaz.

Por ejemplo, si eliminamos la definición del parámetro color del constructor de la clase Triangle recibiremos un error, puesto que Triangle tiene que contener todo lo que se haya indicado en la interfaz.

## Implementación de varias interfaces  

Una clase puede implementar varias interfaces, como en el ejemplo que sigue, en el que se ha declarado una segunda interfaz Printable que cuenta con un método print, el cual también deben implementar, de manera obligatoria, las clases Rectangle y Triangle:

```typescript
interface TwoDimensionalFigure {
  name: string;
  sides: number;
  color: string;
  getArea(): number;
}

interface Printable {
  print(): void;
}

class Rectangle implements TwoDimensionalFigure, Printable {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height;
  }

  print() {
    console.log(`I am a ${this.name}, I have ${this.sides} sides ` +
              `and my area is ${this.getArea().toFixed(2)}`);
  }
}

class Triangle implements TwoDimensionalFigure, Printable {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height / 2;
  }

  print() {
    console.log(`I am a ${this.name}, I have ${this.sides} sides ` +
              `and my area is ${this.getArea().toFixed(2)}`);
  }
}

let rectangle =
  new Rectangle("RedRectangle", 4, "red", 10, 5);

let triangle =
  new Triangle("BlueTriangle", 3, "blue", 5, 9);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  figure.print();
});
```

## Herencia de interfaces 

En TypeScript, las interfaces se pueden extender, haciendo uso de la palabra reservada extends, tal y como se puede hacer con las clases. El resultado es una interfaz con las propiedades y métodos de las interfaces de las que hereda, junto con las nuevas propiedades y métodos definidos en la propia interfaz:

```typescript
interface TwoDimensionalFigure {
  name: string;
  sides: number;
  color: string;
  getArea(): number;
}

interface PrintableTwoDimensionalFigure extends TwoDimensionalFigure {
  print(): void;
}

class Rectangle implements PrintableTwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height;
  }

  print() {
    console.log(`I am a ${this.name}, I have ${this.sides} sides ` +
              `and my area is ${this.getArea().toFixed(2)}`);
  }
}

class Triangle implements PrintableTwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height / 2;
  }

  print() {
    console.log(`I am a ${this.name}, I have ${this.sides} sides ` +
              `and my area is ${this.getArea().toFixed(2)}`);
  }
}

let rectangle =
  new Rectangle("RedRectangle", 4, "red", 10, 5);

let triangle =
  new Triangle("BlueTriangle", 3, "blue", 5, 9);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  figure.print();
});
```

## Definición de propiedades y métodos opcionales en interfaces 

En TypeScript, las interfaces pueden definir propiedades o métodos opcionales, como en el caso del siguiente ejemplo, donde la propiedad color y el método getArea de la interfaz TwoDimensionalFigure han sido definidos como opcionales:

```typescript
interface TwoDimensionalFigure {
  name: string;
  sides: number;
  color?: string;
  getArea?(): number;
}

interface PrintableTwoDimensionalFigure extends TwoDimensionalFigure {
  print(): void;
}

class Rectangle implements PrintableTwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public base: number, public height: number) {
  }

  print() {
    console.log(`I am a ${this.name} and I have ${this.sides} sides`);
  }
}

class Triangle implements PrintableTwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height / 2;
  }

  print() {
    console.log(`I am a ${this.name}, I have ${this.sides} sides ` +
              `and my area is ${this.getArea().toFixed(2)}`);
  }
}

let rectangle =
  new Rectangle("Rectangle", 4, 10, 5);

let triangle =
  new Triangle("BlueTriangle", 3, "blue", 5, 9);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  figure.print();
});
```
En este caso no es obligado implementar la propiedad ```color``` y el metodo ```getArea```.


## Implementación de una interfaz abstracta

En TypeScript, las clases abstractas se pueden utilizar para implementar algunas o todas las propiedades incluidas en una interfaz.

En el ejemplo que sigue, todas las propiedades y métodos de la interfaz TwoDimensionalFigure se han declarado como abstractas en la clase abstracta PrintableRedTwoDimensionalFigure, a excepción de la propiedad color a la cual se le asigna la cadena de caracteres "red". Es por ello, que las clases Rectangle y Triangle, las cuales heredan de la clase PrintableRedTwoDimensionalFigure, no requieren de la definición de la propiedad color en sus constructores, dado que la heredan. De lo que si requieren las subclases es de la definición del resto de propiedades y métodos definidos como abstractos en la superclase:

```typescript
interface TwoDimensionalFigure {
  name: string;
  sides: number;
  color: string;
  getArea(): number;
}

abstract class PrintableRedTwoDimensionalFigure implements TwoDimensionalFigure {
  abstract name: string;
  abstract sides: number;
  abstract getArea(): number;
  abstract print(): void;
  color = "red";
}

class RedRectangle extends PrintableRedTwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public base: number, public height: number) {
    super();
  }

  getArea() {
    return this.base * this.height;
  }

  print() {
    console.log(`I am a ${this.color} ${this.name}, ` +
                `I have ${this.sides} sides, ` +
                `and my area is ${this.getArea().toFixed(2)}`);
  }
}

class RedTriangle extends PrintableRedTwoDimensionalFigure {
  constructor(public readonly name: string, public readonly sides: number,
    public base: number, public height: number) {
    super();
  }

  getArea() {
    return this.base * this.height / 2;
  }

  print() {
    console.log(`I am a ${this.color} ${this.name}, ` +
                `I have ${this.sides} sides, ` +
                `and my area is ${this.getArea().toFixed(2)}`);
  }
}

let rectangle =
  new RedRectangle("rectangle", 4, 10, 5);

let triangle =
  new RedTriangle("triangle", 3, 5, 9);

let figures = [rectangle, triangle];

figures.forEach((figure) => {
  figure.print();
});
```

## Guardianes de tipo e interfaces 

TypeScript permite definir uniones de tipos en las que intervienen interfaces. Para tratar de deshacer la unión de tipos a favor de una interfaz, no se puede establecer un guardián de tipos que utilice typeof o instanceof y, en su lugar, se debe establecer un guardián de tipos que utilice in, con el objetivo de averiguar si la interfaz en cuestión define una propiedad o conjunto de propiedades concretas.

Debemos recordar que una interfaz es equivalente a la forma del tipo de un objeto, por lo que el comportamiento de los guardianes de tipo en ambos casos será equivalente también. Un ejemplo que ilustra lo anterior es el siguiente:

```typescript
interface TwoDimensionalFigure {
  name: string;
  sides: number;
  color: string;
  getArea(): number;
  print(): void;
}

interface ThreeDimensionalFigure {
  name: string;
  faces: number;
  color: string;
  getVolume(): number;
  print(): void;
}

class Rectangle implements TwoDimensionalFigure {
  constructor(public name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height;
  }

  print() {
    console.log(`I am a ${this.color} ${this.name}, ` +
                `I have ${this.sides} sides, ` +
                `and my area is ${this.getArea().toFixed(2)}`);
  }
}

class Cube implements ThreeDimensionalFigure {
  constructor(public name: string, public readonly faces: number,
    public color: string, public base: number,
    public height: number, public depth: number) {
  }

  getVolume() {
    return this.base * this.height * this.depth;
  }

  print() {
    console.log(`I am a ${this.color} ${this.name}, ` +
                `I have ${this.faces} sides, ` +
                `and my area is ${this.getVolume().toFixed(2)}`);
  }
}

let rectangle =
  new Rectangle('rectangle', 4, 'red', 10, 5);

let cube =
  new Cube('cube', 6, 'blue', 5, 9, 14);

let figures: (TwoDimensionalFigure | ThreeDimensionalFigure)[] =
  [rectangle, cube];

figures.forEach((figure) => {
  if ('getArea' in figure) {
    console.log(`Figure: ${figure.name}; Area: ${figure.getArea()}`);
  } else if ('getVolume' in figure) {
    console.log(`Figure: ${figure.name}; Volume: ${figure.getVolume()}`);
  }
});
```

Se han definido dos interfaces TwoDimensionalFigure y ThreeDimensionalFigure, además de las clases Rectangle y Cube que implementan, respectivamente, ambas interfaces. Ahora, el array figures es de tipo (TwoDimensionalFigure | ThreeDimensionalFigure)[], es decir, el tipo es una unión de tipos en la que intervienen interfaces, y no clases. En el caso de querer deshacer la unión de tipos para poder invocar a los métodos que son particulares de cada interfaz, lo que se debe hacer es utilizar in en el guardián de tipos establecido al final del ejemplo.

## Creación dinámica de propiedades 

El compilador de TypeScript solo permite asignar valores a aquellas propiedades definidas en la forma del tipo de un objeto, lo que implica que las clases e interfaces deben definir todas las propiedades y métodos requeridos. Por el contrario, JavaScript permite ampliar un objeto con nuevas propiedades, simplemente, asignando un valor a una propiedad cuyo nombre no se haya usado aún.

En TypeScript se puede utilizar una signatura indexada (index signature) para definir propiedades dinámicamente, al mismo tiempo que se preserva la seguridad de la comprobación de los tipos de datos:

```typescript
interface TwoDimensionalFigure {
  name: string;
  sides?: number;
  color: string;
  getArea(): number;
  print(): void;
}

class Rectangle implements TwoDimensionalFigure {
  constructor(public name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height;
  }

  print() {
    console.log(`I am a ${this.color} ${this.name}, ` +
                `I have ${this.sides} sides, ` +
                `and my area is ${this.getArea().toFixed(2)}`);
  }
}

class Triangle implements TwoDimensionalFigure {
  constructor(public name: string, public readonly sides: number,
    public color: string, public base: number, public height: number) {
  }

  getArea() {
    return this.base * this.height / 2;
  }

  print() {
    console.log(`I am a ${this.color} ${this.name}, ` +
                `I have ${this.sides} sides, ` +
                `and my area is ${this.getArea().toFixed(2)}`);
  }
}

class Circle implements TwoDimensionalFigure {
  constructor(public name: string, public color: string,
    public radius: number) {
  }

  getArea() {
    return Math.PI * Math.pow(this.radius, 2);
  }

  print() {
    console.log(`I am a ${this.color} ${this.name}, ` +
                `and my area is ${this.getArea().toFixed(2)}`);
  }
}

class TwoDimensionalFiguresSet {
  constructor(...twoDimensionalFigures: [string, TwoDimensionalFigure][]) {
    twoDimensionalFigures.forEach((figure) => {
      this[figure[0]] = figure[1];
    });
  }

  [propertyName: string]: TwoDimensionalFigure;
}

let figures = new TwoDimensionalFiguresSet(
    ['rectangle', new Rectangle('rectangle', 4, 'red', 5, 4)],
    ['triangle', new Triangle('triangle', 3, 'yellow', 10, 15)],
);

// It dinamically assigns to the object figures a property named
// "circle" whose value is a new Circle
figures.circle = new Circle('circle', 'green', 6);

Object.keys(figures).forEach((key) => {
  console.log(key);
  figures[key].print();
});
```

En el ejemplo anterior, hemos definido la interfaz TwoDimensionalFigure y tres clases que la implementan: Rectangle, Triangle y Circle.

Además, hemos definido la clase TwoDimensionalFigureSet, la cual recibe como argumentos en su constructor un array de tuplas cuya primera componente es una cadena de caracteres y cuya segunda componente es un objeto que implementa la interfaz TwoDimensionalFigure. De este modo, la primera componente de cada tupla pasada al constructor dará nombre a una propiedad del objeto que se cree, mientras que la segunda componente de cada tupla pasada al constructor será el valor asignado a dicha propiedad. Por último, se puede observar como se utiliza una signatura indexada que permitirá añadir propiedades de manera dinámica a un objeto de la clase TwoDimensionalFigureSet:

```[propertyName: string]: TwoDimensionalFigure;```

En concreto, puede observarse como el nombre de las propiedades que se pueden crear dinámicamente debe ser de tipo string, mientras que el valor asignado a dicha propiedad debe ser un objeto que implemente la interfaz TwoDimensionalFigure.

Al mismo tiempo, la variable figures apuntará a una instancia de la clase TwoDimensionalFiguresSet. Una vez inicializada dicha instancia, el objeto al que apunta figures contendrá:

Una propiedad denominada 'rectangle', cuyo valor es una instancia de la clase Rectangle, la cual implementa TwoDimensionalFigure.
Una propiedad denominada 'triangle', cuyo valor es una instancia de la clase Triangle, la cual implementa TwoDimensionalFigure.
Más adelante, en el código, se puede observar como se añade una nueva propiedad de manera dinámica al objeto apuntado por figures:

```figures.circle = new Circle('circle', 'green', 6);```

En concreto, se añade dinámicamente la propiedad denominada 'circle' cuyo valor es una instancia de la clase Circle, la cual también implementa la interfaz TwoDimensionalFigure.

Por último, el bucle forEach itera en los diferentes nombres de las propiedades del objeto apuntado por figures y, para cada una de ellas, imprime el nombre de la propiedad por consola e invoca al método print() del valor asignado a cada propiedad. El resultado es el siguiente:

```
rectangle
I am a red rectangle, I have 4 sides, and my area is 20.00
triangle
I am a yellow triangle, I have 3 sides, and my area is 75.00
circle
I am a green circle, and my area is 113.10
```
