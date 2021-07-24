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