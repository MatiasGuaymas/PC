# Práctica 5 - Rendezvous (ADA)

## Ejercicio 1.
Se requiere modelar un puente de un único sentido que soporta hasta 5 unidades de peso. El peso de los vehículos depende del tipo: cada auto pesa 1 unidad, cada camioneta pesa 2 unidades y cada camión 3 unidades. Suponga que hay una cantidad innumerable de vehículos (A autos, B camionetas y C camiones). Analice el problema y defina qué tareas, recursos y sincronizaciones serán necesarios/convenientes para resolverlo.

a. Realice la solución suponiendo que todos los vehículos tienen la misma prioridad.

b. Modifique la solución para que tengan mayor prioridad los camiones que el resto de los vehículos.

### <u>Respuestas</u>
a.
```ADA
Procedure Puente is
	TASK Acceso IS
		ENTRY AccesoAuto;
		ENTRY AccesoCamioneta;
		ENTRY AccesoCamion;
		ENTRY Salir (peso: IN integer);
	End Acceso;

	TASK TYPE Auto;
	vecAutos: array (1..A) of Auto;
	TASK BODY Auto IS
	BEGIN
		Acceso.AccesoAuto;
		Acceso.Salir(1);
	END Auto;
	
	TASK TYPE Camioneta;
	vecCamionetas: array (1..B) of Camioneta;
	TASK BODY Camioneta IS
	BEGIN
		Acceso.AccesoCamioneta;
		Acceso.Salir(2);
	END Camioneta;

	TASK TYPE Camion;
	vecCamiones: array (1..C) of Camion;
	TASK BODY Camion IS
	BEGIN
		Acceso.AccesoCamion;
		Acceso.Salir(3);
	END Camion;

	TASK BODY Acceso IS
		peso: integer := 0;
	BEGIN
		LOOP
			SELECT 
				WHEN (peso < 5) => 
					ACCEPT AccesoAuto DO
						peso:= peso + 1;
					END AccesoAuto;
			OR
				WHEN (peso < 4) => 
					ACCEPT AccesoCamioneta DO
						peso:= peso + 2;
					END AccesoCamioneta;
			OR
				WHEN (peso < 3)  => 
					ACCEPT AccesoCamion DO
						peso:= peso + 3;
					END AccesoCamion;
			OR
                ACCEPT Salir (pesoSalida: IN integer) DO
                    peso:= peso - pesoSalida;
                END Salir;
            END SELECT;	
		END LOOP;
	END Acceso;
Begin
	null;
End Puente;
```

b. 
```ADA
Procedure Puente is
	TASK Acceso IS
		ENTRY AccesoAuto;
		ENTRY AccesoCamioneta;
		ENTRY AccesoCamion;
		ENTRY Salir (peso: IN integer);
	End Acceso;

	TASK TYPE Auto;
	vecAutos: array (1..A) of Auto;
	TASK BODY Auto IS
	BEGIN
		Acceso.AccesoAuto;
		Acceso.Salir(1);
	END Auto;
	
	TASK TYPE Camioneta;
	vecCamionetas: array (1..B) of Camioneta;
	TASK BODY Camioneta IS
	BEGIN
		Acceso.AccesoCamioneta;
		Acceso.Salir(2);
	END Camioneta;

	TASK TYPE Camion;
	vecCamiones: array (1..C) of Camion;
	TASK BODY Camion IS
	BEGIN
		Acceso.AccesoCamion;
		Acceso.Salir(3);
	END Camion;

	TASK BODY Acceso IS
		peso: integer := 0;
	BEGIN
		LOOP
			SELECT 
				WHEN ((AccesoCamion’COUNT = 0) and (peso < 5)) => 
					ACCEPT AccesoAuto DO
						peso:= peso + 1;
					END AccesoAuto;
			OR
				WHEN ((AccesoCamion’COUNT = 0) and (peso < 4)) => 
					ACCEPT AccesoCamioneta DO
						peso:= peso + 2;
					END AccesoCamioneta;
			OR
				WHEN (peso < 3)  => 
					ACCEPT AccesoCamion DO
						peso:= peso + 3;
					END AccesoCamion;
			OR
                ACCEPT Salir (pesoSalida: IN integer) DO
                    peso:= peso - pesoSalida;
                END Salir;
            END SELECT;	
		END LOOP;
	END Acceso;
Begin
	null;
End Puente;
```

