# Práctica 1 - Variables compartidas

## Ejercicio 1. 

Para el siguiente programa concurrente suponga que todas las variables están inicializadas en 0 antes de empezar. Indique cual/es de las siguientes opciones son verdaderas:

a) En algún caso el valor de x al terminar el programa es 56.

b) En algún caso el valor de x al terminar el programa es 22.

c) En algún caso el valor de x al terminar el programa es 23.

| **P1** | **P2** | **P3** |
|--------|--------|--------|
| If (x = 0) then <br> &nbsp;&nbsp;y := 4 * 2; <br> &nbsp;&nbsp;x := y + 2; | If (x > 0) then <br> &nbsp;&nbsp;x := x + 1; | x := (x * 3) + (x * 2) + 1; |

### <u>Respuestas</u>

``` C
p1:	1- Load y, reg1
    2- Add 2, reg1
    3- Store reg1, x	

p2:	4- Load x,reg2
	5- Add 1, reg2
	6- Store reg2, x

p3:	7- Load x, reg3
	8- Load x, reg4
	9- Multi  reg3, 3, reg 3
    10- Multi reg4, 2, reg4
    11- Add reg3, reg4, reg5
    12- Add 1, reg5
    13- Store x, reg5 
```

- a) V. Es posible que x termine con el valor 56 si se ejecuta el P1, P2 y luego el P3
- b) V. Es posible que x termine con el valor 22 si el orden de las instrucciones es 1- 2- 7- 3- 8- 9- 10- 11- 12- 13 - 4- 5- 6
- c) V. Es posible que x termine con el valor 23 si el orden de las instrucciones es 1-2-7-3-4-5-6-8-9-10-11-12-13


## Ejercicio 2.

Realice una solución concurrente de grano grueso (utilizando <> y/o <await B; S>) para el siguiente problema. Dado un número N verifique cuántas veces aparece ese número en un arreglo de longitud M. Escriba las pre-condiciones que considere necesarias.

### <u>Respuesta</u>
``` C
Precondiciones: 
- K procesos Buscar.
- Arreglo inicializado con M elementos.
- M mod K = 0. 

int total=0; int buscado=N; int arreglo [M]; int cantBuscar = M DIV K;

Process Buscar [id: 0..K-1] {
	int aux = 0;
	int ini = id * cantBuscar;
	for i = ini .. (ini + cantBuscar - 1) {
        if (arreglo[i] = buscado) then
            aux++;
    }
    <total = total + aux>
}
```

## Ejercicio 3.

Dada la siguiente solución de grano grueso:

a) Indicar si el siguiente código funciona para resolver el problema de Productor/Consumidor con un buffer de tamaño N. En caso de no funcionar, debe hacer las modificaciones necesarias.

| Variables |
|----------------------|
| int cant = 0; int pri_ocupada = 0; int pri_vacia = 0; int buffer[N]; |

| **Process Productor::** | **Process Consumidor::** |
|-----------------------|-------------------------|
| { &nbsp;while (true)<br>&nbsp;&nbsp;{ &nbsp;*produce elemento*<br>&nbsp;&nbsp;&nbsp;&nbsp;&lt;await (cant < N); cant++&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;buffer[pri_vacia] = *elemento*;<br>&nbsp;&nbsp;&nbsp;&nbsp;pri_vacia = (pri_vacia + 1) mod N;<br>&nbsp;&nbsp;}<br>} | { &nbsp;while (true)<br>&nbsp;&nbsp;{ &lt;await (cant > 0); cant--&gt;<br>&nbsp;&nbsp;&nbsp;&nbsp;*elemento* = buffer[pri_ocupada];<br>&nbsp;&nbsp;&nbsp;&nbsp;pri_ocupada = (pri_ocupada + 1) mod N;<br>&nbsp;&nbsp;&nbsp;&nbsp;*consume elemento*<br>&nbsp;&nbsp;}<br>} |

b) Modificar el código para que funcione para C consumidores y P productores.

### <u>Respuestas</u>

a) 
``` C
int cant= 0; int pri_ocupada= 0; int pri_vacia=0; it buffer[N]; 
Process Productor::{
    //produce el elemento 
    <await (cant < N); cant++ 
    buffer[pri_vacia] = elemento;>
    pri_vacia = (pri_vacia + 1) MOD n;
    }
}
Process Consumidor::{
	while(true) {
        <await(cant > 0); cant –; 
        elemento= buffer[pri_ocupada>;
        pri_ocupada= (pri_ocupada) + 1) mod N;
        //consume elemento;
	}
}
```

b) 
``` C
int cant= 0; int pri_ocupada= 0; int pri_vacia=0; it buffer[N]; 
Process Productor [id: 0...P-1] {
	while (true) {
        //produce el elemento 
        <await (cant < N); cant++ 
        buffer[pri_vacia] = elemento;
        pri_vacia = (pri_vacia + 1) MOD n;>
    }
}
Process Consumidor [id: 0...C-1] {
	while(true){
		{<await(cant > 0); cant –; 
		elemento= buffer[pri_ocupada];
		pri_ocupada= (pri_ocupada) + 1) mod N >
		//consume elemento;
	}
}
```


## Ejercicio 4.

Resolver con SENTENCIAS AWAIT (<> y <await B; S>). Un sistema operativo mantiene 5 instancias de un recurso almacenadas en una cola, cuando un proceso necesita usar una instancia del recurso la saca de la cola, la usa y cuando termina de usarla la vuelve a depositar. 

