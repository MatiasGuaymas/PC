# Práctica de repaso – Memoria compartida

# Semáforos

## Ejercicio 1.
Resolver los problemas siguientes:

a. En una estación de trenes, asisten P personas que deben realizar una carga de su tarjeta SUBE en la terminal disponible. La terminal es utilizada en forma exclusiva por cada persona de acuerdo con el orden de llegada. Implemente una solución utilizando únicamente procesos Persona. Nota: la función UsarTerminal() le permite cargar la SUBE en la terminal disponible. 

b. Resuelva el mismo problema anterior pero ahora considerando que hay T terminales disponibles. Las personas realizan una única fila y la carga la realizan en la primera terminal que se libera. Recuerde que sólo debe emplear procesos Persona. Nota: la función UsarTerminal(t) le permite cargar la SUBE en la terminal t.

### <u>Respuestas</u>
a.
```C
Cola c;
boolean libre = true;
sem mutex = 1;
sem espera[P] = ([P], 0);

Process Persona [id: 0..P-1] {
	int idAux;
	P (mutex);
	if not libre {
		c.push(id);
		V (mutex);
		P (espera[id]);
    } else {
        libre = false;
        V (mutex);
    }
    UsarTerminal();
    P (mutex);
    if c.isEmpty() {
        libre = true;
    } else {
        c.pop(idAux);
        V (espera[idAux]);
    }
    V (mutex);
}
```

b.
```C
Cola terminalesLibres;
Cola esperaTerminal;
sem mutexLibres = 1;
sem mutexEspera = 1;
sem espera[P] = ([P], 0);
int vecTerminal [P];

Process Persona [id: 0..P-1] {
	int terminalLibre, siguiente;
	P (mutexLibres);
	if not terminalesLibres.isEmpty(){
		terminalesLibre.pop (terminalLibre);
		vecTerminal [id] = terminalLibre;
		V (mutexLibres);
    } else {
        V (mutexLibres);
        P (mutexEspera);
        c.push (id);
        V (mutexEspera);
        P (espera[id]);
    }
    UsarTerminal (vecTerminal[id]);
    P (mutexLibres);
    P (mutexEspera);
    if esperaTerminal.isEmpty() {
        V (mutexEspera);
        terminalesLibres.push(vecTerminal[id]);
        V (mutexLibres);
    } else {
        V (mutexLibres);
        c.pop (idAux);
        V (mutexEspera);
        vecTerminal [idAux] = vecTerminal[id];
        V (espera[idAux]);
    }
}
```

## Ejercicio 2.
Implemente una solución para el siguiente problema. Un sistema debe validar un conjunto de 10000 transacciones que se encuentran disponibles en una estructura de datos. Para ello, el sistema dispone de 7 workers, los cuales trabajan colaborativamente validando de a 1 transacción por vez cada uno. Cada validación puede tomar un tiempo diferente y para realizarla los workers disponen de la función Validar(t), la cual retorna como resultado un número entero entre 0 al 9. Al finalizar el procesamiento, el último worker en terminar debe informar la cantidad de transacciones por cada resultado de la función de validación. Nota: maximizar la concurrencia. 

### <u>Respuesta</u>
```C
sem mutexCola = 1;
sem mutexBarrera = 1;
sem contador [10] = ([10], 1);
int vectorContador [10] = ([10], 0);
int cant = 0;

Process Worker [id: 0..6] {
	text transaccion;
	int i;
	P (mutexCola);
	while not c.isEmpty() {
		c.pop(transaccion);
		V (mutexCola);
		int res = Validar(transaccion);
		P (contador[res]);
		vectorContador[res]++;
		V (contador[res]);
		P (mutexCola);
    }
    V (mutexCola);
    P (mutexBarrera);
    cant++;
    if (cant == 7) {
        for i: 0..9 {
            writeln(i, ‘ ’, vectorContador[i]);
        }
    }
    V (mutexBarrera);
}
```

## Ejercicio 3.
Implemente una solución para el siguiente problema. Se debe simular el uso de una máquina expendedora de gaseosas con capacidad para 100 latas por parte de U usuarios. Además, existe un repositor encargado de reponer las latas de la máquina. Los usuarios usan la máquina según el orden de llegada. Cuando les toca usarla, sacan una lata y luego se retiran. En el caso de que la máquina se quede sin latas, entonces le debe avisar al repositor para que cargue nuevamente la máquina en forma completa. Luego de la recarga, saca una botella y se retira. Nota: maximizar la concurrencia; mientras se reponen las latas se debe permitir que otros usuarios puedan agregarse a la fila.