## Ejercicio 2.
Se quiere modelar el funcionamiento de un banco, al cual llegan clientes que deben realizar un pago y retirar un comprobante. Existe un único empleado en el banco, el cual atiende de acuerdo con el orden de llegada.

a. Implemente una solución donde los clientes llegan y se retiran sólo después de haber sido atendidos.

b. Implemente una solución donde los clientes se retiran si esperan más de 10 minutos para realizar el pago.

c. Implemente una solución donde los clientes se retiran si no son atendidos inmediatamente.

d. Implemente una solución donde los clientes esperan a lo sumo 10 minutos para ser atendidos. Si pasado ese lapso no fueron atendidos, entonces solicitan atención una vez más y se retiran si no son atendidos inmediatamente.

### <u>Respuestas</u>
a.
```ADA
Procedure Banco is
	TASK Empleado IS
		ENTRY Pedido (pago: IN text; comprobante: OUT text);
	END Empleado;

	TASK TYPE Cliente;
	vecClientes: array (1..C) of Cliente;
	TASK BODY Cliente IS
		comprobante: text;
	BEGIN
		Empleado.Pedido(“Pago”, comprobante);
	END Cliente;

	TASK BODY Empleado IS
	BEGIN
		LOOP
			ACCEPT Pedido(pago: IN text; comprobante: OUT text) DO
				comprobante := Cobrar(pago);
			END Pedido;
		END LOOP;
	END Empleado;
Begin
	null;
End Banco;
```

b.
```ADA
Procedure Banco is
	TASK Empleado IS
		ENTRY Pedido (pago: IN text; comprobante: OUT text);
	END Empleado;

	TASK TYPE Cliente;
	vecClientes: array (1..C) of Cliente;
	TASK BODY Cliente IS
		comprobante: text;
	BEGIN
        SELECT		
            Empleado.Pedido(“Pago”, comprobante);
        OR DELAY 600.0
            NULL;
        END SELECT;
	END Cliente;

	TASK BODY Empleado IS
	BEGIN
		LOOP
			ACCEPT Pedido(pago: IN text; comprobante: OUT text) DO
				comprobante := Cobrar(pago);
			END Pedido;
		END LOOP;
	END Empleado;
Begin
	null;
End Banco;
```

c.
```ADA
Procedure Banco is
	TASK Empleado IS
		ENTRY Pedido (pago: IN text; comprobante: OUT text);
	END Empleado;

	TASK TYPE Cliente;
	vecClientes: array (1..C) of Cliente;
	TASK BODY Cliente IS
		comprobante: text;
	BEGIN
        SELECT		
            Empleado.Pedido(“Pago”, comprobante);
        ELSE
            NULL;
        END SELECT;
	END Cliente;

	TASK BODY Empleado IS
	BEGIN
		LOOP
			ACCEPT Pedido(pago: IN text; comprobante: OUT text) DO
				comprobante := Cobrar(pago);
			END Pedido;
		END LOOP;
	END Empleado;
Begin
	null;
End Banco;
```

d.
```ADA
Procedure Banco is
	TASK Empleado IS
		ENTRY Pedido (pago: IN text; comprobante: OUT text);
	END Empleado;

	TASK TYPE Cliente;
	vecClientes: array (1..C) of Cliente;
	TASK BODY Cliente IS
		comprobante: text;
	BEGIN
        SELECT		
            Empleado.Pedido(“Pago”, comprobante);
        OR DELAY 600.0
            SELECT
                Empleado.Pedido(“Pago”, comprobante);
            ELSE
                NULL;
            END SELECT;
        END SELECT;
	END Cliente;

	TASK BODY Empleado IS
	BEGIN
		LOOP
			ACCEPT Pedido(pago: IN text; comprobante: OUT text) DO
				comprobante := Cobrar(pago);
			END Pedido;
		END LOOP;
	END Empleado;
Begin
	null;
End Banco;
```

