Tutorial para simpapp, una Simple Aplicación en C
====================


¿Qué es simpapp?
++++++++++++++++++
simpapp es una aplicación ATMI de muestra que incluye un cliente y un servidor. Esta aplicación se distribuye con el software Oracle Tuxedo. El servidor realiza un solo servicio: acepta una cadena alfabética en minúsculas del cliente y devuelve la misma cadena en mayúsculas.


Preparación de archivos y recursos de simpapp
+++++++++++++++++++++++++++++++++++++++++++++++
Este tema es un tutorial que lo guía, paso a paso, a través del proceso de desarrollo y ejecución de una aplicación ATMI de Oracle Tuxedo de muestra. El siguiente diagrama de flujo resume el proceso. Haga clic en cada tarea para obtener instrucciones sobre cómo completar esa tarea.

* Prerequisitos
* Paso 1: Cómo copiar los archivos de simpapp
* Paso 2: examinar y compilar el cliente
* Paso 3: examinar y compilar el servidor
* Paso 4: Edición y carga del archivo de configuración
* Paso 5: Cómo iniciar la aplicación
* Paso 6: Cómo ejecutar la aplicación en tiempo de ejecución
* Paso 7: Cómo monitorear la aplicación en tiempo de ejecución
* Paso 8: Cómo cerrar la aplicación

Antes de que empieces
++++++++++++++++++++++++++
Antes de poder ejecutar este tutorial, se debe instalar el cliente y servidor Oracle Tuxedo ATMI para que los archivos y comandos a los que se hace referencia estén disponibles. Si la instalación ya ha sido realizada por otra persona, debe conocer la ruta del directorio en el que está instalado el software (TUXDIR). También debe tener permisos de lectura y escritura en los directorios y archivos en la estructura de directorios de Oracle Tuxedo para poder copiar archivos simpapp y ejecutar comandos de Oracle Tuxedo.

Prerequisitos
++++++++++++++++++
1. Este laboratorio se realizo con este SO::

	$ cat /etc/redhat-release 
	Red Hat Enterprise Linux Server release 7.5 (Maipo)
	
2. Debe tener una IP::

	$ ifconfig | grep -w inet
		inet 192.168.1.20  netmask 255.255.254.0  broadcast 192.168.1.255
		inet 127.0.0.1  netmask 255.0.0.0

3. Tener configurado el nombre de la maquina::

	$ hostname
	nodo01
	$ cat /etc/hostname 
	localhost.localdomain


4. Tener una entrada del nombre del HOST en el archivo /etc/hosts o en el dns::

	$ grep $(hostname) /etc/hosts
	192.168.1.20	nodo01	srv01	srv02

5. Asegurar que responda por la IP::

	$ ping -c 3 $(hostname)
	PING nodo01 (192.168.1.20) 56(84) bytes of data.
	64 bytes from nodo01 (192.168.1.20): icmp_seq=1 ttl=64 time=0.028 ms
	64 bytes from nodo01 (192.168.1.20): icmp_seq=2 ttl=64 time=0.047 ms
	64 bytes from nodo01 (192.168.1.20): icmp_seq=3 ttl=64 time=0.055 ms

	--- nodo01 ping statistics ---
	3 packets transmitted, 3 received, 0% packet loss, time 1998ms
	rtt min/avg/max/mdev = 0.028/0.043/0.055/0.012 ms

6. Tener un usuario del SO y con los privilegios en Tuxedo::

	$ whoami
	oracle
	$ ls -ld /u01/oracle/orahome_1/tuxedo12.2.2.0.0/
	drwxrwx--- 12 oracle oinstall 4096 Aug 30 22:50 /u01/oracle/orahome_1/tuxedo12.2.2.0.0/

7. Tener bien configurado el umask::

	$ umask
	0022