### <u>Respuesta</u>
``` C
colaEspecial C;
int cantRecursos = 5

Process Proceso [id: 0..N-1] {
	<await (cantRecursos>0); 
    Recurso recurso = Sacar(C);
    cantRecursos-–;>
    // Usa el recurso
    <Agregar(C,recurso)
    cantRecursos++;>
}
```

## Ejercicio 5.

En cada ítem debe realizar una solución concurrente degrano grueso (utilizando <> y/o <await B; S>) para el siguiente problema, teniendo en cuenta las condiciones indicadas en el item. Existen N personas que deben imprimir un trabajo cada una. 

a) Implemente una solución suponiendo que existe una única impresora compartida por todas las personas, y las mismas la deben usar de a una persona a la vez, sin importar el orden. Existe una función Imprimir(documento) llamada por la persona que simula el uso de la impresora. Sólo se deben usar los procesos que representan a las Personas.

b) Modifique la solución de (a) para el caso en que se deba respetar el orden de llegada.

c) Modifique la solución de (a) para el caso en que se deba respetar el orden dado por el identificador del proceso (cuando está libre la impresora, de los procesos que han solicitado su uso la debe usar el que tenga menor identificador).

d) Modifique la solución de (b) para el caso en que además hay un proceso Coordinador que le indica a cada persona que es su turno de usar la impresora.

### <u>Respuestas</u>

a) 
``` C
Process Persona [id: 0..N-1];  {
	Documento documento; 
	<Imprimir(documento) >
}
```
b)
``` C
int siguiente=-1;
cola C; // Esta cola no agrega ordenado por prioridad u otro criterio

Process Persona [id: 0..N-1];  {
	<if (siguiente = -1) siguiente = id
	else Agregar(C, id)>; 
	<await (siguiente == id) >;       
	Documento documento = Imprimir(documento)
	<if (empty(C) siguiente = -1
	else siguiente = Sacar(C)>;
}

// Se podría utilizar el algoritmo Ticket también.
```
c)
``` C
int siguiente=-1;
colaEspecial C; // Esta cola si agrega ordenado por prioridad u otro criterio

Process Persona [id: 0..N-1];  {
	<if (siguiente = -1) siguiente = id
	else Agregar(C, id)>; 
	<await (siguiente == id) >;       
	Documento documento = Imprimir(documento)
	<if (empty(C) siguiente = -1
	else siguiente = Sacar(C)>;
}
```
d)
``` C
int actual = -1;
Cola C;
bool ocupada = false;

Process Coordinador {
	while (true) {
		<await (!ocupada && !cola.estaVacia());
		actual= Sacar(C);>
		ocupada= true;
    }
}

Process Persona[id:0..N-1]{
	<Agregar (C, id);>
	<await (actual == id)>; 
	Documento documento = Imprimir(documento); 
	ocupada= false;
}
```

## Ejercicio 6.

Dada la siguiente solución para el Problema de la Sección Crítica entre dos procesos (suponiendo que tanto SC como SNC son segmentos de código finitos, es decir que terminan en algún momento), indicar si cumple con las 4 condiciones requeridas:

| Variables |
|----------------------|
| int turno = 1; |

| **Process SC1::** | **Process SC2::** |
|-----------------------|-------------------------|
| { &nbsp;while (true)<br>&nbsp;&nbsp;{ &nbsp;while (turno == 2) skip;<br>&nbsp;&nbsp;&nbsp;&nbsp;SC; <br>&nbsp;&nbsp;&nbsp;&nbsp;turno = 2;<br>&nbsp;&nbsp;&nbsp;&nbsp;SNC;<br>&nbsp;&nbsp;}<br>} | { &nbsp;while (true)<br>&nbsp;&nbsp;{ while (turno == 2) skip;<br>&nbsp;&nbsp;&nbsp;&nbsp;SC;<br>&nbsp;&nbsp;&nbsp;&nbsp;turno = 1;<br>&nbsp;&nbsp;&nbsp;&nbsp;SNC;<br>&nbsp;&nbsp;}<br>} |

### <u>Respuesta</u>
- Exclusión mutua: Cumple. Nunca va a pasar que los dos entren a SC.
- Ausencia de Deadlock (Livelock): Cumple. Nunca se quedan los dos bloqueados.
- Ausencia de Demora Innecesaria: No cumple. Ej: el procesador 2 quiere entrar a su sección crítica y no puede hacerlo porque para ello el procesador 1 debe cambiar el turno, y se queda esperando en su sección no crítica.
- Eventual Entrada: Cumple

## Ejercicio 7.

Desarrolle una solución de grano fino usando sólo variables compartidas (no se puede usar las sentencias await ni funciones especiales como TS o FA). En base a lo visto en la clase 3 de teoría, resuelva el problema de acceso a sección crítica usando un proceso coordinador. En este caso, cuando un proceso SC[i] quiere entrar a su sección crítica le avisa al coordinador, y espera a que éste le dé permiso. Al terminar de ejecutar su sección crítica, el proceso SC[i] le avisa al coordinador. <b>Nota:</b> puede basarse en la solución para implementar barreras con “Flags y coordinador” vista en la teoría 3.

### <u>Respuesta</u>
``` C
int actual =- 1;

Process Worker[id: 0 .. N-1] {
    while (true) { 
        vec[id] = true;
        while (actual != id) skip;
        SC 
        actual = -1;
    } 
} 

Process Coordinador { 
    while (true) { 
        for [i: 0 .. N-1] {  
            if vec [i] {
                actual = i;
                vec[i] = false;
                while (actual != 1) skip;
            }
        } 
    } 
} 
```