## Ejercicio 3.
Se dispone de un sistema compuesto por 1 central y 2 procesos periféricos, que se comunican continuamente. Se requiere modelar su funcionamiento considerando las siguientes condiciones:
- La central siempre comienza su ejecución tomando una señal del proceso 1; luego toma aleatoriamente señales de cualquiera de los dos indefinidamente. Al recibir una señal de proceso 2, recibe señales del mismo proceso durante 3 minutos.
- Los procesos periféricos envían señales continuamente a la central. La señal del proceso 1 será considerada vieja (se deshecha) si en 2 minutos no fue recibida. Si la señal del proceso 2 no puede ser recibida inmediatamente, entonces espera 1 minuto y vuelve a mandarla (no se deshecha).

### <u>Respuesta</u>
```ADA
Procedure Sistema is
	TASK Central IS
		ENTRY TomarSenialP1 (senial: IN text);
		ENTRY TomarSenialP2 (senial: IN text);
		ENTRY Aviso;
	END Central;
	
	TASK Timer IS
		ENTRY IniciarTimer;
	END Timer;
	TASK BODY Timer IS
	BEGIN
		LOOP
            ACCEPT IniciarTimer;
            DELAY (180.0);
            Central.Aviso;
		END LOOP;
	END;

	TASK Proceso1;
	TASK BODY Proceso1 IS
		senial: text;
	BEGIN
		LOOP
			senial:= CrearSenial();
			SELECT
				Central.TomarSenialP1(senial);
			OR DELAY 120.0
				NULL;
			END SELECT;
		END LOOP;
	END Proceso1;

	TASK Proceso2;
	TASK BODY Proceso2 IS
		senial: text;
	BEGIN
		senial:= CrearSenial();
		LOOP
			SELECT
				Central.TomarSenialP2(senial);
				senial:= CrearSenial();
			ELSE
				DELAY 60.0;
			END SELECT;
		END LOOP;
	END Proceso2;

	TASK BODY Central IS
		fin: boolean;
	BEGIN
		ACCEPT TomarSenialP1(senial: IN text);
		LOOP
			SELECT
				ACCEPT TomarSenialP1(senial: IN text);
			OR
				ACCEPT TomarSenialP2(senial: IN text);
				Timer.IniciarTimer;
				fin := false;
                WHILE (not fin) LOOP
                    SELECT
                        WHEN (Aviso'COUNT = 0) => 
                            ACCEPT TomarSenialP2 (senial: IN text);
                    OR
                        ACCEPT Aviso DO
                        	fin:= true;
						END Aviso;
                    END SELECT;
                END LOOP;
            END SELECT;
		END LOOP;
	END Central;
Begin
	null;
End Sistema;
```

## Ejercicio 4.
En una clínica existe un médico de guardia que recibe continuamente peticiones de atención de las E enfermeras que trabajan en su piso y de las P personas que llegan a la clínica ser atendidos.
Cuando una persona necesita que la atiendan espera a lo sumo 5 minutos a que el médico lo haga, si pasado ese tiempo no lo hace, espera 10 minutos y vuelve a requerir la atención del médico. Si no es atendida tres veces, se enoja y se retira de la clínica.
Cuando una enfermera requiere la atención del médico, si este no lo atiende inmediatamente le hace una nota y se la deja en el consultorio para que esta resuelva su pedido en el momento que pueda (el pedido puede ser que el médico le firme algún papel). Cuando la petición ha sido recibida por el médico o la nota ha sido dejada en el escritorio, continúa trabajando y haciendo más peticiones.
El médico atiende los pedidos dándole prioridad a los enfermos que llegan para ser atendidos. Cuando atiende un pedido, recibe la solicitud y la procesa durante un cierto tiempo. Cuando está libre aprovecha a procesar las notas dejadas por las enfermeras.

