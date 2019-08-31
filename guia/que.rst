BEA/Oracle TUXEDO
====================

Tuxedo (Transactions for Unix, Extended for Distributed Operations)es una plataforma de middleware usada para gestionar procesos transaccionales distribuidos en entornos de computación distribuida. Tuxedo es un middleware orientado a transacciones (en inglés Transaction Oriented Middleware, TOM).

Fue desarrollado inicialmente por AT&T en 1984 para la creación y administración de sistemas de comercio electrónico (e-commerce) con procesos transaccionales en línea, sistemas OLTP (online transaction processing systems). La primera aplicación en la que se usó se llamó LMOS.

Tuxedo fue adquirido por BEA Systems, compañía que fue a su vez adquirida por Oracle.

Tuxedo es un framework que se encarga de administrar transacciones, te permite concentrarte en la funcionalidad y no tener que inventar la parte de transacciones que es muy compleja. Imagina que requieres hacer un proceso compuesto de 4 o 5 pasos, cada paso es ejecutado en sitios distintos, cada paso hace algo muy particular como actualizar un archivo, insertar un registro en una base de datos, dar aviso a otro proceso, etc etc. Si alguno e estos pasos falla, la transacción debe ser deshecha, por ejemplo si el paso 4 falla, el paso 3, 2 y 1 no sirven de nada y debe hacerse un rollback. Ideal para usarse en bancos. Actualmente es muy utilizado en instituciones financieras donde se tiene alto flujo transaccional.

Tuxedo es va bastante viejo, existe desde hace mas de 15 años, lo cual no es necesariamente malo. Otra cualidad importante es que puede ser accesado desde distintos lenguajes y se puede comunicar con otros procesos mediante IPC.

¿Qué es el sistema BEA Tuxedo?
++++++++++++++++++++++++++++++++++

El sistema BEA Tuxedo es un producto de middleware que distribuye aplicaciones en múltiples plataformas, bases de datos y sistemas operativos utilizando comunicaciones basadas en mensajes y si lo desea, procesamiento de transacciones distribuidas.

Middleware se usa con aplicaciones cliente/servidor para distribuir el procesamiento entre múltiples servidores, administrar transacciones distribuidas e integrar múltiples plataformas de bases de datos. Los sistemas de middleware a veces se conocen como sistemas de "procesamiento de transacciones en línea" u "OLTP".

El sistema BEA Tuxedo es un producto maduro basado en más de 15 años de desarrollo de un grupo diverso de compañías tecnológicas que incluyen AT&T, UNIX System Laboratories (USL), Novell y BEA Systems, Inc. Es una plataforma de desarrollo y una plataforma de ejecución. . El sistema BEA Tuxedo sirve como una extensión del sistema operativo.

El sistema BEA Tuxedo proporciona lo siguiente:


1.- Un estándar de la industria para la creación y administración central de aplicaciones de transacciones en línea distribuidas en un entorno heterogéneo cliente/servidor.

2.  Facilidad de uso para desarrolladores de aplicaciones, que no necesitan conocer todos los detalles sobre ubicaciones de servidores, enrutamiento o plataformas utilizadas. En una aplicación BEA Tuxedo, estos aspectos de un programa son transparentes.

3.- Los fundamentos fundamentales para crear, administrar y mantener sistemas distribuidos confiables, de alto rendimiento y fáciles de administrar.


Características del sistema BEA Tuxedo
++++++++++++++++++++++++++++++++++++++++++

El sistema BEA Tuxedo ofrece muchas funciones para satisfacer las necesidades del administrador, arquitecto y programador de una aplicación.

**Características administrativas**


Seguridad de contraseña y control de acceso: la seguridad de contraseña permite a los diseñadores de aplicaciones controlar el acceso al requerir contraseñas en el momento de la inicialización (autenticación). El control adicional está disponible a través de la autorización, un medio para restringir el acceso a ciertos servicios de aplicaciones a clientes a los que se les ha otorgado permiso explícito y que tienen identidades autenticadas.

Notificación de eventos del sistema: el sistema BEA Tuxedo proporciona detalles sobre eventos del sistema, como la muerte de servidores y fallas de la red. Cuando un evento es publicado por clientes o servidores, EventBroker busca a todos los suscriptores a ese evento y toma las medidas apropiadas, según lo determine cada suscripción.

La MIB (Base de información de gestión): una interfaz administrativa que le permite supervisar, configurar y ajustar su aplicación a través de sus propios programas. Es una base de datos de administración independiente de la implementación definida como un conjunto de atributos FML, que le permite consultar o cambiar información.

Administración basada en web: una interfaz gráfica de usuario, disponible a través de la World Wide Web, para la configuración y el control de las aplicaciones BEA Tuxedo.


**Características arquitectonicas**


Servicios distribuidos: permiten el acceso transparente a servicios de aplicaciones y/o sistemas ubicados en diferentes plataformas de hardware.

Comunicaciones rápidas y sin conexión: los clientes se conectan a un tablón de anuncios en lugar de a servidores, lo que mejora el rendimiento del sistema.

Escalabilidad: puede escalar rápidamente su aplicación para satisfacer las diferentes demandas de carga del sistema porque los servicios y servidores se pueden replicar y distribuir fácilmente. Puede establecer umbrales mediante programación para permitir que el sistema BEA Tuxedo genere nuevos servidores o apague los servidores automáticamente.

Transparencia del servidor: el directorio de servicios en el tablero de anuncios asigna los nombres de los servicios a los servidores; Los clientes no necesitan conocer la identidad del servidor.


**Funciones de programación**


Técnicas de comunicación: la interfaz de programación de aplicaciones (API) para el sistema BEA Tuxedo es un superconjunto de la interfaz XATMI de X / Open llamada Interfaz de monitor de aplicación a transacción o ATMI. El BEA Tuxedo ATMI es un rico conjunto de técnicas de comunicación para escribir aplicaciones distribuidas.

Procesamiento de transacciones distribuidas (DTP): permite que el trabajo realizado en una aplicación distribuida se complete atómicamente, una característica esencial de cualquier sistema OLTP.

Buffers tipificados: brindan un manejo transparente de los datos de la aplicación en plataformas heterogéneas.

Conformidad X/Open TX: el sistema BEA Tuxedo se ajusta al estándar de interfaz X / Open para la demarcación de transacciones.

Conformidad X/Open XA: el sistema BEA Tuxedo se ajusta al estándar de interfaz X / Open para sistemas de bases de datos de transacciones (llamados gestores de recursos). Como resultado, puede mezclar y combinar bases de datos dentro de una aplicación mientras mantiene la integridad de los datos.
