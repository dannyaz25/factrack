Definición del alcance
Información General
Requerimiento Nº

REV-1106

Objetivo del documento
El presente documento permite capturar la o las necesidades del usuario a través de requerimientos denominados RUSU, que permitirá descomponer la necesidad principal en requerimientos independientes.

Requerimiento de usuario - RUSU-001
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Retirar la instrucción de envío a Wari en los procesos ejecutados desde Factrack:

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Diagrama Interacción Independización FACTRACK.pdf

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
A continuación se detallan los módulos/opciones y perfiles afectados por los cambios a realizar:

Usuarios: Todos los perfiles

Módulo y/o opciones afectados:

Anotación en Cuenta (ACV): Individual, Masivo y Servicio 04006.
Transferencia Contable / Traspaso: Individual, Masivo y Servicio 04005.
Redención de Facturas: Individual, Masivo y Servicio 04007.
Reprogramación de Pagos: Individual, Masivo y Servicio 04009.
Retiro de Facturas: Individual, Masivo y Servicio 04008.
Conformidad de Facturas: Individual, Masivo y Servicio 04010.
Extorno de Facturas: Servicio 04020.
Servicio de registro, anotación y transferencia contable. 04012
Restricciones: Propias al perfil del usuario.

¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Los procesos en Factrack son los siguientes:

En el Proceso de Anotación en Cuenta (ACV):
Se debe desactivar el proceso de Anotación en Cuenta de WARI. Para ello, Factrack ya no debe invocar al método sendAcvInvoicesToWari() y en su lugar invocar directamente al método continueAcv().
En la opción de ACV para administrador, se deberá modificar para que invoque directamente al método continueAcv().

En el Proceso de Transferencia Contable / Traspaso:
Se debe desactivar la Transferencia Contable de WARI. Para ello, Factrack ya no debe invocar al método sendTransferInvoicesToWari() y en su lugar invocar directamente al método continueTransferAccountant().

En el Proceso de Redención de Facturas:
Se debe desactivar en WARI para que la redención se realice sólo por Factrack. Para ello, Factrack ya no debe invocar al método sendRedeemInvoicesToWari() y en su lugar invocar al método continueRedeemInvoice().

En el Proceso de Extorno de Facturas:
Se debe desactivar en WARI, para que el proceso se ejecute sólo en Factrack. Para ello, Factrack ya no debe invocar al método sendReverseProcessToWari() y en su lugar invocar directamente al método continueReverseProcess().

En el Proceso de Reprogramación de Pagos:
Desactivar en WARI para que el proceso se ejecute sólo en Factrack. Para ello, Factrack ya no debe invocar al método sendReshceduleToWari() y en su lugar invocar directamente al método startContinueReschedulePaymentBL().

En el Proceso de Conformidad de Facturas:
Desactivar en WARI para que el proceso se ejecute sólo en Factrack. Para ello, Factrack ya no debe invocar al método sendConformityInvoicesToWari() y en su lugar invocar directamente al método continueConformityProcess().

En el Proceso de Retiro de Facturas:
Desactivar en WARI para que el proceso se ejecute sólo en Factrack. Para ello, Factrack ya no debe invocar al método sendRemoveInvoicesToWari() y en su lugar invocar directamente al método startProcessResponseWariBL().

En el proceso de anotación y transferencia contable (Servicio 04012)
Considerar la adecuaciones necesarias tomando en cuenta el retiro de los métodos de anotación y transferencia contable (punto 1 y 2)


¿Qué otros procesos consideras que estarían relacionados?	
Proceso de Anotación en Cuenta (ACV):
El proceso de Regularización de Titulares (RUSU-003).
Proceso de Transferencia Contable / Traspaso:
El proceso de Regularización de Titulares (RUSU-003).
Prototipo del formulario/reporte	
No aplica



Requerimiento de usuario - RUSU-002
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Independizar las consultas a las tablas WARI a través de la aplicación ServiceCore

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Mapeo GenericWariServiceImpl.xlsx

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
Método utilitarios para consulta de titulares, participantes, etc. de la clase ServiceCore de Fatrack:

getParticipantsList: mediante este método se realiza una consulta de participantes y ser utilizado en la carga de combos.
getHolderByInvoicePK: obtiene el tenedor de una factura mediante el PK de la misma.
validateValue: permite validar si el titular posee saldo disponible.
obtainRutState: permite validar el estado de RUT desde la validación de factura y petición de la transferencia.
getLargeStringFromElement: recupera descripción larga de tabla de parámetros.
Métodos para cálculo de fechas usando feriados registrados en Wari.