### <u>Respuesta</u>
```ADA
Procedure Clinica is
	TASK MEDICO IS
		ENTRY PedidoAtencionC(pedido: IN text);
		ENTRY PedidoAtencionM(pedido: IN text);
	END Medico;

	TASK Escritorio IS
		ENTRY DejarNota(nota: IN text);
		ENTRY RevisarNotas(nota: IN OUT text);
	End Escritorio;

	TASK TYPE Persona;
	vecPersonas : array (1..P) of Persona;
	TASK BODY Persona IS
		cantIntentos: integer := 0;
		continuar: boolean:= true;
		pedido: text;
	BEGIN
		pedido:= GenerarPedido();
		WHILE ((cantIntentos < 3) and (continuar)) LOOP
			SELECT
				Medico.PedidoAtencionC(pedido);
				continuar:= false;
			OR DELAY 300.0
				cantIntentos:= cantIntentos + 1;
				IF (cantIntentos < 3) THEN
					DELAY 600.0;
				END IF;
			END SELECT;
		END LOOP;
	END Persona;

	TASK TYPE Enfermera;
	vecEnfermeras: array (1..E) of Enfermera;
	TASK BODY Enfermera IS
		pedido: text;
	BEGIN
		LOOP
		    pedido:= GenerarPedido();
		    SELECT
			    Medico.PedidoAtencionE(pedido);
		    ELSE
			    Escritorio.DejarNota(pedido);
		    END SELECT;
	    END LOOP;
	END Enfermera;

	TASK BODY Medico IS
		trabajo: text;
	BEGIN
		LOOP
			SELECT
				ACCEPT PedidoAtencionC(pedido: IN text)  DO
					trabajo:= AtenderPaciente(pedido);
				END PedidoAtencionC;
			OR
				WHEN (PedidoAtencionC’COUNT = 0) =>
                    ACCEPT PedidoAtencionM(pedido: IN text) DO
                        trabajo:= AtenderEnfermera(pedido);
                    END PedidoAtencionM;
            ELSE	
                SELECT 
                    Consultorio.RevisarNotas(trabajo);
                    trabajo:= ProcesarNota(trabajo);
                ELSE
	                NULL;
                END SELECT;
            END SELECT;
		END LOOP;
	END Medico;

	TASK BODY Escritorio IS
		trabajo: cola;
	BEGIN
		LOOP
			SELECT
                ACCEPT DejarNota(nota: IN text) DO
					c.push(nota);
				END DejarNota;
			OR
				WHEN (not c.isEmpty()) =>
					ACCEPT RevisarNotas(nota: IN OUT text) DO
		                c.pop(nota);
                    END RevisarNotas;
			END SELECT;
		END LOOP;
	END Escritorio;
Begin
	null;
End Clinica;
```

## Ejercicio X.
En un sistema para acreditar carreras universitarias, hay UN Servidor que atiende pedidos de U Usuarios de a uno a la vez y de acuerdo con el orden en que se hacen los pedidos. Cada usuario trabaja en el documento a presentar, y luego lo envía al servidor; espera la respuesta de este que le indica si está todo bien o hay algún error. Mientras haya algún error, vuelve a trabajar con el documento y a enviarlo al servidor. Cuando el servidor le responde que está todo bien, el usuario se retira. Cuando un usuario envía un pedido espera a lo sumo 2 minutos a que sea recibido por el servidor, pasado ese tiempo espera un minuto y vuelve a intentarlo (usando el mismo documento).