8. Opcional tener un archivo de env, en el perfil del usuario para que inicializar las variables, TUXDIR, APPDIR, TUXCONFIG::

	$ cd 
	$ vi simpapp.env
	  TUXDIR=/u01/oracle/orahome_1/tuxedo12.2.2.0.0; export TUXDIR
	  APPDIR=/u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir; export APPDIR
	  TUXCONFIG=/u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir/tuxconfig; export TUXCONFIG
	$ source simpapp.env

**Nota** el directorio APPDIR y el Archivo TUXCONFIG aun no estan creados, pero no nos impide tener las variables creadas.

9. Iniciar las variables de entorno del archivo env tux.env, ubicado en $TUXDIR::

	$ cat tux.env 
	#	Copyright (c) 2014 Oracle, Inc.
	#	All rights reserved
	#
	#	THIS IS UNPUBLISHED PROPRIETARY
	#	SOURCE CODE OF ORACLE, Inc.
	#	The copyright notice above does not
	#	evidence any actual or intended
	#	publication of such source code.
	#
	TUXDIR=/u01/oracle/orahome_1/tuxedo12.2.2.0.0; export TUXDIR
	JAVA_HOME=/usr/lib/jvm/jdk1.8.0_151/jre; export JAVA_HOMETUXDIR
	JVMLIBS=$JAVA_HOME/lib/amd64/server:$JAVA_HOME/bin
	PATH=$TUXDIR/bin:$JAVA_HOME/bin:$PATH; export PATH
	COBCPY=:$TUXDIR/cobinclude; export COBCPY
	COBOPT="-C ANS85 -C ALIGN=8 -C NOIBMCOMP -C TRUNC=ANSI -C OSEXT=cbl"; export COBOPT
	SHLIB_PATH=$TUXDIR/lib:$JVMLIBS:$SHLIB_PATH; export SHLIB_PATH
	LIBPATH=$TUXDIR/lib:$JVMLIBS:$LIBPATH; export LIBPATH
	LD_LIBRARY_PATH=$TUXDIR/lib:$JVMLIBS:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH
	WEBJAVADIR=$TUXDIR/udataobj/webgui/java; export WEBJAVADIR

	$ source $TUXDIR/tux.env


Paso 1: Cómo copiar los archivos de simpapp
+++++++++++++++++++++++++++++++++++++++++++++++
**Nota:**	Las siguientes instrucciones se basan en una plataforma de sistema UNIX. Las instrucciones para plataformas que no son UNIX, como Windows 2003, pueden ser diferentes. Los ejemplos utilizados en las aplicaciones de muestra pueden variar significativamente, dependiendo de la plataforma específica.

1. Haga un directorio para simpappy cdpara él::

	mkdir simpdir 
	cd simpdir

**Nota:**	Se sugiere este paso para que pueda ver los simpapp archivos que tiene al principio y los archivos adicionales que cree en el camino. Use el shell estándar (/bin/sh) o el shell Korn; no utilice csh.

2. Establecer y exportar variables de entorno::

	TUXDIR=pathname of the Oracle Tuxedo system root directory
	TUXCONFIG=pathname of your present working directory/tuxconfig
	PATH=$PATH:$TUXDIR/bin
	LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$TUXDIR/lib
	APPDIR=pathname of de application simpapp
	export TUXDIR TUXCONFIG PATH LD_LIBRARY_PATH APPDIR

**Nota** Puede ser redundante nuevamente cargar las variables y exportarlas, pero es para demostrar lo importante que estemos claro con esto y esten bien configuradas.


Es necesario TUXDIR y PATH para acceder a los archivos en la estructura de directorios del sistema de Oracle Tuxedo y para ejecutar comandos del sistema de Oracle Tuxedo. En Sun Solaris, /usr/5bindebe ser el primer directorio en su PATH. Con AIX en el RS/6000, use en LIBPATH lugar de LD_LIBRARY_PATH. En HP-UX en HP 9000, use en SHLIB_PATHlugar de LD_LIBRARY_PATH.

Debe configurar TUXCONFIG para poder cargar el archivo de configuración, que se describe en el Paso 4: Edición y carga del archivo de configuración .

