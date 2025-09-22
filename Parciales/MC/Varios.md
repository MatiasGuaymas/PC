MC del 11-10-22
1. Resolver con SEMÁFOROS el siguiente problema. En una planta verificadora de vehículos, existen 7 estaciones donde se dirigen 150 vehículos para ser verificados. Cuando un vehículo llega a la planta, el coordinador de la planta le indica a qué estación debe dirigirse. El coordinador selecciona la estación que tenga menos vehículos asignados en ese momento. Una vez que el vehículo sabe qué estación le fue asignada, se dirige a la misma y espera a que lo llamen para verificar. Luego de la revisión, la estación le entrega un comprobante que indica si pasó la revisión o no. Más allá del resultado, el vehículo se retira de la planta. Nota: maximizar la concurrencia.
```c


```

2. Resolver con MONITORES el siguiente problema. En un sistema operativo se ejecutan 20 procesos que periódicamente realizan cierto cómputo mediante la función Procesar(). Los resultados de dicha función son persistidos en un archivo, para lo que se requiere de acceso al subsistema de E/S. Sólo un proceso a la vez puede hacer uso del subsistema de E/S, y el acceso al mismo se define por la prioridad del proceso (menor valor indica mayor prioridad).
```c

```
### XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

1. Resolver  con  SEMÁFOROS  el  siguiente  problema.  En  un  restorán  trabajan  C  cocineros  y  M  mozos.  De 
forma repetida, los cocineros preparan un plato y lo dejan listo en la bandeja de platos terminados, mientras 
que los mozos toman los platos de esta bandeja para repartirlos entre los comensales. Tanto los cocineros 
como los mozos trabajan de a un plato por vez. Modele el funcionamiento del restorán considerando que la 
bandeja de platos listos puede almacenar hasta P platos. No es necesario modelar a los comensales ni que 
los procesos terminen.  
### NOTA: es el problema de productores/consumidores con tamaño de buffer limitado visto en la página 14 de la teoría 4

```c

```
2. Resolver  con  MONITORES  el  siguiente  problema.  En  una  planta  verificadora  de  vehículos  existen  5 
estaciones de verificación. Hay 75 vehículos que van para ser verificados, cada uno conoce el número de 
estación a la cual debe ir. Cada vehículo se dirige a la estación correspondiente y espera a que lo atiendan. 
Una vez que le entregan el comprobante de verificación, el vehículo se retira. Considere que en cada estación 
se atienden a los vehículos de acuerdo con el orden de llegada. Nota: maximizar la concurrencia.
```C


```
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

1.  Resolver  con  SEMÁFOROS  el  siguiente  problema.  En  una  fábrica  de  muebles  trabajan  50  empleados.  A  llegar,  los  empleados 
forman 10 grupos de 5 personas cada uno, de acuerdo al orden de llegada (los 5 primeros en llegar forman el  primer grupo, los 5 
siguientes el segundo grupo, y así sucesivamente). Cuando un grupo se ha terminado de formar, todos sus integrantes se ponen a 
trabajar. Cada grupo debe armar M muebles (cada mueble es armado por un solo empleado); mientras haya muebles por armar en 
el  grupo  los  empleados  los  irán  resolviendo  (cada  mueble es  armado  por  un  solo  empleado).  Nota:  Cada  empleado  puede  tardar 
distinto  tiempo  en  armar  un  mueble.  Sólo  se  pueden  usar  los  procesos  “Empleado”,  y  todos  deben  terminar  su  ejecución. 
Maximizar la concurrencia
```c

```
2. Resolver con MONITORES el siguiente problema. En un comedor estudiantil hay un horno microondas que debe ser usado por E 
estudiantes  de  acuerdo  con  el  orden  de  llegada.  Cuando  el  estudiante  accede  al  horno,  lo  usa  y  luego  se  retira  para  dejar  al 
siguiente. Nota: cada Estudiante una sólo una vez el horno; los únicos procesos que se pueden usar son los “estudiantes”.
```C

```
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

MC del 10-10-23
 1.  Resolver con SEMÁFOROS los problemas siguientes: 