### <u>Respuesta</u>
```ADA
Procedure Sistema is
	TASK Servidor IS
		ENTRY RecibirPedido (doc: IN text; respuesta: OUT boolean);
	END Servidor;
	
	TASK TYPE Usuario;
	vecUsuarios: array (1..U) of Usuario;
	TASK BODY Usuario IS
		documento: text;
		hayError: boolean;
	BEGIN
		hayError := true;
		documento:= TrabajarDocumento();
		WHILE (hayError) LOOP
			SELECT
				Servidor.RecibirPedido(documento, hayError);
				IF (hayError) THEN
					documento := TrabajarDocumento(documento);
				END IF;
			OR DELAY 120.0
				DELAY 60.0;
			END SELECT;
		END LOOP;
	END Usuario;

	TASK BODY Servidor IS
	BEGIN
		LOOP
			ACCEPT RecibirPedido (doc: IN text; respuesta: OUT boolean) DO
				respuesta := HayError(doc);
			END RecibirPedido;
		END LOOP;
	END Servidor;
Begin
	null;
End Sistema;
```

## Ejercicio 5.
En una playa hay 5 equipos de 4 personas cada uno (en total son 20 personas donde cada una conoce previamente a que equipo pertenece). Cuando las personas van llegando esperan con los de su equipo hasta que el mismo esté completo (hayan llegado los 4 integrantes), a partir de ese momento el equipo comienza a jugar. El juego consiste en que cada integrante del grupo junta 15 monedas de a una en una playa (las monedas pueden ser de 1, 2 o 5 pesos) y se suman los montos de las 60 monedas conseguidas en el grupo. Al finalizar cada persona debe conocer el grupo que más dinero junto. Nota: maximizar la concurrencia. Suponga que para simular la búsqueda de una moneda por parte de una persona existe una función Moneda() que retorna el valor de la moneda encontrada.

### <u>Respuesta</u>
```ADA
Procedure Playa IS 
	TASK Administrador IS
		ENTRY CalcularGanador (idEquipo: IN integer; monto: IN integer);
		ENTRY IdentificarGanador (ganador: OUT integer);
	END Administrador;

	TASK TYPE Equipo IS 
		Entry RecibirID (id: IN integer);
		ENTRY Llegada;
		ENTRY Empezar;
		ENTRY SumarMonedas (monto: IN integer);
	END Equipo;
	vecEquipos = array(1..5) of Equipo;
	TASK BODY Equipo IS
		montoTotal: integer:= 0;
		idEquipo: integer;
	BEGIN
		ACCEPT RecibirID (id: IN integer) DO
			idEquipo:= id;
		END RecibirID;
		FOR i IN 1..4 LOOP
			ACCEPT Llegada;
		END LOOP;
		FOR i IN 1..4 LOOP
			ACCEPT Empezar;
		END LOOP;
		FOR i IN 1..4 LOOP
			ACCEPT SumarMonedas(monto: IN integer) DO
				montoTotal:= montoTotal + monto;
			END SumarMonedas;
		END LOOP;
		Administrador.CalcularGanador(idEquipo, montoTotal);
	END Equipo;

	TASK TYPE Persona;
	vecPersonas = array(1..20) of Persona;
	TASK BODY Persona IS
		equipo: integer:= ObtenerNumEquipo();
		monto: integer:= 0;
		ganador: integer;
	BEGIN
		Equipo(equipo).Llegada;
		Equipo(equipo).Empezar;
		FOR i 1..15 LOOP
			monto:= monto + Moneda();
		END LOOP;
		Equipo(equipo).SumarMonedas(monto);
		Administrador.IdentificarGanador(ganador);
	END Persona;

	TASK BODY Administrador IS
		max: integer:= -1;
		maxEquipo: integer;
	BEGIN
		FOR i IN 1..5 LOOP
			ACCEPT CalcularGanador (idEquipo: IN integer; monto: IN integer) DO
				IF (monto > max) THEN
					max:= monto;
					maxEquipo:= idEquipo;
				END IF;
			END CalcularGanador;
		END LOOP;
		FOR i IN 1..20 LOOP
			ACCEPT IdentificarGanador (ganador: OUT integer) DO
				ganador:= maxEquipo;
			END IdentificarGanador;
		END LOOP;
	END Administrador;
Begin
	FOR i IN 1..5 LOOP 
		vecEquipos(i).RecibirID(i);
	END LOOP;
End Playa;
```