addUtilDays: permite aumentar días útiles en base a una fecha.
getDateIsNotHolidayOrWeekend: verifica si un día no es feriado o fin de semana.
getUtilDaysBetweenDates: obtiene días útiles entre dos fechas.
beforeUtilDays: obtiene una nueva fecha, restante días útiles.
¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Sólo los siguientes métodos seguirán siendo usados por los módulos de Factrack pero con adecuaciones a nivel de implementación:

getParticipantsList, utilizado para la consulta de participantes. Será utilizada al iniciar la aplicación Factrack y almacenará la información obtenida en la memoria cache para consultas posteriores.
getHolidaysList, utilizado para obtener la lista de feriados. Será utilizada al iniciar la aplicación Factrack y almacenará la información obtenida en la memoria cache para consultas posteriores.
addUtilDays, getDateIsNotHolidayOrWeekend, getUtilDaysBetweenDates, beforeUtilDays: Se implementará la lógica de los procedimientos/funciones de WARI en métodos incluidos en una clase utilitaria en Factrack, los cuales utilizaran la información almacenada en caché por el método getHolidaysList.
Cabe mencionar que Wari deberá alertar cuando se realice alguna actualización de los participantes o feriados, de manera que se actualice la información de Factrack que se encuentra en cache.

Considerar la actualización de la llamada a estos nuevos métodos en todos los puntos en donde se utilizan actualmente (procesos de anotación, transferencia contable, etc).

Adicionalmente, el método obtainRutState seguirá siendo utilizado en el proceso de Transferencia Contable cuando el RUT sea indicado como dato de entrada.

¿Qué otros procesos consideras que estarían relacionados?	
No Aplica

Prototipo del formulario/reporte	
No Aplica

Requerimiento de usuario - RUSU-003
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Crear el proceso de regularización de titulares

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Actualmente el proceso de Anotación en Cuenta ejecutado desde Factrack: envía el lote de facturas a procesar que luego son atendidas por el bus de integración invocando a los servicios de validación de Wari. En en el caso de que ocurriese algún error en las validaciones de WARI con respecto al titular que albergará el valor asociado a la factura, el servicio retorna un error y bloquea la ejecución del proceso ACV.
¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
Perfiles Administrador y Participante al realizar un proceso de ACV

¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Se va a crear un proceso de regularización de titulares en WARI que recibirá una lista de facturas con números de documento asociados y el proceso origen que lo ejecuta. Este proceso realizará lo siguiente:

Verificar si existe un titular asociado al número de documento.
Si el titular no existe:
Procederá a registrar el titular en estado bloqueado con los mismos valores extraídos actualmente de la factura.
Si el titular existe :
Si el titular se encuentra correcto, procederá a devolver el código RUT y su descripción a Factrack para que sea actualizado en la factura.
Si el titular se encuentra desactualizado (RUC con 8 dígitos) o existe más de un titular para el número de RUC enviado, entonces procederá a notificar al participante que debe regularizar la información de este número de documento y lo colocará en una lista de titulares por regularizar.
Las facturas cuyo legítimo tenedor sea un número de documento incluido en la lista de titulares por regularizar, solo serán considerados para seguir con los procesos de:
Anotación en cuenta.
Transferencia contable / Traspaso,
Retiro
Todos los demás procesos quedan bloqueados para su ejecución,
Importante: No serán consideradas para la generación de constancias de ningún tipo. Para ello, en los módulos correspondientes se deberá agregar una validación relacionada en el procesamiento.
Adicionalmente, se deberá crear un mecanismo que se ejecute en la madrugada de cada día y realice las siguientes actividades:
Verificar si existen números de documento en la lista de titulares por regularizar que ya estén regularizados, de existir procederá a actualizar el RUT y descripción de RUT de las facturas cuyo legítimo tenedor es este número de documento.
Luego, ubicar los números de documento aún no regularizados y registrados el día anterior para actualizarles el estado a "Bloqueado". Adicionalmente, enviará una notificación indicando los titulares que se bloquearon desde este proceso, el detalle se encuentra en el Anexo 11.
Los números de documento en estado “Bloqueado”, tendrán impacto en los siguientes módulos de Factrack:
Registro de Facturas (electrónicas y físicas): No permitirá realizar registros de facturas que tengan como RUC proveedor el número de documento. (Participante, contribuyente)
Registro de Información adicional: No permitirá realizar registro de información adicional en facturas que tengan como RUC proveedor el número de documento. (Participante, contribuyente)
Anotación en Cuenta: No permitirá realizar el ACV de facturas que tengan como RUC proveedor el número de documento.
Transferencia Contable / Traspaso: No permitirá realizar Transferencias Contables / Traspasos en facturas donde el legítimo tenedor es el número de documento.
Re programación, No permitirá realizar la re programación de facturas que tengan como RUC proveedor el número de documento.
Redención, No permitirá realizar la redención de facturas que tengan como RUC proveedor el número de documento.
Solicitud/Generación de constancias (Informativas / Ejecutivas) No permitirá solictar/generar constancias de facturas que tengan como RUC proveedor el número de documento.
Conformidad/Disconformidad: No se permitirá ejecutar la acción solo si la ejecutara el participante, en el caso del contribuyente continua realizando la acción.
Extorno: No permitirá realizar el extorno de facturas que tengan como RUC proveedor el número de documento.
Registro de fecha de comunicación: No permitirá realizar el registro de fecha de facturas que tengan como RUC proveedor el número de documento.
¿Qué otros procesos consideras que estarían relacionados?	
No aplica