3. Copia los simpapp archivos::

	cp $TUXDIR/samples/atmi/simpapp/*.

**Nota:**	Es mejor comenzar con una copia de los archivos en lugar de los originales entregados con el software porque editará algunos de los archivos para hacerlos ejecutables.

4. Liste los archivos::

   $ ls 
   README  simpcl.c  simpserv.c  ubbsimple

**Nota:**	Los READMEarchivos proporcionan explicaciones de los otros archivos.
Los tres archivos que son centrales para la aplicación son:

	* simpcl.c: El código fuente del programa cliente.
	* simpserv.c: El código fuente del programa del servidor.
	* ubbsimple: La forma de texto del archivo de configuración para la aplicación.


Paso 2: examinar y compilar el cliente
++++++++++++++++++++++++++++++++++++++++
Cómo examinar al cliente
Revise el código fuente del programa cliente ATMI::

     $ more simpcl.c

El resultado se muestra en la siguiente lista.::


	1 #include <stdio.h>          
	2 #include "atmi.h" / * TUXEDO * / 
	3 
	4 
	5 
	6 
	7 #ifdef __STDC__ 
	8 main (int argc, char * argv []) 
	9 
	10 #else 
	11 
	12 main (argc , argv) 
	13 int argc; 
	14 char * argv []; 
	15 #endif 
	16 
	17 { 
	18 
	19 char * sendbuf, * rcvbuf; 
	20 int sendlen, rcvlen; 
	21 int ret; 
	22 
	23 if (argc! = 2) { 
	24 fprintf (stderr, "Uso: simpcl string \ n"); 
	25 salida (1); 
	26}
	27 / * Adjuntar a BEA TUXEDO como un proceso de cliente * / 
	28 if (tpinit ((TPINIT *) NULL) == -1) { 
	29 fprintf (stderr, "Tpinit falló \ n"); 
	30 salida (1); 
	31} 
	32 sendlen = strlen (argv [1]); 
	33 if ((sendbuf = (char *) tpalloc ("STRING", NULL, sendlen + 1)) == NULL) { 
	34 fprintf (stderr, "Error al asignar el buffer de envío \ n"); 
	35 tpterm (); 
	36 salida (1); 
	37} 
	38 if ((rcvbuf = (char *) tpalloc ("STRING", NULL, sendlen + 1)) == NULL) { 
	39 fprintf (stderr, "Error al asignar el buffer de recepción \ n");
	40 tpfree (sendbuf); 
	41 tpterm (); 
	42 salida (1); 
	43} 
	44 strcpy (sendbuf, argv [1]); 
	45 ret = tpcall ("TOUPPER", sendbuf, NULL, & rcvbuf, & rcvlen, 0); 
	46 if (ret == -1) { 
	47 fprintf (stderr, "No se puede enviar la solicitud al servicio TOUPPER \ n"); 
	48 fprintf (stderr, "Tperrno =% d,% s \ n", tperrno, 
	49 tmemsgs [tperrno]); 
	50 tpfree (sendbuf); 
	51 tpfree (rcvbuf); 
	52 tpterm (); 
	53 salida (1); 
	54}
	55 printf ("La cadena devuelta es:% s \ n", rcvbuf); 
	56 
	57 / * Buffers gratuitos y separación de Oracle TUXEDO * / 
	58 tpfree (sendbuf); 
	59 tpfree (rcvbuf); 
	60 tpterm (); 
	61}

Tabla 2-1 Líneas significativas en el código fuente de simpcl.c 

Líneas
Archivo / Función
Propósito
 	 	 
2
atmi.h
Se requiere un archivo de encabezado cada vez que se utilizan las funciones de Oracle Tuxedo ATMI.
28
tpinit()
La función ATMI utilizada por un programa cliente para unirse a una aplicación.
33
tpalloc()
La función ATMI utilizada para asignar un búfer escrito. STRINGes uno de los cinco tipos básicos de búfer de Oracle Tuxedo; NULLindica que no hay argumento de subtipo. El argumento restante sendlen + 1, especifica la longitud del búfer más 1 para el carácter nulo que finaliza la cadena.
38
tpalloc()
Asigna otro búfer para el mensaje de retorno.
45
tpcall()
Envía el búfer de mensajes al TOUPPERservicio especificado en el primer argumento. También incluye la dirección del búfer de retorno. tpcall()espera un mensaje de respuesta.
35, 41, 52, 60
tpterm()
La función ATMI utilizada para salir de una aplicación. Se tpterm()utiliza una llamada a para salir de la aplicación antes de salir en respuesta a una condición de error (líneas 36, 42 y 53). La última llamada a tpterm()(línea 60) se emite después de que se imprime el mensaje.
40, 50, 51, 58, 59
tpfree()
Libera los buffers asignados. tpfree()es el opuesto funcional de tpalloc().
55
printf()
La conclusión exitosa del programa. Imprime el mensaje devuelto por el servidor.

Cómo compilar al cliente
+++++++++++++++++++++++++++++
**Cómo examinar el Cliente**

1. Primero debe editar el archivo simpcl.c y incluir los headers de string::

	#include <string.h>

2. Ejecute build clientpara compilar el programa cliente ATMI::

     buildclient -o simpcl -f simpcl.c

El archivo de salida es simpcl y el archivo de origen de entrada es simpcl.c.

2. Comprueba los resultados::

	 $ ls -l 
	total 32
	-rw-rw---- 1 oracle oinstall  2943 Mar 30  2016 README
	-rwxr-xr-x 1 oracle oinstall 13432 Aug 30 22:59 simpcl
	-rw-rw---- 1 oracle oinstall  1846 Aug 30 22:59 simpcl.c
	-rw-rw---- 1 oracle oinstall  2155 Mar 30  2016 simpserv.c
	-rw-rw---- 1 oracle oinstall   886 Mar 30  2016 ubbsimple

Como se puede ver, ahora tenemos un módulo ejecutable llamado simpcl. El tamaño de simpcl puede variar.


Paso 3: examinar y compilar el servidor
++++++++++++++++++++++++++++++++++++++++
**Cómo examinar el servidor**

1. Revise el código fuente del programa del servidor ATMI.::

	$ more simpserv.c

	* /
	/ * #ident "@ (#) apps / simpapp / simpserv.c $ Revisión: 1.1 $" * / 
	1  #include <stdio.h> 
	2  #include <ctype.h> 
	3  #include <atmi.h> / * Archivo de encabezado TUXEDO * / 
	4  #include <userlog.h> / * TUXEDO Header File * / 
	5  / * tpsvrinit se ejecuta cuando se inicia un servidor, antes de que comience a 
	   procesar las solicitudes. No es necesario tener esta función. 
	   También está disponible tpsvrdone (no se usa en este ejemplo), que se 
	   llama en el momento de apagado del servidor.
	9  * / 
	10# si está definido (__ STDC__) || definido (__ cplusplus) 

	12tpsvrinit (int argc, char * argv []) 
	13#else 
	14tpsvrinit (argc, argv) 
	15int argc; 
	16char ** argv; 
	17#endif 
	18     { 
	19                 / * Algunos compiladores advierten si no se usan argc y argv. 
	20                 * / 
	21        argc = argc; 
	22                argv = argv; 
	23        / * userlog escribe en el registro de mensajes central de TUXEDO * / 
	24                  userlog ("Bienvenido al servidor simple"); 
	25                 retorno (0); 
	26} 
	27/ * Esta función realiza el servicio real solicitado por el cliente. 
	       Su argumento es una estructura que contiene, entre otras cosas, un puntero
		al búfer de datos y la longitud del búfer de datos. 
	30* / 
	31#ifdef __cplusplus 
	32extern "C" 
	33#endif 
	34vacío 
	35#if definido (__ STDC__) || definido (__ cplusplus) 
	36TOUPPER (TPSVCINFO * RQST) 
	37#else 
	38TOUPPER (RQST) 
	39TPSVCINFO * RQST; 
	40#endif 
	41  { 
	42               int i; 
	43 
	44       para (i = 0; i <rqst-> len-1; i ++) 
	45                               rqst-> datos [i] = toupper (rqst-> datos [i]); 
	46               / * Devuelve el búfer transformado al solicitante. * / 
	47              tpreturn (TPSUCCESS, 0, rqst-> data, 0L, 0); 
	48}

Tabla 2-2 Partes significativas del código fuente simpserv.c

Líneas
Archivo / Función
Propósito
Archivo completo
 	
Un servidor Oracle Tuxedo no contiene a main(). El main()sistema Oracle Tuxedo lo proporciona cuando se construye el servidor.
12
tpsvrinit()
Esta subrutina se llama durante la inicialización del servidor, es decir, antes de que el servidor comience a procesar las solicitudes de servicio. Una subrutina predeterminada (proporcionada por el sistema Oracle Tuxedo) escribe un mensaje para USERLOGindicar que el servidor se ha iniciado. userlog(3c)es un registro utilizado por el sistema Oracle Tuxedo y puede ser utilizado por las aplicaciones.
38
TOUPPER()
La declaración de un servicio (el único ofrecido por simpserv). El único argumento esperado por el servicio es un puntero a una TPSVCINFOestructura, que contiene la cadena de datos que se convertirá a mayúsculas.
45
for loop
Convierte la entrada a mayúsculas mediante llamadas repetidas a TOUPPER.
49
tpreturn()
Devuelve la cadena convertida al cliente con el TPSUCCESSconjunto de indicadores.

Cómo compilar el servidor
+++++++++++++++++++++++++
1. Ejecute buildserverpara compilar el programa del servidor ATMI::

     buildserver -o simpserv -f simpserv.c -s TOUPPER

El archivo ejecutable que se creará se llama simpserv y simpserv.c es el archivo fuente de entrada. La opción -s TOUPPER  especifica el servicio que se anunciará cuando se inicie el servidor.

Comprueba los resultados::

	 $ ls -l 
	total 44
	-rw-rw---- 1 oracle oinstall  2943 Mar 30  2016 README
	-rwxr-xr-x 1 oracle oinstall 13432 Aug 30 22:59 simpcl
	-rw-rw---- 1 oracle oinstall  1846 Aug 30 22:59 simpcl.c
	-rwxr-xr-x 1 oracle oinstall 10016 Aug 30 23:04 simpserv
	-rw-rw---- 1 oracle oinstall  2155 Mar 30  2016 simpserv.c
	-rw-rw---- 1 oracle oinstall   886 Mar 30  2016 ubbsimple


Ahora tiene un módulo ejecutable llamado simpserv.


Paso 4: Edición y carga del archivo de configuración
+++++++++++++++++++++++++++++++++++++++++++++++++++++
**Cómo editar el archivo de configuración**

1. En un editor de texto, familiarícese con ubbsimple cuál es el archivo de configuración simpapp.::

	1 $ 
	2 
	3 #Skeleton UBBCONFIG archivo para la aplicación BEA Tuxedo Simple. 
	4 # Reemplace los elementos <bracketed> con los valores apropiados. 
	5 RECURSOS 
	6 IPCKEY < Reemplazar con una clave IPC válida mayor que 32768 > 
	7 
	8 # Ejemplo: 
	9 
	10 #IPCKEY 62345 
	11 
	12 MASTER simple 
	13 MAXACCESSERS 5 
	14 MAXSERVERS 5 
	15 MAXSERVICES 10 
	16 MODELO SHM 
	17 LDBAL N 
	18 
	19 * MÁQUINAS 
	20 
	21 PREDETERMINADO : 
	22 
	23 APPDIR = "< Reemplazar con la ruta actual >" 
	24 TUXCONFIG = "<Reemplazar con TUXCONFIG Nombre de ruta > " 
	25 TUXDIR =" < Directorio raíz de Tuxedo (not /) > " 
	26 # Ejemplo : 
	27 # APPDIR =" / usr / me / simpdir " 
	28 # TUXCONFIG =" / usr / me / simpdir / tuxconfig " 
	29 # TUXDIR =" / usr / tuxedo " 
	30 
	31 <nombre de máquina> LMID = simple 
	32 # Ejemplo: 
	33 #tuxmach LMID = simple 
	34 * GRUPOS 
	35 GROUP1 
	36 LMID = simple GRPNO = 1 OPENINFO = NINGUNO 
	37 
	38 * SERVIDORES 
	39 PREDETERMINADO: 
	40 CLOPT = "- A" 
	41 simpserv SRVGRP = GROUP1 SRVID = 1 
	42 * SERVICIOS 
	43 TOUPPER

Los campos editados quedarían asi::

	IPCKEY          32777

	DEFAULT:
		APPDIR="/u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir"
		TUXCONFIG="/u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir/tuxconfig"
		TUXDIR="/u01/oracle/orahome_1/tuxedo12.2.2.0.0"

	nodo01  LMID=simple


2. Para cada uno <string> (es decir, para cada cadena que se muestra entre paréntesis angulares), sustituya un valor apropiado.

**Cómo cargar el archivo de configuración**

1. Ejecute tmloadcf para cargar el archivo de configuración, el que declaramos en la variable TUXCONFIG::

	$ tmloadcf ubbsimple 
	Inicializar archivo TUXCONFIG: /u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir/tuxconfig [y, q] ?
	$

2. Comprueba los resultados::

	$ ls -l 
	total 580
	-rw-rw---- 1 oracle oinstall   2943 Mar 30  2016 README
	-rwxr-xr-x 1 oracle oinstall  13432 Aug 30 22:59 simpcl
	-rw-rw---- 1 oracle oinstall   1846 Aug 30 22:59 simpcl.c
	-rwxr-xr-x 1 oracle oinstall  10016 Aug 30 23:04 simpserv
	-rw-rw---- 1 oracle oinstall   2155 Mar 30  2016 simpserv.c
	-rw------- 1 oracle oinstall 544768 Aug 30 23:13 tuxconfig
	-rw-rw---- 1 oracle oinstall    881 Aug 30 23:12 ubbsimple
	-rw-r--r-- 1 oracle oinstall    481 Aug 30 23:13 ULOG.083019

Ahora tiene un archivo llamado tuxconfig. El archivo tuxconfig es un archivo nuevo bajo el control del sistema Oracle Tuxedo.

3. Vea el formato::

	$ file tuxconfig 
	tuxconfig: data

4. Visualice el LOG::

	$ cat ULOG.083019 
	231305.nodo01!tmloadcf.2968.3406209472.-2: 08-30-2019: client high water (), total client ()              
	231305.nodo01!tmloadcf.2968.3406209472.-2: 08-30-2019: Tuxedo Version 12.2.2.0.0, 64-bit
	231305.nodo01!tmloadcf.2968.3406209472.-2: CMDTUX_CAT:879: INFO: A new file system has been created. (size = 1276 512-byte blocks)
	231305.nodo01!tmloadcf.2968.3406209472.-2: CMDTUX_CAT:871: INFO: TUXCONFIG file /u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir/tuxconfig has been created

Ver para más comandos https://docs.oracle.com/cd/E13161_01/tuxedo/docs10gr3/rfcm/rfcmd.html

Paso 5: Cómo iniciar la aplicación
++++++++++++++++++++++++++++++++++++++
1. Ejecute tmbootpara abrir la aplicación::

	$ tmboot
	Boot all admin and server processes? (y/n): y
	Booting all admin and server processes in /u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir/tuxconfig
	INFO: Oracle Tuxedo, Version 12.2.2.0.0, 64-bit, Patch Level (none)

	Booting admin processes ...

	exec BBL -A :
		process id=3007 ... Started.

	Booting server processes ...

	exec simpserv -A :
		process id=3010 ... Started.
	2 processes started.


El BBL es el proceso administrativo que monitorea las estructuras de memoria compartida en la aplicación. simpserv es el simpapp servidor que se ejecuta continuamente, esperando solicitudes.

2. Listamos los procesos::

	$ ps -ef | grep tuxedo
	oracle    3007     1  0 23:24 pts/0    00:00:00 BBL -C dom=simpapp -g 30002 -i 0 -u nodo01 -U /u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir/ULOG -m 0 -A
	oracle    3010     1  0 23:24 pts/0    00:00:00 simpserv -C dom=simpapp -g 1 -i 1 -u nodo01 -U /u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir/ULOG -m 0 -A

3. Vemos el origen del BBL::

	$ which BBL
	/u01/oracle/orahome_1/tuxedo12.2.2.0.0/bin/BBL

Ver para más comandos https://docs.oracle.com/cd/E13161_01/tuxedo/docs10gr3/rfcm/rfcmd.html

Paso 6: Cómo ejecutar la aplicación en tiempo de ejecución
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Para ejecutar su simpapp, haga que el cliente envíe una solicitud.::

	$ simpcl "hola, mundo" 
	HOLA, MUNDO


Paso 7: Cómo monitorear la aplicación en tiempo de ejecución
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Como administrador, puede usar el tmadmin intérprete de comandos para verificar una aplicación y realizar cambios dinámicos. Para ejecutar tmadmin, debe tener establecida la TUXCONFIG como variable de entorno.

tmadmin Puede interpretar y ejecutar más de 50 comandos. Para una lista completa. Lo siguiente usa dos de los tmadmin comandos.

1. Ingrese el siguiente comando::

	$ tmadmin
	tmadmin - Copyright (c) 1996-2016 Oracle.
	All Rights Reserved.
	Distributed under license by Oracle.
	Tuxedo is a registered trademark.

	> 

Nota:	El signo mayor que (>) es el tmadmin indicador.

2. Ingrese el printserver(psr)comando para mostrar información sobre los servidores::

	> psr
	Prog Name      Queue Name  2ndQueue Name  Grp Name      ID RqDone Load Done Current Service
	---------      ----------  ----------     --------      -- ------ --------- ---------------
	BBL            32777                      simple         0      0         0 (  IDLE )
	simpserv       00001.00001                GROUP1         1      0         0 (  IDLE )


3. Ingrese el printservice(psc)comando para mostrar información sobre los servicios::

	> psc
	Service Name Routine Name Prog Name  Grp Name  ID    Machine  # Done Status
	------------ ------------ ---------  --------  --    -------  ------ ------
	TOUPPER      TOUPPER      simpserv   GROUP1     1     simple       0 AVAIL

4. para salir::

	> quit

Ver para más comandos https://docs.oracle.com/cd/E13161_01/tuxedo/docs10gr3/rfcm/rfcmd.html

Paso 8: Cómo cerrar la aplicación
+++++++++++++++++++++++++++++++++++++
1. Ejecute tmshutdown para desactivar la aplicación::
	
	$ tmshutdown 
	Shutdown all admin and server processes? (y/n): y
	Shutting down all admin and server processes in /u01/oracle/orahome_1/tuxedo12.2.2.0.0/simpdir/tuxconfig

	Shutting down server processes ...

		Server Id = 1 Group Id = GROUP1 Machine = simple:	shutdown succeeded

	Shutting down admin processes ...

		Server Id = 0 Group Id = simple Machine = simple:	shutdown succeeded
	2 processes stopped.

2. Comprueba el ULOG::

	$ cat ULOG* 
	232424.nodo01!simpserv.3010.958108096.0: LIBTUX_CAT:262: INFO: Standard main starting
	232424.nodo01!simpserv.3010.958108096.0: Welcome to the simple server
	233335.nodo01!simpserv.3010.958108096.0: LIBTUX_CAT:522: INFO: Default tpsvrdone() function used
	233338.nodo01!BBL.3007.1161916864.0: CMDTUX_CAT:26: INFO: The BBL is exiting system

Ver para más comandos https://docs.oracle.com/cd/E13161_01/tuxedo/docs10gr3/rfcm/rfcmd.html