### <u>Respuesta</u>
```C
sem mutex = 1;
sem hayQueReponer = 0;
sem hayLatas = 0;
sem espera[U] = ([U], 0);
Cola c;
int cantLatas = 100;
boolean libre = true;

Process Usuario [id: 0..U-1] {
	int idAux;
	P (mutex);
	if not libre {
        c.push(id);
        V (mutex);
        P (espera[id]);
    } else {
        libre = false;
        V (mutex);
    }
    if cantLatas == 0 {
        V (hayQueReponer);
        P (hayLatas);
    }
    cantLatas–;
    P (mutex);
    if c.isEmpty() {
        libre = true;
    } else {
        c.pop(idAux);
        V (espera[idAux]);
    }
    V (mutex);
}

Process Repositor {
	while (true) {
        P (hayQueReponer);
        cantLatas = 100;
        V (hayLatas);
    }
}
```

# Monitores

## Ejercicio 1.
Resolver el siguiente problema. En una elección estudiantil, se utiliza una máquina para voto electrónico. Existen N Personas que votan y una Autoridad de Mesa que les da acceso a la máquina de acuerdo con el orden de llegada, aunque ancianos y embarazadas tienen prioridad sobre el resto. La máquina de voto sólo puede ser usada por una persona a la vez. Nota: la función Votar() permite usar la máquina.

### <u>Respuesta</u>
```C
Process Persona [id: 0..N-1] {
	int edad = …;
	Maquina.llegar(id, edad);
	Votar();
	Maquina.liberar();	
}

Process Autoridad {
	for i: 0 .. N-1 {
		Maquina.darPermiso();
    }
}

Monitor Maquina {	
	ColaOrdenada c;
	cond espera[N];
	cond hayPedido, esperaUso;

	Procedure llegar (id: in int, edad: in int) {
		c.pushOrdenado(id, edad);
		signal (hayPedido);
		wait (espera[id]);
    }

    Procedure darPermiso() {
        if c.isEmpty() wait (hayPedido);
        c.pop(id);
        signal (espera[id]);
        wait (esperaUso);
    }

    Procedure liberar() {
        signal (esperaUso);
    }
}
```

## Ejercicio 2.
Resolver el siguiente problema. En una empresa trabajan 20 vendedores ambulantes que forman 5 equipos de 4 personas cada uno (cada vendedor conoce previamente a qué equipo pertenece). Cada equipo se encarga de vender un producto diferente. Las personas de un equipo se deben juntar antes de comenzar a trabajar. Luego cada integrante del equipo trabaja independientemente del resto vendiendo ejemplares del producto correspondiente. Al terminar cada integrante del grupo debe conocer la cantidad de ejemplares vendidos por el grupo. Nota: maximizar la concurrencia.

### <u>Respuesta</u>
```C
Process Vendedor [id: 0..19] {
	int equipo = …;
	int cant = 0;
	int cantGrupo;
	Equipo[equipo].llegar();
	cant = VenderProductos();
	Equipo[equipo].fin(cant, cantGrupo);
}

Monitor Equipo [id: 0..4] {
	cond espera;
	int total = 0;
	int cant = 0;

	Procedure llegar() {
		cant++;
		if (cant = 4) {
	        signal_all(espera);
	        cant = 0;
        } else {
	        wait (espera);
        }
    }

    Procedure fin (cantProd: in int, cantGrupo: out int) {
        total += cantProd;
        cant++;
        if (cant = 4) {
            signal_all(espera);
        } else {
            wait(espera);
        }
        cantGrupo = total;
    }
}
```

## Ejercicio 3.
Resolver el siguiente problema. En una montaña hay 30 escaladores que en una parte de la subida deben utilizar un único paso de a uno a la vez y de acuerdo con el orden de llegada al mismo. Nota: sólo se pueden utilizar procesos que representen a los escaladores; cada escalador usa sólo una vez el paso.

### <u>Respuesta</u>
```C
Process Escalador [id: 0..29] {
	Montaña.llegar();
	// Cruza la montaña
	Montaña.salir();
}

Monitor Montaña {
	cond espera;
	boolean libre = true;
	int esperando = 0;

	Procedure llegar() {
		if not libre {
			esperando++;
			wait (esperando);
        } else {
	        libre = false;
        }
    }

    Procedure salir() {
        if esperando > 0 {
            esperando–;
            signal (esperando);
        } else {
            libre = true;
        }
    }
}
```