Prototipo del formulario/reporte	
No aplica

Requerimiento de usuario - RUSU-004
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Modificar el proceso de transferencia contable

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
El módulo de transferencia contable permite realizar: el registro de solicitud de transferencia agrupando las facturas que se encuentren aptas para ser transferidas (Anotadas en cuenta); la confirmación de las solicitudes registradas para realizar la transferencia contable anotada en cuenta hacia un RUT; y el registro y carga masiva de solicitudes mediante la carga de un archivo excel.

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
Perfiles Administrador y Participante al realizar una confirmación de Transferencia Contable

¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Para atender el traspaso, se está coordinando ajustar el módulo de transferencia contable de la siguiente manera:

P. origen y P. destino	Número de documento	Operación ejecutada	Comentarios
Iguales	No ingresado	Transferencia contable	Se actualiza el RUT del participante como legítimo tenedor
Iguales	Ingresado	Transferencia contable	Se actualiza el RUT ingresado como legítimo tenedor
Distintos	No ingresado	Traspaso	Se actualiza sólo el participante de la factura, el RUT del legítimo tenedor se mantiene
Distintos	Ingresado	Transferencia contable	Se actualiza el participante de la factura y el RUT ingresado como legítimo tenedor
NOTA:

Se debe considerar la actualización de la información histórica de factrack, de manera que la información se encuentre uniforme.
Adicionalmente, el proceso de transferencia contable deberá obtener el RUT del participante destino de la información pre-cargada "en memoria" en el sistema. Esto para los casos en que la transferencia contable (o traspaso) se realice a nombre del RUT del participante.

¿Qué otros procesos consideras que estarían relacionados?	
No aplica

Prototipo del formulario/reporte	No aplica
Requerimiento de usuario - RUSU-005
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Modificar el proceso de anotación en cuenta (ACV) para utilizar el proceso de regularización de titulares

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Actualmente el módulo de anotación en cuenta en WARI valida que si existe un titular asociado al RUC del proveedor, este se encuentre actualizado en su dato "número de documento" (RUC con 11 dígitos). Si no lo está, se bloquea la anotación para dicha factura.

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
El proceso de anotación en cuenta se puede realizar de manera Individual y masiva, éste último mediante la carga de un archivo en formato excel. Siendo los siguientes perfiles que tienen acceso a las opciones mencionadas:

Emisor con cuenta
Participante
Administrador
¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Luego que el proceso de Anotación en Cuenta (ACV) finaliza las validaciones funcionales propias de su competencia (estructura de campos identificadores y no identificadores, estados de la factura, SUNAT), procederá a realizar las siguientes actividades en paralelo:

Invocará el Proceso de Regularización de Titulares (RUSU-003).
Ejecutará la anotación en cuenta en Factrack.
¿Qué otros procesos consideras que estarían relacionados?	
No aplica

Prototipo del formulario/reporte	No aplica
Requerimiento de usuario - RUSU-006
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Modificar el proceso de generación de constancias

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Actualmente la información del titular y el participante que se presenta en las constancias, se obtiene en línea desde WARI.

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
Emisor con cuenta
Participante
Administrador
¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Las constancias que se generan desde factrack, deberán obtener la información de titulares de las tablas de Factrack. Para el caso de los participante los deberá extraer de la lista que se encuentra en memoria, obtenida mediante el RUSU002.

¿Qué otros procesos consideras que estarían relacionados?	
No aplica.

Prototipo del formulario/reporte	No aplica
Requerimiento de usuario - RUSU-007
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Enviar la creación o actualización de datos de titular en WARI hacia Factrack

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Dado que Factrack debe bloquear procesos al no regularizarse un titular, WARI debe indicarle cuando un titular fue regularizado para que Factrack ya no lo considere en el bloqueo.

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
Emisor con cuenta
Participante
Administrador
¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Para atender este requerimiento se deben hacer las modificaciones necesarias a fin que:

WARI informe sobre la actualización de datos/unificación/creación de titulares a Factrack.
Factrack recibe la información y actualice los RUT y descripciones enviadas en las facturas donde los números de documento informados son legítimos tenedores, además para retirar estos números de documento de la lista de titulares por regularizar.
Factrack procede a enviar una notificación al participante confirmando la regularización de los titulares. La estructura se encuentra en el Anexo 12.
¿Qué otros procesos consideras que estarían relacionados?	
No aplica.

Prototipo del formulario/reporte	No aplica
Requerimiento de usuario - RUSU-008
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Verificar que los reportes de contabilidad enviados a la SMV en WARI no generen información de facturas

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Debido a que la información de Factrack ya no estará almacenada en las tablas de WARI, se debe realizar una revisión en los procesos que generan los reportes de contabilidad en WARI para que se certifique que la información no se esté generando dentro de los reportes listados en el detalle.

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
Contabilidad
¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Para atender este requerimiento se deben hacer las validaciones necesarias a fin que:

WARI no muestre la información de facturas al generar los reportes de contabilidad.
Para más detalle se adjuntaron los anexos 2, 3, 4 y 5.

¿Qué otros procesos consideras que estarían relacionados?	
No aplica.

Prototipo del formulario/reporte	Adjunto en los anexos 2, 3, 4 y 5
Requerimiento de usuario - RUSU-009
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Actualizar los procesos que generan los reportes diarios enviados a la SMV en WARI

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Debido a que la información de Factrack ya no estará almacenada en las tablas de WARI, se debe realizar una actualización en los procesos que generan los reportes diarios en WARI para que obtengan esta información

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
Procesos nocturnos
¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Para atender este requerimiento se deben hacer las modificaciones necesarias a fin que:

Factrack reporte la información necesaria en WARI para el envío de los reportes diarios:
SD01: ANOTACIONES EN CUENTA
RT: RETIROS DE FACTURAS
MV: ALTAS Y BAJAS DE VALORES
MC: ALTAS Y BAJAS DE COMITENTES
WARI obtenga la información reportada por Factrack y lo adecúe al generar los reportes diarios.
El detalle de la información enviada en cada archivo se encuentra en el anexo 6.

Un ejemplo de archivo se encuentran en los anexos 7, 8, 9, 10.

¿Qué otros procesos consideras que estarían relacionados?	
No aplica.

Prototipo del formulario/reporte	Adjunto en los anexos 6, 7, 8, 9, 10, 11.
Requerimiento de usuario - RUSU-010
Especificación del Requerimiento	
Descripción del requerimiento del usuario	
Preparar procedimiento para consistencia de información previo al pase a producción del proyecto

Documentación que sustente el cambio (Reglamento, DV, Referencia, Correo, Diagrama del proceso)	
Dado que el proyecto ocasionará que las facturas dejen de enviarse a WARI, se origina la necesidad de ejecutar un procedimiento que deje las facturas consistentes en Factrack y WARI. Adicionalmente, que este procedimiento no afecte el funcionamiento de los reportes diarios enviados a la SMV.

¿Quien y cómo interactúa el formulario/reporte/proceso/módulo?	
Operaciones CVL

¿Cómo crees que debe comportarse el formulario/reporte/proceso/módulo?	
Para atender el requerimiento, se debe crear un procedimiento que considere las siguientes premisas:

Se deben actualizar todas las facturas vivas al estado "Retirado".
Se deben generar los movimientos necesarios en todas las facturas vivas de WARI para indicar que se están retirando a causa del proyecto de independización.
El procedimiento debe dejar la data consistente en WARI de tal modo que no afecte la generación e información enviada en los reportes diarios de la SMV.
El procedimiento debe dejar la data consistente en Factrack para que dichas facturas puedan seguir operando bajo la nueva lógica (independizado).
¿Qué otros procesos consideras que estarían relacionados?	
No aplica.

Prototipo del formulario/reporte	No aplica.
Anexos:
Nro	Nombre	Archivo
1	
Estructura de la notificación para regularización de titulares

[Pendiente]

2	Anexo 02 - SMV - Contabilidad	
Anexo 02_CIBUR006_Mar19.pdf

3	Anexo 04 - SMV - Contabilidad	
Anexo 04_EMV30100R_Mar19.pdf

4	Anexo 05 - SMV - Contabilidad	
Anexo 05_ACV25400R_Mar19.pdf

5	Anexo 06 - SMV - Contabilidad	
Anexo 06_GRT56050R_Mar19.pdf

6	Información de Archivos SMV	
Informacion_archivosSMV.docx

7	SMV: SD01	
SD010610.txt

8	SMV: RT	
RT010510.txt

9	SMV: MV	
MV010610.txt

10	SMV: MC	
MC010610.txt

11	Estructura de la notificación informando sobre los titulares bloqueados por falta de regularización	[Pendiente]
12	Estructura de la notificación que confirma la regularización de un titular	[Pendiente]
