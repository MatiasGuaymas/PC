# Práctica de repaso – Memoria distribuida

# Pasaje de mensajes

## Ejercicio 1.
En una oficina existen 100 empleados que envían documentos para imprimir en 5 impresoras compartidas. Los pedidos de impresión son procesados por orden de llegada y se asignan a la primera impresora que se encuentre libre:

a. Implemente un programa que permita resolver el problema anterior usando PMA.

b. Resuelva el mismo problema anterior pero ahora usando PMS.

### <u>Respuestas</u>
a.
```C
chan impresion(text, int);
chan impresionLista[100](text);
	
Process Empleado [id: 0..99] {
	text impresion, documento;
	while (true) {
        documento = GenerarDocumento();
        send impresion(documento, id);
        receive impresionLista[id](impresion);
    }
}
Process Impresora [id: 0..4] {
	text documento, imp;
	int idE;
	while (true) {
        receive impresion(documento, idE);
        imp = ImprimirDocumento(documento);
        send impresionLista[idE](imp);
    }
}
```

b.
```C
Process Empleado [id: 0..99] {
	text res;
	while true {
		text documento = GenerarDocumento();
        Admin!llegada(id, documento);
        Impresora[*]?impresion(res);
    }
}
Process Impresora [id: 0..4] {
	int idEmpleado;
	text documento, res;
	while true {
		Admin!siguiente(id);
		Admin?hayDocumento(idEmpleado, documento);
		res = ImprimirDocumento(documento);
		Empleado[idEmpleado]!impresion(res);
    }
}
Process Admin {
	int idEmpleado, int idImpresora;
	text documento;
	Cola c;
	do Empleado[*]?llegada(idEmpleado, documento) → c.push(idEmpleado, documento);
	[] not c.isEmpty(); Impresora[*]?siguiente(idImpresora) → Impresora[idImpresora]!hayDocumento(c.pop());
	od
}
```

## Ejercicio 2.
Resolver el siguiente problema con PMS. En la estación de trenes hay una terminal de SUBE que debe ser usada por P personas de acuerdo con el orden de llegada. Cuando la persona accede a la terminal, la usa y luego se retira para dejar al siguiente. Nota: cada Persona usa sólo una vez la terminal.

### <u>Respuesta</u>
```C
Process Persona [id: 0..P-1] {
	Admin!solicitud(id);
	Admin?pasar();
	UsarTerminal();
	Admin!salir();
	
}
Process Admin {
	int idPersona;
	boolean libre = true;
	Cola c;
	do Persona[*]?solicitud(idPersona) →
		if (libre) {
            Persona[idPersona]!pasar();
            libre = false;
        } else {
            c.push(idPersona);
        }
    [] Persona[*]?salir() →
        if (c.isEmpty()) {
            libre = true;
        } else {
            Persona[c.pop()]!pasar();
        }
    od
}
```

## Ejercicio 3.
Resolver el siguiente problema con PMA. En un negocio de cobros digitales hay P personas que deben pasar por la única caja de cobros para realizar el pago de sus boletas. Las personas son atendidas de acuerdo con el orden de llegada, teniendo prioridad aquellos que deben pagar menos de 5 boletas de los que pagan más. Adicionalmente, las personas embarazadas tienen prioridad sobre los dos casos anteriores. Las personas entregan sus boletas al cajero y el dinero de pago; el cajero les devuelve el vuelto y los recibos de pago.