## Ejercicio 6.
Se debe calcular el valor promedio de un vector de 1 millón de números enteros que se encuentra distribuido entre 10 procesos Worker (es decir, cada Worker tiene un vector de 100 mil números). Para ello, existe un Coordinador que determina el momento en que se debe realizar el cálculo de este promedio y que, además, se queda con el resultado. Nota: maximizar la concurrencia; este cálculo se hace una sola vez.

### <u>Respuesta</u>
```ADA
Procedure Promedio is
	TASK Coordinador IS
		ENTRY Iniciar;
		ENTRY Resultado (res: IN integer);
	END Coordinador;

	TASK TYPE Worker;
	vecWorkers: array (1..10) of Worker;
	TASK BODY Worker IS
		vec: array (1..100000) of integer := InicializarVector; 
		suma: integer:= 0;
	BEGIN
		Admin.Iniciar; 
        FOR i IN 1..100000 LOOP
	        suma:= suma + vec(i);
        END LOOP; 
        Admin.Resultado(suma);
	END Contador;

	TASK BODY Coordinador IS
		total: integer:= 0;
		prom: float;
	BEGIN
        FOR i IN 1..20 LOOP
            SELECT
                ACCEPT Iniciar;
            OR
                ACCEPT Resultado (suma: IN integer) DO
                        total = total + suma;
                END Resultado;
            END SELECT;
        END LOOP;
        promedio := (total / 1000000);
	END Coordinador;
Begin
	null;
End Promedio;
```

## Ejercicio 7.
Hay un sistema de reconocimiento de huellas dactilares de la policía que tiene 8 Servidores para realizar el reconocimiento, cada uno de ellos trabajando con una Base de Datos propia; a su vez hay un Especialista que utiliza indefinidamente. El sistema funciona de la siguiente manera: el Especialista toma una imagen de una huella (TEST) y se la envía a los servidores para que cada uno de ellos le devuelva el código y el valor de similitud de la huella que más se asemeja a TEST en su BD; al final del procesamiento, el especialista debe conocer el código de la huella con mayor valor de similitud entre las devueltas por los 8 servidores. Cuando ha terminado de procesar una huella comienza nuevamente todo el ciclo. Nota: suponga que existe una función Buscar(test, código, valor) que utiliza cada Servidor donde recibe como parámetro de entrada la huella test, y devuelve como parámetros de salida el código y el valor de similitud de la huella más parecida a test en la BD correspondiente. Maximizar la concurrencia y no generar demora innecesaria.

### <u>Respuesta</u>
```ADA
Procedure Sistema is
	TASK Especialista IS
		ENTRY EnviarHuella(res: OUT text);
		ENTRY Resultado(codigo: IN integer; valor: IN float);
		ENTRY Fin;
    END Especialista;

	TASK TYPE Servidor IS
		ENTRY Identificador(id: IN integer);
	END Servidor;
	vecServidores: array(1..8) of Servidor;
	TASK BODY Servidor IS
		idServidor, codigo: integer;
		test: text;
		valor: float;
	BEGIN
		ACCEPT Identificador(id: IN integer) DO
			idServidor:= id;
		END Identificador;
		LOOP
			Especialista.EnviarHuella(test);
			Buscar(test, codigo, valor);
			Especialista.Resultado(codigo, valor);
			Especialista.Fin;
		END LOOP;
	END Servidor;
	
	TASK BODY Especialista IS
		huella: text;
		max: float;
		idMax: integer;
	BEGIN
		LOOP
			huella:= TomarHuella();
			max:= -9999;
			FOR i IN 1..16 LOOP
				SELECT
					ACCEPT EnviarHuella (test: OUT text) DO
						test:= huella;
					END EnviarHuella;
				OR
					ACCEPT Resultado (codigo: IN integer; valor: IN float) DO
						IF (valor > max) THEN
							max:= valor;
							idMax:= codigo;
						END IF;
					END Resultado;
				END SELECT;
			END LOOP;
			FOR i IN 1..8 LOOP
				ACCEPT Fin;
			END LOOP;
		END LOOP;
	END Especialista;
Begin
	FOR i IN 1..8 LOOP
		vecServidores(i).Identificador(i);
	END LOOP;
End Sistema;
```

