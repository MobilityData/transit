## Referencia de especificación de feed de tránsito general

** Revisado el 17 de enero de 2019. Consulte  [Revision History](../../CHANGES.md) para obtener más detalles. **

Este documento define el formato y la estructura de los archivos que constan de un conjunto de datos GTFS.

## Tabla de contenidos

1.  [Definiciones de términos](#term-definitions)
2.  [Tipos de campo](#field-types)
3.  [Archivos de conjunto de datos](#dataset-files)
4.  [Requisitos de archivo](#file-requirements)
5.  [Definiciones de campo](#field-definitions)
    -   [agency.txt](#agencytxt)
    -   [stops.txt](#stopstxt)
    -   [routes.txt](#routestxt)
    -   [trips.txt](#tripstxt)
    -   [stop\_times.txt](#stop_timestxt)
    -   [calendar.txt](#calendartxt)
    -   [calendar\_dates.txt](#calendar_datestxt)
    -   [fare\_attributes.txt](#fare_attributestxt)
    -   [fare\_rules.txt](#fare_rulestxt)
    -   [shapes.txt](#shapestxt)
    -   [frequencies.txt](#frequenciestxt)
    -   [transfers.txt](#transferstxt)
    -   [pathways.txt](#pathwaystxt)
    -   [levels.txt](#levelstxt)
    -   [feed\_info.txt](#feed_infotxt)

## Definiciones de términos

Esta sección define los términos que se utilizan en este documento.


* ** Conjunto de datos **: un conjunto completo de archivos definidos por esta referencia de especificación. Al modificar el conjunto de datos se crea una nueva versión del conjunto de datos. Los conjuntos de datos deberían publicarse en un URL público y permanente, incluido el nombre del archivo zip. (por ejemplo, https://www.agency.org/gtfs/gtfs.zip).
* ** Registro **: una estructura de datos básica que consta de varios valores de campo diferentes que describen una sola entidad (p. Ej., Agencia de tránsito, parada, ruta, etc.). Representado, en una tabla, como una fila.
* ** Campo ** - Una propiedad de un objeto o entidad. Representado, en una tabla, como una columna.
* ** Valor de campo ** - Una entrada individual en un campo. Representado, en una tabla, como una sola celda.
* **Obligatorio** - El campo hay que incluirse en el conjunto de datos, y hay que proporcionarse un valor en ese campo para cada registro. Algunos campos obligatorios permiten una cadena vacía como un valor (indicado en esta especificación como vacío). Para ingresar una cadena vacía, simplemente omita cualquier texto entre las comas para ese campo.
* ** Opcional ** - El campo se puede omitir del conjunto de datos. Si se incluye una columna opcional, algunas de las entradas en ese campo pueden ser cadenas vacías. Para introducir una cadena vacía, simplemente omita cualquier texto entre las comas para ese campo. Tenga en cuenta que un campo omitido es equivalente a un campo que está completamente vacío.
* ** Condicionalmente requerido ** - El campo o archivo se requiere bajo ciertas condiciones, que se describen en el campo o descripción del archivo. Fuera de estas condiciones, este campo o archivo es opcional.
* ** Día de servicio **: un día de servicio es un período de tiempo utilizado para indicar la programación de la ruta. La definición exacta del día de servicio varía de agencia a agencia, pero los días de servicio a menudo no se corresponden con los días calendario. Un día de servicio puede exceder las 24:00:00 si el servicio comienza en un día y termina en el día siguiente. Por ejemplo, el servicio que se extiende desde las 08:00:00 del viernes a las 02:00:00 del sábado, se puede denotar como de 08:00:00 a 26:00:00 en un solo día de servicio.

## Tipos de campo

- ** Color ** - Un color codificado como un número hexadecimal de seis dígitos. Consulte [https://htmlcolorcodes.com](https://htmlcolorcodes.com) para generar un valor válido (el "#" inicial no está incluido). <br> * Ejemplo: `FFFFFF` para blanco,` 000000` para negro o `0039A6` para las líneas A, C, E en NYMTA. *
- ** Código de moneda ** - Un código de moneda alfabético ISO 4217. Para obtener la lista de la moneda actual, consulte [https://en.wikipedia.org/wiki/ISO_4217#Active\_codes](https://en.wikipedia.org/wiki/ISO_4217#Active_codes). <br> * Ejemplo: `CAD` para dólares canadienses,` EUR` para euros o `JPY` para yenes japoneses. *
- ** Fecha ** - Día de servicio en el formato AAAAMMDD. Dado que el tiempo dentro de un día de servicio puede ser superior a las 24:00:00, un día de servicio a menudo contiene información para los días posteriores. <br> * Ejemplo: `20180913` para el 13 de septiembre de 2018. *
- ** Correo electrónico ** - Una dirección de correo electrónico. <br> * Ejemplo: `example@example.com`*
- ** Enum **: una opción de un conjunto de constantes predefinidas delimitadas en la columna "Descripción". <br> * Ejemplo: el campo `route_type` contiene un` 0` para el tranvía, un `1` para el metro ... *
- ** ID **: una secuencia de cualquier carácter UTF-8 que identifica de forma única a una entidad, pero no identifica necesariamente un registro específico en una tabla. Las identificaciones definidas en un archivo .txt a menudo se mencionan en otro archivo .txt. Un valor de campo de ID no está destinado a ser mostrado al usuario, y es una secuencia de cualquier carácter UTF-8, pero se recomienda usar solo caracteres ASCII imprimibles. <br> * Ejemplo: el campo `stop_id` en [stops.txt] (# stopstxt) es una ID. El campo `stop_id` en [stop_times.txt] (# stop_timestxt) es una ID que hace referencia a` stops.stop_id`. *
- ** Código de idioma ** - Un código de idioma IETF BCP 47. Para una introducción a IETF BCP 47, consulte [http://www.rfc-editor.org/rfc/bcp/bcp47.txt](http://www.rfc-editor.org/rfc/bcp/bcp47.txt) y  [http://www.w3.org/International/articles/language-tags/](http://www.w3.org/International/articles/language-tags/). <br> * Ejemplo: `en` para inglés,` en-US` para inglés americano o `de` para alemán. *
- ** Latitud ** -  Latitud WGS84 en grados decimales. El valor tiene que ser mayor o igual a -90.0 y menor o igual a 90.0. * <br> Ejemplo: `41.890169` para el Coliseo en Roma. *
- ** Longitud ** - Longitud WGS84 en grados decimales. El valor tiene que ser mayor o igual a -180.0 y menor o igual a 180.0. <br> * Ejemplo: `12.492269` para el Coliseo en Roma. *
- ** Punto flotante no negativo ** - Un número de punto flotante mayor o igual a 0.
- ** Entero no negativo ** - Un entero mayor o igual a 0.
- ** Número de teléfono ** - Un número de teléfono.
- ** Hora ** - Hora en el formato HH: MM: SS (H: MM: SS también se acepta). El tiempo se mide desde el "mediodía menos las 12" del día de servicio (efectivamente, es medianoche, excepto los días en que ocurren los cambios de horario de verano). Para las horas posteriores a la medianoche, introduzca la hora como un valor mayor que 24:00:00 en HH: MM: SS hora local para el día en que comienza el programa de viaje. <br> * Ejemplo: `14: 30: 00` para las 2:30 PM o` 25: 35: 00` para la 1:35 AM del día siguiente. *
- ** Texto ** - Una cadena de caracteres UTF-8, cuyo objetivo es mostrarse y que, por lo tanto, tiene que ser legible por humanos.
- ** Zona horaria **: TZ zona horaria de la [https://www.iana.org/time-zones](https://www.iana.org/time-zones). Los nombres de la zona horaria nunca contienen el carácter de espacio, pero pueden contener un guión bajo. Consulte [http://en.wikipedia.org/wiki/List\_of\_tz\_zones](http://en.wikipedia.org/wiki/List\_of\_tz\_zones) para obtener una lista de valores válidos . <br> * Ejemplo: `Asia / Tokyo`,` America / Los_Angeles` o `Africa / Cairo`. *
- ** URL **: una URL que cumple los requisitos que incluye http: // o https: //, y cualquier carácter especial en la URL tiene que ser escapado correctamente. Consulte la siguiente [http://www.w3.org/Addressing/URL/4\_URI\_Recommentations.html](http://www.w3.org/Addressing/URL/4\_URI\_Recommentations.html) para una descripción de cómo crear valores de URL que cumplen los requisitos.

## Archivos de conjunto de datos

Esta especificación define los siguientes archivos:

|  Nombre del archivo | Obligatorio | Define |
|  ------ | ------ | ------ |
|  [agency.txt](#agencytxt) | **Obligatorio** | Agencias de tránsito con servicio representado en este conjunto de datos. |
|  [stops.txt](#stopstxt) | **Obligatorio** | Paradas donde los vehículos recogen o dejan a los pasajeros. También define estaciones y entradas de estaciones.  |
|  [routes.txt](#routestxt) | **Obligatorio** | Rutas de tránsito. Una ruta es un grupo de viajes que se muestran a los pasajeros como un solo servicio. |
|  [trips.txt](#tripstxt)  | **Obligatorio** | Viajas para cada ruta. Un viaje es una secuencia de dos o más paradas que se producen durante un período de tiempo específico. |
|  [stop_times.txt](#stop_timestxt)  | **Obligatorio** | Horas en que un vehículo llega y sale de las paradas para cada viaje. |
|  [calendar.txt](#calendartxt)  | ** Condicionalmente Obligatorio ** | Fechas de servicio especificadas utilizando un horario semanal con fechas de inicio y finalización. Este archivo es obligatorio a menos que todas las fechas de servicio estén definidas en [calendar_dates.txt] (# calendar_datestxt). |
|  [calendar_dates.txt](#calendar_datestxt)  | ** Condicionalmente Obligatorio ** | Excepciones para los servicios definidos en [calendar.txt](#calendartxt). Si se omite [calendar.txt](#calendartxt), entonces [calendar_dates.txt](# calendar_datestxt) es obligatorio y tiene que contener todas las fechas de servicio. |
|  [fare_attributes.txt](#fare_attributestxt)  | Opcional | Información de tarifas para las rutas de una agencia de tránsito. |
|  [fare_rules.txt](#fare_rulestxt)  | Opcional | Reglas de aplicación de tarifas para itinerarios. |
|  [shapes.txt](#shapestxt)  | Opcional | Reglas para trazar rutas de viaje de vehículos, a veces denominadas alineamientos de rutas. |
|  [frequencies.txt](#frequenciestxt)  | Opcional | Avance (tiempo entre viajes) para el servicio basado en el avance o una representación comprimida del servicio de horario fijo. |
|  [transfers.txt](#transferstxt)  | Opcional | Reglas para realizar conexiones en puntos de transferencia entre rutas. |
|  [pathways.txt](#pathwaystxt)  | Opcional | Caminos que unen ubicaciones dentro de las estaciones. |
|  [levels.txt](#levelstxt)  | Opcional | Niveles dentro de las estaciones. |
|  [feed_info.txt](#feed_infotxt)  | Opcional | Metadatos del conjunto de datos, incluidos el editor, la versión y la información de caducidad. |

## Requisitos de archivo

Los siguientes requisitos se aplican al formato y contenido de los archivos de conjunto de datos:

* Todos los archivos hay que guardarse como texto delimitado por comas.
* La primera línea de cada archivo tiene que contener nombres de campo. Cada subsección de la sección [Field Definitions](#field-definitions) corresponde a uno de los archivos en un conjunto de datos GTFS y enumera los nombres de campo que se pueden usar en ese archivo.
* Todos los nombres de campo son sensibles al uso de mayúsculas y minúsculas.
* Los valores de campo no pueden contener tabuladores, retornos de carro o nuevas líneas.
* Los valores de campo que contienen comillas o comas tiene que estar entre comillas. Además, cada comilla en el valor de campo tiene que ir precedida de una comilla. Esto es consistente con la manera en que Microsoft Excel genera archivos delimitados por comas (CSV). Para obtener más información sobre el formato de archivo CSV, consulte [http://tools.ietf.org/html/rfc4180](http://tools.ietf.org/html/rfc4180).
El siguiente ejemplo muestra cómo aparecería un valor de campo en un archivo delimitado por comas:
  * ** Valor de campo original :** `Contains "quotes", commas and text`
  * **Valor de campo en un archivo CSV:** `"Contains ""quotes"", commas and text"`
* Los valores de campo no deben contener etiquetas HTML, comentarios o secuencias de escape.
* Eliminar cualquier espacio extra entre campos o nombres de campo. Muchos parseadores consideran que los espacios son parte del valor, lo que puede causar errores.
* Cada línea tiene que terminar con un carácter de salto de línea CRLF o LF.
* Los archivos deberían estar codificados en UTF-8 para admitir todos los caracteres Unicode. Los archivos que incluyen el carácter de marca de orden de bytes (BOM) de Unicode son aceptables. Consulte  [http://unicode.org/faq/utf_bom.html#BOM](http://unicode.org/faq/utf_bom.html#BOM) para obtener más información sobre el carácter de la lista de materiales y UTF-8.
* Todos los archivos de datos tienen que estar comprimidos juntos.

## Definiciones de campo

### agency.txt

Archivo: ** Obligatorio **

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `agency_id` | ID | ** Condicionalmente Obligatorio ** | Identifica una marca de tránsito que a menudo es sinónimo de una agencia de tránsito. Tenga en cuenta que en algunos casos, como cuando una sola agencia opera múltiples servicios separados, las agencias y las marcas son distintas. Este documento utiliza el término "agencia" en lugar de "marca". Un conjunto de datos puede contener datos de múltiples agencias. Este campo es obligatorio cuando el conjunto de datos contiene datos para varias agencias de tránsito, de lo contrario es opcional. |
|  `agency_name` | Texto | **Obligatorio** | Nombre completo de la agencia de tránsito. |
|  `agency_url` | URL | **Obligatorio** | URL de la agencia de tránsito. |
|  `agency_timezone` | Zona horaria | **Obligatorio** | Zona horaria donde se encuentra la agencia de tránsito. Si se especifican varias agencias en el conjunto de datos, cada una tiene que tener el mismo `agency_timezone`. |
|  `agency_lang` | Código de idioma | Opcional | Idioma primario utilizado por esta agencia de tránsito. Este campo ayuda a los consumidores de GTFS a elegir reglas de capitalización y otras configuraciones específicas de idioma para el conjunto de datos. |
|  `agency_phone` | Número de teléfono | Opcional | Un número de teléfono de voz para la agencia especificada. Este campo es un valor de cadena que presenta el número de teléfono como típico para el área de servicio de la agencia. Puede y debería contener signos de puntuación para agrupar los dígitos del número. Se permite el texto que se puede marcar (por ejemplo, "503-238-RIDE" de TriMet), pero el campo no debe contener ningún otro texto descriptivo. |
|  `agency_fare_url` | URL | Opcional | URL de una página web que le permite a un usuario comprar billetes u otros instrumentos de tarifas para esa agencia en línea. |
|  `agency_email` | Correo eléctronico | Opcional | Dirección de correo electrónico supervisada activamente por el departamento de servicio al cliente de la agencia. Esta dirección de correo electrónico debería ser un punto de contacto directo donde los pasajeros de tránsito pueden comunicarse con un representante de servicio al cliente en la agencia. |

### stops.txt

Archivo: ** Obligatorio **

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `stop_id` | ID | **Obligatorio** | Identifica una parada, estación o entrada de estación. El término "entrada de la estación" se refiere tanto a las entradas como a las salidas de la estación. Las paradas, estaciones o entradas de estación se denominan colectivamente ubicaciones. Múltiples rutas pueden usar la misma parada. |
|  `stop_code` | Texto | Opcional | Texto corto o un número que identifica la ubicación de los pasajeros. Estos códigos se usan a menudo en sistemas de información de tránsito basados en el teléfono o impresos en carteles para facilitar que los pasajeros obtengan información para una ubicación en particular. El `stop_code` puede ser el mismo que` stop_id` si es público. Este campo debería dejarse vacío para las ubicaciones sin un código presentado a los usuarios. |
|  `stop_name` | Texto | ** Condicionalmente Obligatorio ** | Nombre de la ubicación. Use un nombre que la gente entienda en el idioma local y turístico. <br><br> Cuando la ubicación es un área de embarque (`location_type = 4`), el` stop_name` debería contener el nombre del área de embarque tal como lo muestra la agencia. Podría ser solo una letra (como en algunas estaciones ferroviarias interurbanas europeas), o un texto como"Área de embarque en silla de ruedas" (Metro de Nueva York) o "Jefe de trenes cortos" (RER de París). <br><br> Condicionalmente obligatorio: <br> • ** Obligatorio ** para ubicaciones que son paradas (`location_type = 0`), estaciones (` location_type = 1`) o entradas / salidas (`location_type = 2`). <br> • Opcional para ubicaciones que son nodos genéricos (`location_type = 3`) o áreas de embarque (` location_type = 4`).|
|  `stop_desc` | Texto | Opcional | Descripción de la ubicación que proporciona información útil y de calidad. No se limite a duplicar el nombre de la ubicación. |
|  `stop_lat` | Latitud | ** Condicionalmente Obligatorio ** | Latitud de la ubicación. <br><br> Condicionalmente obligatorio: <br> • ** Obligatorio ** para ubicaciones que son paradas (`location_type = 0`), estaciones (` location_type = 1`) o entradas / salidas (`location_type = 2`). <br> • Opcional para ubicaciones que son nodos genéricos (`location_type = 3`) o áreas de embarque (` location_type = 4`).|
|  `stop_lon` | Longitud | ** Condicionalmente Obligatorio ** | Longitud de la ubicación. <br><br> Condicionalmente obligatorio: <br> • ** Obligatorio ** para ubicaciones que son paradas (`location_type = 0`), estaciones (` location_type = 1`) o entradas / salidas (`location_type = 2`). <br> • Opcional para ubicaciones que son nodos genéricos (`location_type = 3`) o áreas de embarque (` location_type = 4`). | 
|  `zone_id` | ID | ** Condicionalmente Obligatorio ** | Identifica la zona de tarifa para una parada. Este campo es obligatorio si se proporciona información sobre tarifas usando [fare_rules.txt] (# fare_rulestxt), de lo contrario es opcional. Si este registro representa una entrada de estación o estación, el `zone_id` se ignora. |
|  `stop_url` | URL | Opcional | URL de una página web sobre la ubicación. Esto debería ser diferente de los valores de campo `agency.agency_url` y` route.route_url`. |
|  `location_type` | Enum | Opcional | Tipo de lugar: <br> • `0` (o en blanco): ** Parado ** (o ** Andén **). Un lugar donde los pasajeros suben o bajan de un vehículo de tránsito. Se llama andén cuando se define dentro de una `parent_station`. <br> • `1`: ** Estación **. Una estructura física o área que contiene uno o más andenes. <br> • `2`: ** Entrada / Salida **. Un lugar donde los pasajeros pueden entrar o salir de una estación desde la calle. Si una entrada / salida pertenece a varias estaciones, se puede vincular por caminos a ambas, pero el proveedor de datos tiene que elegir una de ellas como principal. <br> • `3`: ** Nodo Genérico **. Una ubicación dentro de una estación, que no coincide con ningún otro `location_type`, que se puede usar para enlazar caminos definidos en pathways.txt. <br> • `4`: ** Área de embarque **. Una ubicación específica en un andén, donde los pasajeros pueden abordar y / o descender de vehículos.|
|  `parent_station` | ID que hace referencia a `stops.stop_id` | ** Condicionalmente Obligatorio ** | Define la jerarquía entre las diferentes ubicaciones definidas en `stops.txt`. Contiene el ID de la ubicación principal, como se indica a continuación: <br> • ** Parada / andén ** (`location_type = 0`): el campo` parent_station` contiene el ID de una estación. <br> • ** Estación ** (`location_type = 1`): este campo tiene que estar vacío. <br> • ** Entrada / salida ** (`location_type = 2`) o ** nodo genérico ** (` location_type = 3`): el campo `parent_station` contiene el ID de una estación (` location_type = 1`) <br> • ** Área de embarque ** (`location_type = 4`): el campo` parent_station` contiene el ID de una plataforma. <br><br> Condicionalmente obligatorio: <br> • ** Obligatorio ** para ubicaciones que son entradas (`location_type = 2`), nodos genéricos (` location_type = 3`) o áreas de embarque (`location_type = 4`). <br> • Opcional para paradas / plataformas (`location_type = 0`). <br> • Prohibido para estaciones (`location_type = 1`).|
|  `stop_timezone` | Zona horaria | Opcional | Zona horaria de la ubicación. Si la ubicación tiene una estación principal, hereda la zona horaria de la estación principal en lugar de aplicar su propia estación. Las estaciones y las paradas sin padres con `stop_timezone` vacío heredan la zona horaria especificada por` agency.agency_timezone`. Si se proporcionan valores de `stop_timezone`, los tiempos en [stop_times.txt] (# stop_timetxt) deberían ingresarse como el tiempo desde la medianoche en la zona horaria especificada por` agency.agency_timezone`. Esto asegura que los valores de tiempo en un viaje siempre aumenten en el transcurso de un viaje, independientemente de las zonas horarias en las que el viaje se cruce. |
|  `wheelchair_boarding` | Enum | Opcional | Indica si es posible realizar abordajes con silla de ruedas desde la ubicación. Las opciones válidas son: <br><br> Para paradas huérfanas: <br> `0` o vacío - No hay información de accesibilidad para la parada. <br> `1` - Un pasajero en silla de ruedas puede subirse a algunos vehículos en esta parada. <br> `2` - El embarque en silla de ruedas no es posible en esta parada. <br><br> Para paradas hijas: <br> `0` o vacío: la parada heredará su comportamiento` wheelchair_boarding` de la estación principal, si se especifica en la principal. <br> `1` - Existe algún camino accesible desde fuera de la estación hasta la parada / plataforma específica. <br> `2` - No existe un camino accesible desde el exterior de la estación hasta la parada / plataforma específica. <br><br> Para entradas / salidas de la estación: <br> `0` o vacío: la entrada de la estación heredará su comportamiento` wheelchair_boarding` de la estación principal, si se especifica para la principal. <br> `1` - La entrada a la estación es accesible para sillas de ruedas. <br> `2` - No hay camino accesible desde la entrada de la estación hasta las paradas / plataformas. |
|  `level_id` | ID que hace referencia a `levels.level_id` | Opcional | Nivel de la ubicación. El mismo nivel puede ser usado por múltiples estaciones no vinculadas.|
|  `platform_code` | Texto | Opcional | Identificador de plataforma para una parada de plataforma (una parada que pertenece a una estación). Este debería ser solo el identificador de la plataforma (por ejemplo, "3"). No se deberían incluir palabras como "plataforma" o "seguimiento" (o el equivalente específico del idioma del feed). Esto permite a los consumidores de feeds internacionalizar y localizar más fácilmente el identificador de la plataforma en otros idiomas. |  

### routes.txt

Archivo: ** Obligatorio **

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `route_id` | ID | **Obligatorio** | Identifica una ruta. |
|  `agency_id` | ID que hace referencia a `agency.agency_id` | ** Condicionalmente Obligatorio ** | Agencia para la ruta especificada. Este campo es obligatorio cuando el conjunto de datos proporciona datos para rutas de más de una agencia en [agency.txt] (# agencia), de lo contrario es opcional.  |
|  `route_short_name` | Texto | ** Condicionalmente Obligatorio ** | Nombre abreviado de una ruta. A menudo, este será un identificador corto y abstracto como "32", "100X", o "Verde" que los usuarios utilizan para identificar una ruta, pero que no da ninguna indicación de a qué lugares sirve la ruta. Hay que especificar `route_short_name` o` route_long_name`, o potencialmente ambos si es apropiado. |
|  `route_long_name` | Texto | ** Condicionalmente Obligatorio ** | Nombre completo de una ruta. Este nombre es generalmente más descriptivo que el `route_short_name` y con frecuencia incluye el destino o la parada de la ruta. Hay que especificar `route_short_name` o` route_long_name`, o potencialmente ambos si es apropiado. |
|  `route_desc` | Texto | Opcional | Descripción de una ruta que proporciona información útil y de calidad. No se limite a duplicar el nombre de la ruta. <hr> _Ejemplo: los trenes "A" operan entre Inwood-207 St, Manhattan y Far Rockaway-Mott Avenue, Queens en todo momento. Además, desde aproximadamente las 6:00 am hasta aproximadamente la medianoche, los trenes "A" adicionales operan entre Inwood-207 St y Lefferts Boulevard (los trenes se alternan entre Lefferts Blvd y Far Rockaway)._ |
|  `route_type` | Enum | **Obligatorio** | Indica el tipo de transporte utilizado en una ruta. Las opciones válidas son: <br><br> `0` - Tranvía, Metro ligero. Cualquier tren ligero o sistema de nivel de calle dentro de un área metropolitana. <br> `1` - Metro. Cualquier sistema ferroviario subterráneo dentro de un área metropolitana. <br> `2` - Carril. Utilizado para viajes interurbanos o de larga distancia. <br> `3` - Autobús. Utilizado para rutas de autobuses de corta y larga distancia. <br> `4` - Ferry. Utilizado para servicio de embarcaciones de corta y larga distancia. <br> `5` - Teleférico. Se utiliza para los tranvías a nivel de la calle donde el cable corre debajo del automóvil. <br> `6` - Góndola, teleférico suspendido. Normalmente se utiliza para los teleféricos en los que el automóvil está suspendido del cable. <br> `7` - Funicular. Cualquier sistema de carril diseñado para pendientes pronunciadas. |
|  `route_url` | URL | Opcional | URL de una página web sobre la ruta en particular. Debería ser diferente del valor `agency.agency_url`. |
|  `route_color` | Color | Opcional | Designación del color de la ruta que coincida con el material de cara al público. El valor predeterminado es blanco (`FFFFFF`) cuando se omite o se deja vacío. La diferencia de color entre `route_color` y` route_text_color` debería proporcionar suficiente contraste cuando se ve en una pantalla en blanco y negro. |
|  `route_text_color` | Color | Opcional | Color legible para usar con el texto dibujado sobre un fondo de `route_color`. El valor predeterminado es negro (`000000`) cuando se omite o se deja vacío. La diferencia de color entre `route_color` y` route_text_color` debería proporcionar suficiente contraste cuando se ve en una pantalla en blanco y negro. |
|  `route_sort_order` | Entero no negativo | Opcional | Ordena las rutas de una manera que sea ideal para la presentación a los clientes. Las rutas con valores de `route_sort_order` más pequeños deberían mostrarse primero. |

### trips.txt

Archivo: ** Obligatorio **

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `route_id` | ID que hace referencia a a `route.route_id` | **Obligatorio** | Identifica una ruta. |
|  `service_id` | ID que hace referencia a `calendar.service_id` o` calendar_dates.service_id` | **Obligatorio** | Identifica un conjunto de fechas cuando el servicio está disponible para una o más rutas. |
|  `trip_id` | ID | **Obligatorio** | Identifica un viaje. |
|  `trip_headsign` | Texto | Opcional | Texto que aparece en la señalización que identifica el destino del viaje para los pasajeros. Utilice este campo para distinguir entre diferentes patrones de servicio en la misma ruta. Si el encabezado cambia durante un viaje, se puede anular `trip_headsign` especificando valores para el `stop_times.stop_headsign`. |
|  `trip_short_name` | Texto | Opcional | El texto público que se usa para identificar el viaje a los pasajeros, por ejemplo, para identificar los números de trenes para los viajes en tren de cercanías. Si los usuarios no suelen depender de los nombres de los viajes, deje este campo en blanco. Un valor `trip_short_name`, si se proporciona, debería identificar de forma única un viaje dentro de un día de servicio; no debería utilizarse para nombres de destino o designaciones limitadas / expresas. |
|  `direction_id` | Enum | Opcional | Indica la dirección de viaje para un viaje. Este campo no se utiliza en el enrutamiento; proporciona una manera de separar los viajes por dirección al publicar horarios. Las opciones válidas son: <br><br> `0` - Viaje en una dirección (por ejemplo, viaje de ida). <br> `1` - Viajar en la dirección opuesta (por ejemplo, viajes de entrada). <hr> * Ejemplo: Los campos `trip_headsign` y` direction_id` podrían usarse juntos para asignar un nombre para viajar en cada dirección para un conjunto de viajes. Un archivo [trips.txt] (# tripstxt) podría contener estos registros para su uso en horarios: * <br> `trip_id, ..., trip_headsign, direction_id` <br> `1234, ..., Aeropuerto, 0` <br> `1505, ..., Downtown, 1` |
|  `block_id` | ID | Opcional | Identifica el bloque al que pertenece el viaje. Un bloque consiste en un solo viaje o varios viajes secuenciales realizados con el mismo vehículo, definidos por días de servicio compartido y `block_id`. Un `block_id` puede tener viajes con diferentes días de servicio, haciendo bloques distintos. Vea el [ejemplo a continuación] (# example-blocks-and-service-day) |
|  `shape_id` | ID que hace referencia a `shapes.shape_id` | Opcional | Identifica una forma geoespacial que describe la trayectoria de viaje del vehículo para un viaje. |
|  `wheelchair_accessible` | Enum | Opcional | Indica accesibilidad para silla de ruedas. Las opciones válidas son: <br><br> `0` o vacío - No hay información de accesibilidad para el viaje. <br> `1`: el vehículo que se usa en este viaje en particular puede acomodar al menos a un jinete en una silla de ruedas. <br> `2` - No se pueden acomodar pasajeros en silla de ruedas en este viaje. |
|  `bikes_allowed` | Enum | Opcional | Indica si las bicicletas están permitidas. Las opciones válidas son: <br><br> `0` o vacío - No hay información de la bicicleta para el viaje. <br> `1` - El vehículo que se usa en este viaje en particular puede acomodar al menos una bicicleta. <br> `2` - No se permiten bicicletas en este viaje. |

#### Ejemplo: Bloques y día de servicio.

El siguiente ejemplo es válido, con distintos bloques todos los días de la semana.

| route_id | trip_id | service_id                     | block_id | <span style="font-weight:normal">* (horario de la primera parada) *</span> | <span style="font-weight:normal">* (horario de la última parada) *</span> |
|----------|---------|--------------------------------|----------|-----------------------------------------|-------------------------|
| rojo      | trip_1  | L-M-X-J-V-S-D | red_loop | 22:00:00                                | 22:55:00                |
| red      | trip_2  | V-S-D                    | red_loop | 23:00:00                                | 23:55:00                |
| red      | trip_3  | V-S                        | red_loop | 24:00:00                                | 24:55:00                |
| red      | trip_4  | L-M-X-J             | red_loop | 20:00:00                                | 20:50:00                |
| red      | trip_5  | L-M-X-J             | red_loop | 21:00:00                                | 21:50:00                |

Notas sobre la tabla anterior:

* El viernes hasta la mañana del sábado, por ejemplo, un solo vehículo opera `trip_1`,` trip_2` y `trip_3` (10:00 PM a 12:55 AM). Tenga en cuenta que el último viaje se realiza el sábado de 12:00 a.m. a 12:55 a.m., pero es parte del "día de servicio" del viernes porque los horarios son de 24:00 a 24:55.
* Los lunes, martes, miércoles y jueves, un solo vehículo opera `trip_1`,` trip_4` y `trip_5` en un bloque de 8:00 PM a 10:55 PM.

### stop_times.txt

Archivo: ** Obligatorio **

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `trip_id` | ID que hace referencia a a `trips.trip_id` | **Obligatorio** | Identifica un viaje.  |
|  `arrival_time` | Hora | ** Condicionalmente Obligatorio ** | Hora de llegada a una parada específica para un viaje específico en una ruta. Si no hay tiempos separados para la llegada y la salida en una parada, introduzca el mismo valor para `arrival_time` y` starting_time`. Para las horas que se producen después de la medianoche del día de servicio, introduzca la hora como un valor mayor que 24:00:00 en HH: MM: SS hora local para el día en que comienza el programa de viaje. <br/><br/> Las paradas programadas donde el vehículo se adhiere estrictamente a los tiempos de llegada y salida especificados son los puntos de tiempo. Si esta parada no es un punto de tiempo, deje vacío `arrival_time` o proporcione un tiempo interpolado. Además, indique que los tiempos interpolados se proporcionan con `timepoint` =` 0`. Si los tiempos interpolados se indican con `timepoint` =` 0`, entonces los puntos de tiempo tienen que indicarse con `timepoint` =` 1`. Proporcione tiempos de llegada para todas las paradas que son puntos de tiempo. Hay que especificar una hora de llegada para la primera y la última parada de un viaje. |
|  `starting_time` | Hora | ** Condicionalmente Obligatorio ** | Hora de salida desde una parada específica para un viaje específico en una ruta. Para las horas que se producen después de la medianoche del día de servicio, ingrese la hora como un valor mayor que 24:00:00 en HH: MM: SS hora local para el día en que comienza el programa de viaje. Si no hay tiempos separados para la llegada y la salida en una parada, introduzca el mismo valor para `arrival_time` y` starting_time`. Consulte la descripción de `arrival_time` para obtener más detalles sobre el uso correcto de los puntos de tiempo. |
|  `stop_id` | ID que hace referencia a `stops.stop_id` | **Obligatorio** | Identifica la parada con servicio. Todas las paradas con servicio durante un viaje deben tener un registro en [stop_times.txt] (# stop_timestxt). Las ubicaciones a las que se hace referencia deben ser paradas, no estaciones o entradas de estaciones. Una parada puede recibir servicio varias veces en el mismo viaje, y múltiples viajes y rutas pueden operar en la misma parada. |
|  `stop_sequence` | Entero no negativo | **Obligatorio** | Orden de paradas para un viaje particular. Los valores deben aumentar a lo largo del viaje pero no necesitan ser consecutivos. <hr> * Ejemplo: la primera ubicación en el viaje podría tener un `stop_sequence` =` 1`, la segunda ubicación en el viaje podría tener un `stop_sequence` =` 23`, la tercera ubicación podría tener un `stop_sequence` =` 40` , y así.* |
|  `stop_headsign` | Texto | Opcional | Texto que aparece en la señalización que identifica el destino del viaje para los pasajeros. Este campo anula el valor predeterminado `trips.trip_headsign` cuando el encabezado cambia entre paradas. Si se muestra el encabezado para un viaje completo, use `trips.trip_headsign` en su lugar. |
|  `pickup_type` | Enum | Opcional | Indica el método de recogida. Las opciones válidas son: <br><br> `0` o vacío - recogida programada regularmente. <br> `1` - No hay recogida disponible. <br> `2` - Debe llamar a la agencia para organizar la recogida. <br> `3` - Debe coordinar con el conductor para organizar la recogida. |
|  `drop_off_type` | Enum | Opcional | Indica el método de entrega. Las opciones válidas son: <br><br> `0` o vacío: entrega regular programada. <br> `1` - No hay entrega disponible. <br> `2` - Debe llamar a la agencia para organizar la entrega. <br> `3` - Debe coordinar con el conductor para organizar la entrega. |
|  `shape_dist_traveled` | Punto flotante no negativo | Opcional | Distancia real recorrida a lo largo de la ruta, desde la primera parada hasta la parada especificada en este registro. Este campo especifica la cantidad de la ruta que se recorre entre dos paradas durante un viaje. Debe estar en las mismas unidades utilizadas en [shapes.txt] (# shapestxt). Los valores utilizados para `shape_dist_traveled` deben aumentar junto con` stop_sequence`; no se pueden utilizar para mostrar el recorrido inverso a lo largo de una ruta. <hr> * Ejemplo: si un autobús recorre una distancia de 5.25 kilómetros desde el inicio de la forma hasta la parada, `shape_dist_traveled` =` 5.25`. *|
|  `timepoint` | Enum | Opcional | Indica si el vehículo respeta estrictamente los tiempos de llegada y salida para una parada o si son tiempos aproximados y / o interpolados. Este campo permite que un productor de GTFS proporcione paradas interpoladas, mientras que indica que los tiempos son aproximados. Las opciones válidas son: <br><br> `0` - Los tiempos se consideran aproximados. <br> `1` o vacío: los tiempos se consideran exactos. |


### calendar.txt

Archivo: ** Condicionalmente Obligatorio **

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ |------ |
|  `service_id` | ID | **Obligatorio** | Identifica de forma exclusiva un conjunto de fechas cuando el servicio está disponible para una o más rutas. Cada valor `service_id` puede aparecer como máximo una vez en un archivo [calendar.txt] (# calendartxt). |
|  `monday` | Enum | **Obligatorio** | Indica si el servicio funciona todos los lunes en el intervalo de fechas especificado por los campos `start_date` y` end_date`. Tenga en cuenta que las excepciones para fechas particulares pueden aparecer en [calendar_dates.txt] (# calendar_datestxt). Las opciones válidas son: <br><br> `1` - El servicio está disponible para todos los lunes en el rango de fechas. <br> `0` - El servicio no está disponible para los lunes en el rango de fechas. |
|  `tuesday` | Enum | **Obligatorio** | Funciona de la misma manera que `monday` excepto que se aplica a los martes |
|  `wednesday` | Enum | **Obligatorio** | Funciona de la misma manera que `monday` excepto que se aplica a los miércoles  |
|  `thursday` | Enum | **Obligatorio** | Funciona de la misma manera que `monday` excepto que se aplica a los jueves  |
|  `friday` | Enum | **Obligatorio** | Funciona de la misma manera que `monday` excepto que se aplica a los viernes  |
|  `saturday` | Enum | **Obligatorio** | Funciona de la misma manera que `monday` excepto que se aplica a los sábados |
|  `sunday` | Enum | **Obligatorio** | Funciona de la misma manera que `monday` excepto que se aplica a los domingos |
|  `start_date` | Fecha | **Obligatorio** | Día del inicio de servicio para el intervalo de servicio. |
|  `end_date` | Fecha | **Obligatorio** | Día de finalización del servicio para el intervalo de servicio. Este día de servicio está incluido en el intervalo. |

### calendar_dates.txt

Archivo: ** Condicionalmente Obligatorio **

La tabla [calendar_dates.txt] (# calender_datestxt) puede activar o desactivar explícitamente el servicio por fecha. Se puede utilizar de dos maneras.

* Recomendado: use [calendar_dates.txt] (# calender_datestxt) junto con [calendar.txt] (# calendartxt) para definir excepciones a los patrones de servicio predeterminados definidos en [calendar.txt] (# calendartxt). Si el servicio es generalmente habitual, con algunos cambios en fechas explícitas (por ejemplo, para acomodar servicios de eventos especiales o un horario escolar), este es un buen enfoque. En este caso, `calendar_dates.service_id` es un ID que hace referencia a` calendar.service_id`.
* Alternativo: Omitir [calendar.txt] (# calendartxt), y especifique cada fecha de servicio en [calendar_dates.txt] (# calenderdatestxt). Esto permite una variación considerable del servicio y se adapta al servicio sin horarios semanales normales. En este caso, `service_id` es un ID.

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `service_id` | ID que hace referencia a `calendar.service_id` o ID | **Obligatorio** | Identifica un conjunto de fechas cuando se produce una excepción de servicio para una o más rutas. Cada par (`service_id`,` fecha`) solo puede aparecer una vez en [calendar_dates.txt] (# calender_datestxt) si usa [calendar.txt] (# calendartxt) y [calendar_dates.txt] (# calender_datestxt) en conjunto. Si aparece un valor `service_id` en [calendar.txt] (# calendartxt) y [calendar_dates.txt] (# calender_datestxt), la información en [calendar_dates.txt] (# calenderdatestxt) modifica la información de servicio especificada en [calendar. txt] (# calendartxt). |
|  `date` | Fecha | **Obligatorio** | Fecha en que se produce la excepción de servicio. |
|  `exception_type` | Enum | **Obligatorio** | Indica si el servicio está disponible en la fecha especificada en el campo de fecha. Las opciones válidas son: <br><br> `1` - El servicio se ha agregado para la fecha especificada. <br> `2` - El servicio se ha eliminado para la fecha especificada. <hr> * Ejemplo: Supongamos que una ruta tiene un conjunto de viajes disponibles en días festivos y otro conjunto de viajes disponible en los demás días. Un `service_id` podría corresponder al programa de servicio regular y otro` service_id` podría corresponder al programa de vacaciones. Para un día festivo en particular, el archivo [calendar_dates.txt] (# calender_datestxt) se podría usar para agregar el día festivo al `service_id` del día festivo y eliminar el día festivo del programa` service_id` regular. * |

### fare_attributes.txt

Archivo: ** Opcional **

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `fare_id` | ID | **Obligatorio** | Identifica una clase de tarifa. |
|  `price` | Punto flotante no negativo | **Obligatorio** | Precio de tarifa, en la unidad especificada por `currency_type`. |
|  `currency_type` | Código de moneda | **Obligatorio** | Moneda utilizada para pagar la tarifa. |
|  `payment_method` | Enum | **Obligatorio** | Indica cuándo debe pagarse la tarifa. Las opciones válidas son: <br><br> `0` - La tarifa se paga a bordo. <br> `1` - La tarifa debe pagarse antes del embarque. |
|  `transfers` | Enum | **Obligatorio** | Indica el número de transferencias permitidas en esta tarifa. El hecho de que este campo se puede dejar vacío es una excepción al requisito de que un campo Obligatorio no debe estar vacío. Las opciones válidas son: <br><br> `0` - No se permiten transferencias en esta tarifa. <br> `1` - Los pasajeros pueden hacer trasbordo una vez. <br> `2` - Los pasajeros pueden hacer trasbordo dos veces. <br> Vacío - Se permiten trasbordos ilimitados. |
|  `agency_id` | ID que hace referencia a `agency.agency_id` | ** Condicionalmente Obligatorio ** | Identifica la agencia relevante para una tarifa. Este campo es obligatorio para los conjuntos de datos con varias agencias definidas en [agency.txt] (# agencytxt), de lo contrario es opcional. |
|  `transfer_duration` | Entero no negativo | Opcional | Tiempo en segundos antes de que caduque el trasbordo. Cuando `transfers` =` 0` este campo se puede usar para indicar por cuánto tiempo es válido un boleto o se puede dejar vacío. |

### fare_rules.txt

Archivo: ** Opcional **

La tabla [fare_rules.txt] (# farerulestxt) especifica cómo se aplican las tarifas en [fare_attributes.txt] (# fare_attributestxt) a un itinerario. La mayoría de las estructuras de tarifas utilizan alguna combinación de las siguientes reglas:

* La tarifa depende de las estaciones de origen o destino.
* La tarifa depende de las zonas por las que pasa el itinerario.
* La tarifa depende de la ruta que utilice el itinerario.

Para ver ejemplos que muestran cómo especificar una estructura de tarifa con [fare_rules.txt] (# farerulestxt) y [fare_attributes.txt] (# fareattributestxt), consulte [https://code.google.com/p/googletransitdatafeed/wiki/FareExamples](https://code.google.com/p/googletransitdatafeed/wiki/FareExamples) en la wiki del proyecto de código abierto GoogleitablyDataFeed.

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `fare_id` | ID que hace referencia a `fare_attributes.fare_id`  | **Obligatorio** | Identifica una clase de tarifa. |
|  `route_id` | ID que hace referencia a a `route.routes_id` | Opcional | Identifica una ruta asociada a la clase de tarifa. Si existen varias rutas con los mismos atributos de tarifa, cree un registro en [fare_rules.txt] (# fare_rules.txt) para cada ruta. <hr> * Ejemplo: Si la clase de tarifa "b" es válida en la ruta "TSW" y "TSE", el archivo [fare_rules.txt] (# fare_rules.txt) contendrá estos registros para la clase de tarifa: * <br> `fare_id, route_id` <br> `b, TSW` <br> `b, TSE`|
|  `origin_id` | ID que hace referencia a a `stops.zone_id` | Opcional | Identifica una zona de origen. Si una clase de tarifa tiene varias zonas de origen, cree un registro en [fare_rules.txt] (# fare_rules.txt) para cada `origin_id`. <hr> * Ejemplo: Si la clase de tarifa &quot;b&quot; es válida para todos los viajes que se originan desde la zona "2" o la zona "8", el archivo [fare_rules.txt] (# fare_rules.txt) contendrá estos registros para la clase de tarifa: * <br> `fare_id, ..., origin_id` <br> `b, ..., 2` <br> `b, ..., 8` |
|  `destination_id` | ID que hace referencia a a `stops.zone_id` | Opcional | Identifica una zona de destino. Si una clase de tarifa tiene múltiples zonas de destino, cree un registro en [fare_rules.txt] (# fare_rules.txt) para cada `destination_id`. <hr> * Ejemplo: los campos `origin_id` y` destination_id` podrían usarse juntos para especificar que la clase de tarifa "b" es válida para viajar entre las zonas 3 y 4, y para viajar entre las zonas 3 y 5, la [fare_rules.txt] ( El archivo # fare_rules.txt) contendría estos registros para la clase de tarifa: * <br> `fare_id, ..., origin_id, destination_id` <br> `b, ..., 3,4` <br> `b, ..., 3,5` |
|  `includes_id` | ID que hace referencia a a `stops.zone_id` | Opcional | Identifica las zonas que un usuario entrará al usar una clase de tarifa determinada. Se utiliza en algunos sistemas para calcular la clase de tarifa correcta. <hr> * Ejemplo: Si la clase de tarifa "c" está asociada con todos los viajes en la ruta GRT que pasa por las zonas 5, 6 y 7, [fare_rules.txt] (# fare_rules.txt) contendría estos registros: * <br> `fare_id, route_id, ..., contiene_id` <br> `C, GRT, ..., 5` <br> `C, GRT, ..., 6` <br> `C, GRT, ..., 7` <br> * Debido a que todas las zonas `contents_id` deben coincidir para que la tarifa se aplique, un itinerario que pase por las zonas 5 y 6 pero no la zona 7 no tendrá la clase de tarifa "c". Para obtener más detalles, consulte [https://code.google.com/p/googletransitdatafeed/wiki/FareExamples](https://code.google.com/p/googletransitdatafeed/wiki/FareExamples) en el wiki del proyecto GoogleTransitDataFeed. * |

### shapes.txt

Archivo: ** Opcional **

Describe el camino físico que un vehículo toma como un conjunto de coordenadas ordenadas de latitud y longitud. Trazar las coordenadas en orden proporciona la ruta del vehículo. Las coordenadas no tienen que coincidir con las ubicaciones de parada.

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `shape_id` | ID | **Obligatorio** | Identifica una ruta. |
|  `shape_pt_lat` | Latitud | **Obligatorio** | Latitud de un punto de ruta. Cada registro en [shapes.txt] (# shapestxt) representa un punto de forma utilizado para definir la ruta. |
|  `shape_pt_lon` | Longitud | **Obligatorio** | Longitud de un punto de ruta. |
|  `shape_pt_sequence` | Entero no negativo | **Obligatorio** | Secuencia en la que los puntos de la ruta se conectan para formarla. Los valores deben aumentar a lo largo del viaje, pero no es necesario que sean consecutivos. <hr> * Ejemplo: Si la forma "A_shp" tiene tres puntos en su definición, el archivo [shapes.txt] (# shapestxt) puede contener estos registros para definir la ruta: * <br> `shape_id, shape_pt_lat, shape_pt_lon, shape_pt_sequence` <br> `A_shp, 37.61956, -122.48161,0` <br> `A_shp, 37.64430, -122.41070,6` <br> `A_shp, 37.65863, -122.30839,11` |
|  `shape_dist_traveled` | Punto flotante no negativo | Opcional | Distancia real recorrida a lo largo de la ruta desde el primer punto hasta el punto especificado en este registro. Utilizado por los planificadores de viajes para mostrar la parte correcta de la ruta en un mapa. Los valores deben aumentar junto con `shape_pt_sequence`; no se pueden utilizar para mostrar el recorrido inverso a lo largo de una ruta. Las unidades de distancia deben ser coherentes con las utilizadas en [stop_times.txt] (# stop_timestxt). <hr> * Ejemplo: si un autobús viaja a lo largo de los tres puntos definidos anteriormente para A_shp, los valores `shape_dist_traveled` adicionales (que se muestran aquí en kilómetros) se verían así: * <br> `shape_id, shape_pt_lat, shape_pt_lon, shape_pt_sequence, shape_dist_traveled` <br> `A_shp, 37.61956, -122.48161,0,0` <br> `A_shp, 37.64430, -122.41070,6,6.8310` <br> `A_shp, 37.65863, -122.30839,11,15.8765` |

### frequencies.txt

Archivo: ** Opcional **

[Frequencies.txt] (# frequenciestxt) representa los viajes que operan en los avances regulares (tiempo entre viajes). Este archivo se puede utilizar para representar dos tipos diferentes de servicio.

* Servicio basado en la frecuencia (`exact_times` =` 0`) en el que el servicio no sigue un horario fijo durante todo el día. En cambio, los operadores intentan mantener estrictamente los avances predeterminados para los viajes.
* Una representación comprimida del servicio basado en la programación (`exact_times`=`1`) que tiene el mismo avance exacto para los viajes en períodos de tiempo específicos. En el servicio basado en horarios, los operadores intentan adherirse estrictamente a un horario.


|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `trip_id` | ID que hace referencia a a `trips.trip_id` | **Obligatorio** | Identifica un viaje al que se aplica el avance de servicio especificado. |
|  `start_time` | Hora | **Obligatorio** | Hora en que el primer vehículo sale de la primera parada del viaje con el avance especificado. |
|  `end_time` | Hora | **Obligatorio** | Hora en que el servicio cambia a un avance diferente (o cesa) en la primera parada del viaje. |
|  `headway_secs` | Entero no negativo | **Obligatorio** | Tiempo, en segundos, entre las salidas desde la misma parada (avance) para el viaje, durante el intervalo de tiempo especificado por `start_time` y` end_time`. Se permiten varios avances para el mismo viaje, pero no se pueden interponer. Los nuevos avances pueden comenzar en el momento exacto en que finaliza el avance anterior.  |
|  `exact_times` | Enum | Opcional | Indica el tipo de servicio para un viaje. Vea la descripción del archivo para más información. Las opciones válidas son: <br><br> `0` o vacío - viajes basados en frecuencia. <br> `1` - Viajes basados en horarios con el mismo avance durante todo el día. En este caso, el valor `end_time` debe ser mayor que el último viaje deseado` start_time` pero menor que el último viaje deseado start_time + `headway_secs`. |

### transfers.txt

Archivo: ** Opcional **

Al calcular un itinerario, las aplicaciones que consumen GTFS interpolan las transferencias según el tiempo permitido y la proximidad. [Transfers.txt] (# transferstxt) especifica reglas adicionales y anulaciones para trasbordos seleccionados.

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `from_stop_id` | ID que hace referencia a `stops.stop_id` | **Obligatorio** | Identifica una parada o estación donde comienza una conexión entre rutas. Si este campo se refiere a una estación, la regla de transferencia se aplica a todas sus paradas secundarias. |
|  `to_stop_id` | ID que hace referencia a `stops.stop_id` | **Obligatorio** | Identifica una parada o estación donde finaliza una conexión entre rutas. Si este campo se refiere a una estación, la regla de transferencia se aplica a todas las paradas secundarias. |
|  `transfer_type` | Enum | **Obligatorio** | Indica el tipo de conexión para el par especificado (`from_stop_id`,` to_stop_id`). Las opciones válidas son: <br><br> `0` o vacío - Punto de transferencia recomendado entre rutas. <br> `1` - Punto de transferencia cronometrado entre dos rutas. Se espera que el vehículo que salga espere al que llega y deje tiempo suficiente para que un pasajero se haga trasbordo entre rutas. <br> `2` - El trasbordo requiere una cantidad mínima de tiempo entre la llegada y la salida para garantizar una conexión. El tiempo requerido para la transferencia se especifica mediante `min_transfer_time`. <br> `3` - Los trasbordos no son posibles entre las rutas en la ubicación. |
|  `min_transfer_time` | Entero no negativo | Opcional | Cantidad de tiempo, en segundos, que debe estar disponible para permitir un trasbordo entre rutas en las paradas especificadas. El `min_transfer_time` debería ser suficiente para permitir que un usuario típico se mueva entre las dos paradas, incluido el tiempo de búfer para permitir la variación de la programación en cada ruta. |

### pathways.txt

Archivo: ** Opcional **

La extensión GTFS-Pathways utiliza una representación gráfica para describir el metro o el tren, con nodos (las ubicaciones) y bordes (los caminos).

Para ir desde la entrada (que es un nodo representado como un lugar con location_type = 2) a una plataforma (que es un nodo representado como un lugar con location_type = 0), el usuario pasará por una pasarela, puertas de tarifas, escaleras, etc. (que son bordes representados como caminos). La propuesta también agrega otro tipo de lugar, uno genérico llamado "nodo genérico", para representar, por ejemplo, un cruce de vía peatonal desde donde se pueden tomar diferentes vías peatonales.

** Advertencia: los caminos deben ser exhaustivos en una estación. ** Como consecuencia, tan pronto como una plataforma (como parada), entrada o nodo que pertenece a una estación tenga un camino vinculado a ella, se supone que la estación tiene una descripción exhaustiva de sus caminos. Por lo tanto, se aplican las siguientes reglas de sentido común:
- Ningún lugar inconexo: si algún lugar dentro de una estación tiene un camino, entonces todos los lugares deberían tener caminos (excepto aquellas plataformas que tienen áreas de embarque).
- Ninguna plataforma bloqueada: cada plataforma debe estar conectada al menos a una entrada a través de alguna cadena de caminos.
- Ninguna vía para una plataforma con áreas de embarque: una plataforma que tiene áreas de embarque se trata como un objeto principal, no como un punto. Puede que no tenga caminos asignados. Todos los caminos deberían ser para áreas de embarque.

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `pathway_id` | ID | **Obligatorio** | El campo `pathway_id` contiene una ID que identifica de forma única el camino. Los sistemas usan el `pathway_id` como un identificador interno de este registro (por ejemplo, la clave principal en la base de datos) y, por lo tanto, el` pathway_id` debe ser exclusivo del conjunto de datos. <br> Diferentes caminos pueden ir desde el mismo `from_stop_id` al mismo` to_stop_id`. Por ejemplo, esto sucede cuando dos escaleras mecánicas están una al lado de la otra en dirección opuesta, o cuando una escalera está cerca del elevador y ambos van del mismo lugar al mismo lugar.|
|  `from_stop_id` | ID que hace referencia a `stops.stop_id` | **Obligatorio** | Lugar donde comienza el camino. Contiene un `stop_id` que identifica una plataforma, entrada / salida, nodo genérico o área de embarque desde el archivo` stops.txt`.|
|  `to_stop_id` | ID que hace referencia a `stops.stop_id` | **Obligatorio** | Lugar donde finaliza el camino. Contiene un `stop_id` que identifica una plataforma, entrada / salida, nodo genérico o área de embarque desde el archivo` stops.txt`.|
|  `pathway_mode` | Enum | **Obligatorio** | Tipo de camino entre el par especificado (`from_stop_id`,` to_stop_id`). Los valores válidos para este campo son: <br> • 1: vía peatonal <br> • 2: escaleras <br> • 3: acera móvil / travelador <br> • 4: escalera mecánica <br> • 5: elevador <br> • 6: compuerta de tarifas (o compuerta de pago): un camino que cruza hacia un área de la estación donde se requiere un comprobante de pago (generalmente a través de una compuerta de pago física). <br> Las puertas de tarifas pueden separar las áreas pagadas de la estación de las no pagadas, o separar las diferentes áreas de pago dentro de la misma estación entre sí. Esta información se puede usar para evitar que los pasajeros pasen por estaciones que utilizan accesos directos que requieren que los pasajeros realicen pagos innecesarios, como por ejemplo, hacer que un pasajero camine a través de una plataforma de metro para llegar a una vía de autobuses. <br> • 7: puerta de salida: indica un camino que sale de un área donde se requiere comprobante de pago en un área donde ya no se requiere comprobante de pago.|
|  `is_bidirectional` | Enum | **Obligatorio** | Indica en qué dirección se puede utilizar el camino: <br> • 0: camino unidireccional, solo se puede usar desde `from_stop_id` a` to_stop_id`. <br> • 1: camino bidireccional, se puede utilizar en las dos direcciones. <br><br> Las puertas de tarifa (`pathway_mode = 6`) y las puertas de salida (` pathway_mode = 7`) no pueden ser bidireccionales.|
| `length` | Punto flotante no negativo | Opcional | Longitud horizontal en metros de la ruta desde el lugar de origen (definido en `from_stop_id`) al lugar de destino (definida en` to_stop_id`). <br><br> Este campo se recomienda para vías peatonales (`pathway_mode = 1`), puertas de tarifa (` pathway_mode = 6`) y puertas de salida (`pathway_mode = 7`).|
| `traversal_time` | Entero positivo | Opcional | Tiempo promedio en segundos necesario para recorrer la ruta desde la ubicación de origen (definida en `from_stop_id`) hasta la ubicación de destino (definida en` to_stop_id`). <br><br> Este campo se recomienda para cintas desplazadoras (`pathway_mode = 3`), escaleras mecánicas (` pathway_mode = 4`) y elevador (`pathway_mode = 5`).|
| `stair_count` | Entero no nulo | Opcional | Número de escaleras del camino. <br><br> Mejores prácticas: se podría usar la aproximación de 1 piso = 15 escaleras para generar valores aproximados. <br><br> Un `stair_count` positivo implica que el pasajero sube de` from_stop_id` a `to_stop_id`. Y un `stair_count` negativo implica que el pasajero camina desde` from_stop_id` a `to_stop_id`. <br><br> Este campo se recomienda para escaleras (`pathway_mode = 2`).|
| `max_slope` | Punto flotante | Opcional | Relación máxima de pendiente de la vía. Los valores válidos para este campo son: <br> • 0 o (vacío): sin pendiente. <br> • Una relación flotante: relación de pendiente de la vía, positiva para arriba, negativa para abajo. <br><br> Este campo se debería usar solo con vías peatonales (`pathway_type = 1`) y cintas transportadoras (` pathway_type = 3`). <br><br> Ejemplo: en los EE. UU., 0.083 (también escrito en 8.3%) es la relación de pendiente máxima para una silla de ruedas propulsada a mano, lo que significa un aumento de 0.083 m (por lo tanto, 8,3 cm) por cada 1 m.|
| `min_width` | Punto flotante positivo | Opcional | Ancho mínimo del camino en metros. <br><br> Este campo es muy recomendable si el ancho mínimo es inferior a 1 metro.|
| `signposted_as` | Texto | Opcional | Cadena de texto de la señalización física visible para los pasajeros de tránsito. La cadena se puede utilizar para proporcionar instrucciones de texto a los usuarios, como "seguir las indicaciones a". El texto del idioma debería aparecer en este campo exactamente como está impreso en los letreros, no debería estar traducido.|
| `reversed_signposted_as` | Texto | Opcional | Igual que el campo `signposted_as`, pero cuando se camino se usa hacia atrás, es decir, desde el` to_stop_id` hasta el `from_stop_id`.|

### levels.txt

Archivo: ** Opcional **

Describe los diferentes niveles de una estación. Es sobre todo útil cuando se usa junto con `pathways.txt`, y se requiere para que el elevador (` pathway_mode = 5`) solicite al usuario que tome el elevador al nivel “Mezzanine” o “Plataforma”.

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `level_id` | ID | **Obligatorio** | ID del nivel al que se puede hacer referencia desde `stops.txt`.|
|  `level_index` | Punto flotante | **Obligatorio** | Índice numérico del nivel que indica la posición relativa de este nivel en relación con otros niveles (se supone que los niveles con índices más altos se ubican por encima de los niveles con índices más bajos). <br><br> El nivel del suelo debería tener un índice 0, con los niveles por encima del suelo indicados por índices positivos y los niveles por debajo del suelo por índices negativos.|
|  `level_name` | Texto | Opcional | Nombre opcional del nivel (que coincide con las letras / numeración de nivel utilizadas dentro del edificio o la estación). Es útil para enrutar el elevador (por ejemplo, "llevar el elevador al nivel "Mezzanine" o "Plataformas" o "-1").|


### feed_info.txt

Archivo: ** Opcional **

El archivo contiene información sobre el conjunto de datos en sí, en lugar de los servicios que describe el conjunto de datos. Tenga en cuenta que, en algunos casos, el editor del conjunto de datos es una entidad diferente a cualquiera de las agencias.

|  Nombre del campo | Tipo | Obligatorio | Descripción |
|  ------ | ------ | ------ | ------ |
|  `feed_publisher_name` | Texto | **Obligatorio** | Nombre completo de la organización que publica el conjunto de datos. Esto puede ser lo mismo que uno de los valores `agency.agency_name`. |
|  `feed_publisher_url` | URL | **Obligatorio** | URL del sitio web de la organización que publica el conjunto de datos. Esto puede ser lo mismo que uno de los valores `agency.agency_url`. |
|  `feed_lang` | Código de idioma | **Obligatorio** | Idioma predeterminado utilizado para el texto en este conjunto de datos. Esta configuración ayuda a los consumidores de GTFS a elegir reglas de capitalización y otras configuraciones específicas de idioma para el conjunto de datos. |
|  `feed_start_date` | Fecha | Opcional | El conjunto de datos proporciona información de programación completa y confiable para el servicio en el período desde el comienzo del día `feed_start_date` hasta el final del día` feed_end_date`. Ambos días se pueden dejar vacíos si no están disponibles. La fecha `feed_end_date` no debe preceder a la fecha` feed_start_date` si se dan ambos. Se recomienda a los proveedores de conjuntos de datos que proporcionen datos de horarios fuera de este período para informar sobre un posible servicio futuro, pero los consumidores de conjuntos de datos debería tratarlo teniendo en cuenta su estado no autorizado. Si `feed_start_date` o` feed_end_date` se extienden más allá de las fechas activas del calendario definidas en [calendar.txt] (# calendartxt) y [calendar_dates.txt] (# calendar_datestxt), el conjunto de datos hace una afirmación explícita de que no hay servicio para las fechas dentro del rango `feed_start_date` o` feed_end_date` pero no incluido en las fechas activas del calendario. |
|  `feed_end_date` | Fecha | Opcional | (véase más arriba) |
|  `feed_version` | Texto | Opcional | Cadena que indica la versión actual de su conjunto de datos GTFS. Las aplicaciones que consumen GTFS pueden mostrar este valor para ayudar a los editores de conjuntos de datos a determinar si se ha incorporado el último conjunto de datos. |
|  `feed_contact_email` | Correo eléctronico | Opcional | Dirección de correo electrónico para la comunicación relacionada con el conjunto de datos GTFS y las prácticas de publicación de datos. `feed_contact_email` es un contacto técnico para aplicaciones que hacen uso de GTFS. Proporcione información de contacto de servicio al cliente a través de [agency.txt] (# agencytxt). |
|  `feed_contact_url` | URL | Opcional | URL para información de contacto, un formulario web, soporte técnico u otras herramientas para la comunicación con respecto al conjunto de datos GTFS y las prácticas de publicación de datos. `feed_contact_url` es un contacto técnico para aplicaciones que consumen GTFS. Proporcione información de contacto de servicio al cliente a través de [agency.txt] (# agencytxt). |