### <u>Respuesta</u>
```C
chan hayPedido(boolean);
chan prioridadEmbarazo(int, text, double);
chan prioridadBoletas(int, text, double);
chan sinPrioridad(int, text, double);
chan esperandoResultados[P](text, double);

Process Persona [id: 0..P-1] {
	int cantBoletas = DeterminarBoletas();
	text boletas = …;
	text recibos;
	double pago = …;
	double vuelto;
	boolean estoyEmbarazada = …;
	if (estoyEmbarazada) {
	    send prioridadEmbarazo(id, boletas, pago);
    } else if (cantBoletas < 5) {
	    send prioridadBoletas(id, boletas, pago);
    } else {
	    send sinPrioridad(id, boletas, pago);
    }
    send hayPedido(true);
    receive esperandoResultados[id](recibos, vuelto);
}

Process Cajero {
	boolean ok;
	int idPersona;
	text boletas, recibos;
	double pago, vuelto;
	while (true) {
        receive hayPedido(ok);
        if (not empty (prioridadEmbarazo)) {
            receive prioridadEmbarazo(id, boletas, pago);
        } else if (not empty(prioridadBoletas)) {
            receive prioridadBoletas(id, boletas, pago);
        } else {
            receive sinPrioridad(id, boletas, pago);
        }
        recibos, vuelto = CobrarBoletas(boletas, pago);
        send esperandoResultados[id](recibos, vuelto);
    }
}
```

# ADA

## Ejercicio 1.
Resolver el siguiente problema. La página web del Banco Central exhibe las diferentes cotizaciones del dólar oficial de 20 bancos del país, tanto para la compra como para la venta. Existe una tarea programada que se ocupa de actualizar la página en forma periódica y para ello consulta la cotización de cada uno de los 20 bancos. Cada banco dispone de una API, cuya única función es procesar las solicitudes de aplicaciones externas. La tarea programada consulta de a una API por vez, esperando a lo sumo 5 segundos por su respuesta. Si pasado ese tiempo no respondió, entonces se mostrará vacía la información de ese banco.

### <u>Respuesta</u>
```ADA
Procedure Banco is
	TASK APIBanco IS
		ENTRY Cotizacion(compra: OUT String; venta: OUT String);
	END APIBanco;

	TASK Tarea;
	TASK BODY Tarea IS
		compra, venta: String;
		cotizacionesCompra: array(1..20) of String;
		cotizacionesVenta: array(1..20) of String;
	BEGIN
		LOOP
			FOR i IN 1..20 LOOP
				SELECT
					vecAPIs(i).Cotizacion(compra, venta);
					cotizacionesCompra(i) := compra;
					cotizacionesVenta(i) := venta;
				OR DELAY 5.0
					cotizacionesCompra(i) := “ ”;
					cotizacionesVenta(i) := “ ”;
				END SELECT;
			END LOOP;
		END LOOP;
	END Tarea;

	TASK TYPE APIBanco;
	vecAPIs: array (1..20) of APIBanco;
	TASK BODY APIBanco IS
	BEGIN
		LOOP
			ACCEPT Cotizacion(compra: OUT String; venta: OUT String) DO
				compra:= CotizacionCompra();
				venta:= CotizacionVenta();
			END Cotizacion;
		END LOOP;
	END APIBanco;
Begin
	null;
End Banco;
```

## Ejercicio 2.
Resolver el siguiente problema. En un negocio de cobros digitales hay P personas que deben pasar por la única caja de cobros para realizar el pago de sus boletas. Las personas son atendidas de acuerdo con el orden de llegada, teniendo prioridad aquellos que deben pagar menos de 5 boletas de los que pagan más. Adicionalmente, las personas ancianas tienen prioridad sobre los dos casos anteriores. Las personas entregan sus boletas al cajero y el dinero de pago; el cajero les devuelve el vuelto y los recibos de pago. 