a)  En  una  estación  de  trenes,  asisten  P  personas  que  deben  realizar  una  carga  de  su  tarjeta  SUBE  en  la  terminal 
disponible.  La  terminal  es  utilizada  en  forma  exclusiva  por  cada  persona  de  acuerdo  con  el  orden  de  llegada. 
Implemente una solución utilizando sólo emplee procesos Persona. Nota: la función UsarTerminal() le permite cargar 
la SUBE en la terminal disponible. 
```c

```
b)  Resuelva  el  mismo  problema  anterior  pero  ahora  considerando  que  hay  T  terminales  disponibles.  Las  personas 
realizan  una  única  fila  y  la  carga  la  realizan  en  la  primera  terminal  que  se  libera.  Recuerde  que  sólo  debe  emplear 
procesos Persona. Nota: la función UsarTerminal(t) le permite cargar la SUBE en la terminal t.
```c

```
2. Resolver  con  MONITORES  el  siguiente  problema:  En  una  elección  estudiantil,  se  utiliza  una  máquina  para  voto 
electrónico. Existen N Personas que votan y una Autoridad de Mesa que les da acceso a la máquina de acuerdo con el 
orden de llegada, aunque ancianos y embarazadas tienen prioridad sobre el resto. La máquina de voto sólo puede ser 
usada por una persona a la vez. Nota: la función Votar() permite usar la máquina.
```c

```
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
MCyMD 01-07-2021

1. Resolver  el  siguiente  problema  con  MONITORES.  En  un  examen  de  la  secundaria  hay  un  preceptor  y
una  profesora  que  deben  tomar  un  examen  escrito  a  45  alumnos.  El  preceptor  se  encarga  de  darle  el
enunciado  del  examen  a  los  alumnos  cundo  los  45  han  llegado  (es  el  mismo  enunciado  para  todos).  La
profesora  se  encarga  de  ir  corrigiendo  los  exámenes  de  acuerdo  al  orden  en  que  los  alumnos  van
entregando.  Cada  alumno  al  llegar  espera  a  que  le  den  el  enunciado,  resuelve  el  examen,  y  al    terminar  lo
deja  para  que  la  profesora  lo  corrija  y  le  dé  la  nota.  Nota:  maximizar  la  concurrencia;  todos  los  procesos
deben  terminar  su  ejecución;  suponga  que  la  profesora  tienen  una  función  corregirExamen  que  recibe  un
examen y devuelve un entero con la nota.
```C

```

1. Resolver  el  siguiente  problema  con  SEMÁFOROS.  En  un  vacunatorio  hay  un  empleado  de  salud  para  
vacunar a 50 personas. El empleado de salud atiende a las personas de acuerdo al orden de llegada y de a 5 
personas a la vez, es decir que cuando está libre debe esperar a que haya al menos 5 personas esperando, 
luego  vacuna  a  las  5  primeras  personas,  y  al  terminar  las  deja  ir  para  esperar  por  otras  5.  Cuando  ha  
atendido  a  las  50  personas  el  empleado  de  salud  se  retira.  Nota:  todos  los  procesos  deben  terminar  su  
ejecución; asegurarse de no realizar Busy Waiting; suponga que el empleado tienen una función 
VacunarPersona() que simula que el empleado está vacunando a UNA persona. 
```C

```



1- Resolver con SEMAFOROS el siguiente problema. En el examen de una materia hay un docente y 50 alumnos. Cuando todos los alumnos han llegado comienza el examen. A medida que los alumnos van terminando le entregan el examen al docente, y esperan a que este le devuelva la nota del examen. El docente debe corregir los exámenes de acuerdo al orden en que los alumnos entregaron. Nota: maximizar la concurrencia.
```C

```

2- Resolver con SEMAFOROS el siguiente problema. Hay una carrera donde compiten 15 Autos; cuando todos han llegado se larga la carrera. Al terminar cada auto debe saber en qué posición terminó. Nota: maximizar la concurrencia.
```C

```
3- Resolver con MONITORES el siguiente problema. Hay un teléfono público que debe ser utilizado por N personas de acuerdo al orden de llegada (de a una persona a la vez). Nota: maximizar la concurrencia.
```C

```
4- Resolver con MONITORES el uso de un equipo de videoconferencia que puede ser usado por una única persona a la vez. Hay P Personas que utilizan este equipo (una única vez cada uno) para su trabajo de acuerdo a su prioridad. La prioridad de cada persona está dada por un número entero positivo. Además existe un Administrador que cada 3 hs. incrementa en 1 la prioridad de todas las personas que están esperando por usar el equipo. Nota: maximizar la concurrencia.
```C

```
5- Resolver con MONITORES el siguiente problema. En un Crucero por el Mediterráneo hay 200 personas que deben subir al barco por medio de 10 lanchas con 20 lugares cada una. Cada persona sube a la lancha que le corresponde. Cuando en una lancha han subido sus 20 personas durante 5 minutos navega hasta el barco. Recién cuando han llegado las 10 lanchas al barco se les permite a las 200 personas subir al barco. Nota: suponga que cada persona llama a la función int NúmeroDeLancha() que le devuelve un valor entre 0 y 9 indicando la lancha a la que debe subir. Maximizar la concurrencia.
```C

```