## Ejercicio 8.
Una empresa de limpieza se encarga de recolectar residuos en una ciudad por medio de 3 camiones. Hay P personas que hacen reclamos continuamente hasta que uno de los camiones pase por su casa. Cada persona hace un reclamo y espera a lo sumo 15 minutos a que llegue un camión; si no pasa, vuelve a hacer el reclamo y a esperar a lo sumo 15 minutos a que llegue un camión; y así sucesivamente hasta que el camión llegue y recolecte los residuos. Sólo cuando un camión llega, es cuando deja de hacer reclamos y se retira. Cuando un camión está libre la empresa lo envía a la casa de la persona que más reclamos ha hecho sin ser atendido. Nota: maximizar la concurrencia.

### <u>Respuesta</u>
```ADA
Procedure Limpieza is
	TASK Empresa IS
		ENTRY Pedido (id: IN integer);
		ENTRY AtenderReclamo (id: OUT integer);
	END Empresa;

	TASK TYPE Persona IS
		ENTRY Identificador (id: IN integer);
		ENTRY LlegadaCamion;
	END Persona;
	vecPersonas: array (1..P) of Persona;
	TASK BODY Persona IS
		seguir: boolean := true;
		idPersona: integer;
	BEGIN
		ACCEPT Identificador (id: IN integer) DO
			idPersona:= id;
		END Identificador;
		WHILE (seguir) LOOP
			Empresa.Pedido(id);
			SELECT
				ACCEPT LlegadaCamion DO
					seguir:= false;
				END LlegadaCamion;
			OR DELAY 900.0
				NULL;
			END SELECT;
		END LOOP;
	END Persona;

	TASK TYPE Camion;
	vecCamiones: array (1..3) of Camion;
		idPersona: integer;
	TASK BODY Camion IS
		LOOP
			Empresa.AtenderReclamo(idPersona);
			vecPersonas(idPersona).LlegadaCamion;
		END LOOP;
	END Camion;

	TASK BODY Empresa IS
		idPersona: integer;
		vecContador: array (1..P) of integer;
		max: integer:= 0;
		cantReclamos: integer:= 0;
	BEGIN
		FOR i IN 1..P LOOP
			vecContador(i) := 0;
		END LOOP;
		LOOP
			SELECT
				ACCEPT Pedido (id: IN integer) DO
					IF (vecContador(i) /= -999) THEN 
					    IF (vecContador(i) = 0) THEN
						    cantReclamos:= cantReclamos + 1;
					    END IF;
					    vecContador(i) := vecContador(i) + 1;
				    END IF;
				END Pedido;
			OR
				WHEN (cantReclamos > 0) =>
                    ACCEPT AtenderReclamo(id: OUT integer) DO
						FOR i IN 1..P LOOP
							IF (vecContador(i) > max) THEN
								max:= vecContador(i);
								id:= i;
							END IF;
						END LOOP;
						vecContador(i):= -999;
						max:= 0;
						cantReclamos:= cantReclamos - 1;
				    END AtenderReclamo;
            END SELECT;
		END LOOP;
	END Empresa;
Begin
	FOR i IN 1..P LOOP
		vecPersonas(i).Identificador(i);
	END LOOP;
End Limpieza;
```