### <u>Respuesta</u>
```ADA
Procedure Cobro is
	TASK Cajero IS
		ENTRY CobroEmbarazadaAnciano(boletas: IN text; pago: IN float; recibos: OUT text; vuelto: OUT float);
		ENTRY CobroPocasBoletas(boletas: IN text; pago: IN float; recibos: OUT text; vuelto: OUT float);
		ENTRY CobroSinPrioridad(boletas: IN text; pago: IN float; recibos: OUT text; vuelto: OUT float);
	END Cajero;

	TASK TYPE Persona;
	vecPersonas: array(1..P) of Persona;
	TASK BODY Persona IS
		prioridad: boolean:= DeterminarEmbAnc();
		boletas: text := ObtenerBoletas();
		recibos: text;
		pago, vuelto: float;
	BEGIN
		IF (prioridad) THEN
			Cajero.CobroEmbarazadaAnciano(boletas, pago, recibos, vuelto);
		ELSIF (boletas.size() < 5) THEN
			Cajero.CobroPocasBoletas(boletas, pago, recibos, vuelto);
		ELSE
			Cajero.CobroSinPrioridad(boletas, pago, recibos, vuelto);
		END IF;
	END Persona;

	TASK BODY Cajero IS
	BEGIN
		LOOP 
			SELECT
				ACCEPT CobroEmbarazadaAnciano(boletas: IN text; pago: IN float; recibos: OUT text; vuelto: OUT float) DO
					recibos, vuelto:= Cobrar(boletas, pago);
				END CobroEmbarazadaAnciano;
			OR
				WHEN (CobroEmbarazadaAnciano’COUNT = 0) =>
					ACCEPT CobroPocasBoletas(boletas: IN text; pago: IN float; recibos: OUT text; vuelto: OUT float) DO
						recibos, vuelto:= Cobrar(boletas, pago);
					END CobroPocasBoletas;
			OR
				WHEN (CobroEmbarazadaAnciano’COUNT = 0 and CobroPocasBoletas’COUNT = 0) =>
					ACCEPT CobroSinPrioridad(boletas: IN text; pago: IN float; recibos: OUT text; vuelto: OUT float) DO
						recibos, vuelto:= Cobrar(boletas, pago);
					END CobroSinPrioridad;
			END SELECT;
		END LOOP;
	END Cajero;
Begin
	null;
End;
```

## Ejercicio 3.
Resolver el siguiente problema. La oficina central de una empresa de venta de indumentaria debe calcular cuántas veces fue vendido cada uno de los artículos de su catálogo. La empresa se compone de 100 sucursales y cada una de ellas maneja su propia base de datos de ventas. La oficina central cuenta con una herramienta que funciona de la siguiente manera: ante la consulta realizada para un artículo determinado, la herramienta envía el identificador del artículo a las sucursales, para que cada una calcule cuántas veces fue vendido en ella. Al final del procesamiento, la herramienta debe conocer cuántas veces fue vendido en total, considerando todas las sucursales. Cuando ha terminado de procesar un artículo comienza con el siguiente (suponga que la herramienta tiene una función generarArtículo() que retorna el siguiente ID a consultar). Nota: maximizar la concurrencia. Existe una función ObtenerVentas(ID) que retorna la cantidad de veces que fue vendido el artículo con identificador ID en la base de la sucursal que la llama.

### <u>Respuesta</u>
```ADA
Procedure ArticuloCatalogo is
	TASK Herramienta IS
		ENTRY Valor (ID: OUT integer);
		ENTRY Resultado (res: IN integer);
		ENTRY Fin;
	BEGIN Herramienta;

	TASK TYPE Sucursal;
	vecSucursales: array (1..100) of Sucursal;
	TASK BODY Sucursal IS
		ID: integer;
		cant: integer;
	BEGIN
		LOOP
			Herramienta.Valor(ID);
			cant:= ObtenerVentas(ID);
			Herramienta.Resultado(cant);
			Herramienta.Fin
		END LOOP;
	END Sucursal;

	TASK BODY Herramienta IS
		IDArticulo: integer;
		cant: integer;
	BEGIN
		LOOP
			IDArticulo:= generarArtículo();
			cant:= 0;
			FOR i IN 1..200 LOOP
				SELECT
					ACCEPT Valor (ID: OUT integer) DO
						ID:= IDArticulo;
					END Valor;
				OR
					ACCEPT Resultado (res: IN integer) DO
						cant:= cant + res;
					END Resultado;
				END SELECT;
			END LOOP;
			FOR i IN 1..100 LOOP
				ACCEPT Fin;
			END LOOP;
		END LOOP;
	END Herramienta;
Begin
	null;
End ArticuloCatalogo;
```