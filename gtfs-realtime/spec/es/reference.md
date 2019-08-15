Un feed GTFS en tiempo real permite que las empresas de transporte público brinden a los consumidores información en tiempo real acerca de las interrupciones de sus servicios (estaciones cerradas, líneas que no funcionan, demoras importantes, etc.), la ubicación de sus vehículos y tiempos de llegada esperados.

Las especificaciones de la versión 2.0 del feed se abordan y documentan aquí. Versiones validas son "2.0", "1.0". 

### Definiciones de términos 

#### Obligatorio

En GTFS-realtime v2.0 y superior, la columna *Obligatorio* define qué campos debe proporcionar un productor para que los datos de tránsito sean válidos y tengan sentido para una aplicación consumidora.

Los siguientes valores son utilizados en el campo *Obligatorio*:

*   **Obligatorio**: Este campo tiene que ser facilitado por un productor del feed GTFS en tiempo real.
*   **Condicionalmente obligatorio**: Este campo es obligatorio bajo ciertas condiciones, que se definen en el campo *Description*. Fuera de estas condiciones, el campo es opcional.
*   **Opcional**: este campo es opcional y no está requerida su implementación por los productores. Sin embargo, si los datos están disponibles en los sistemas de ubicación de vehículos automáticos subyacentes (p. Ej., VehiclePosition `timestamp`), se recomienda que los productores proporcionen estos campos opcionales cuando sea posible.

* Ten en cuenta que los requisitos semánticos no se definieron en la versión 1.0 de GTFS-realtime y, por lo tanto, los feeds con `gtfs_realtime_version` de` 1` pueden no cumplir estos requisitos (consulte [la propuesta para requisitos semánticos] (https://github.com/google/transit/pull/64) para detalles). *

#### Cardinalidad

* Cardinalidad * representa el número de elementos que pueden proporcionarse para un campo en particular, con los siguientes valores:

* **Uno** - Se puede proporcionar un único elemento para este campo. Esto se asigna al [Protocolo Buffer * obligatorio * y * opcional * cardinalidades] (https://developers.google.com/protocol-buffers/docs/proto#simple).
* **Muchos** - Se pueden proporcionar muchos elementos (0, 1 o más) para este campo. Esto se asigna a la [cardinalidad *repetida* del búfer de protocolo] (https://developers.google.com/protocol-buffers/docs/proto#simple).

Siempre haga referencia a los campos * Obligatorio * y * Descripción * para ver cuándo se requiere un campo, condicionalmente obligatorio u opcional. Consulte [`gtfs-realtime.proto`] (https://github.com/google/transit/blob/master/gtfs-realtime/proto/gtfs-realtime.proto) para la cardinalidad del búfer de protocolo.

#### Tipos de datos del búfer de protocolo

Los siguientes tipos de datos de búfer de protocolo se utilizan para describir elementos de feed:

*   **message**: tipo complejo
*   ** enum **: lista de valores fijos

#### Campos experimentales

Los campos etiquetados como ** experimental ** están sujetos a cambios y aún no se han adoptado formalmente en la especificación. Un campo ** experimental ** podrá ser adoptado formalmente en el futuro.

## Índice de elementos

*   [FeedMessage](#message-feedmessage)
    *   [FeedHeader](#message-feedheader)
        *   [Incrementality](#enum-incrementality)
    *   [FeedEntity](#message-feedentity)
        *   [TripUpdate](#message-tripupdate)
            *   [TripDescriptor](#message-tripdescriptor)
                *   [ScheduleRelationhip](#enum-schedulerelationship-1)
            *   [VehicleDescriptor](#message-vehicledescriptor)
            *   [StopTimeUpdate](#message-stoptimeupdate)
                *   [StopTimeEvent](#message-stoptimeevent)
                *   [ScheduleRelationhip](#enum-schedulerelationship)
        *   [VehiclePosition](#message-vehicleposition)
            *   [TripDescriptor](#message-tripdescriptor)
                *   [ScheduleRelationhip](#enum-schedulerelationship-1)
            *   [VehicleDescriptor](#message-vehicledescriptor)
            *   [Position](#message-position)
            *   [VehicleStopStatus](#enum-vehiclestopstatus)
            *   [CongestionLevel](#enum-congestionlevel)
            *   [OccupancyStatus](#enum-occupancystatus)
        *   [Alert](#message-alert)
            *   [TimeRange](#message-timerange)
            *   [EntitySelector](#message-entityselector)
                *   [TripDescriptor](#message-tripdescriptor)
                    *   [ScheduleRelationhip](#enum-schedulerelationship-1)
            *   [Cause](#enum-cause)
            *   [Effect](#enum-effect)
            *   [TranslatedString](#message-translatedstring)
                *   [Translation](#message-translation)
            *   [SeverityLevel](#enum-severitylevel)

# Elementos

## _message_ FeedMessage

El contenido de un mensaje de feed. Cada mensaje en el flujo se obtiene como respuesta a una solicitud GET de HTTP apropiada. Un feed en tiempo real siempre se define con relación a un feed GTFS existente. Todos los identificadores de entidad se resuelven con respecto al feed GTFS.

#### Campos

| _**Nombre del campo**_ | _**Type**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
|**header** | [FeedHeader](#message-feedheader) | Obligatorio | Uno | Metadatos sobre este feed y mensaje de feed. |
|**entity** | [FeedEntity](#message-feedentity) | Condicionalmente obligatorio | Muchos | Contenido del feed. Si hay información en tiempo real disponible para el sistema de tránsito, se debe proporcionar este campo. Si este campo está vacío, los consumidores deben asumir que no hay información disponible en tiempo real para el sistema. |

## _message_ FeedHeader

Metadatos sobre un feed, incluidos en los mensajes de feed.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **gtfs_realtime_version** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obligatorio | Uno | Versión de la especificación del feed. La versión actual es 2.0. |
| **incrementality** | [Incrementalidad](#enum-incrementality) | Obligatorio | Uno |
| **timestamp** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obligatorio | Uno | Esta marca de tiempo identifica el momento en que se creó el contenido de este feed (en tiempo del servidor). En el tiempo POSIX (es decir, número de segundos desde el 1 de enero de 1970, 00:00:00 UTC). Para evitar el sesgo de tiempo entre los sistemas que producen y consumen información en tiempo real, se recomienda encarecidamente obtener la marca de tiempo de un servidor de tiempo. Es completamente aceptable usar Stratum 3 o incluso servidores de estratos más bajos, ya que las diferencias de tiempo de hasta un par de segundos son tolerables. |

## _enum_ Incrementality

Determina si la consulta actual es incremental.

*   ** FULL_DATASET **: esta actualización del feed sobrescribirá toda la información anterior en tiempo real para el feed. Por lo tanto, es de esperar que esta actualización proporcione una instantánea completa de toda la información conocida en tiempo real.
*   **DIFFERENTIAL**: actualmente, este modo es **no compatible** y el comportamiento **no está especificado** para los feeds que usan este modo. Hay discusiones en la [Lista de correo en tiempo real de GTFS] (http://groups.google.com/group/gtfs-realtime) en torno a la especificación completa del comportamiento del modo DIFFERENTIAL y la documentación se actualizará cuando se finalicen esas discusiones.

#### Valores

| _**Valor**_ |
|-------------|
| **FULL_DATASET** |
| **DIFFERENTIAL** |

## _message_ FeedEntity

Una definición (o actualización) de una entidad en el feed de tránsito. Si la entidad no se está eliminando, se debe rellenar exactamente uno de los campos 'trip_update', 'vehicle' y 'alert'.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obligatorio | Uno | Identificador único del feed para esta entidad. Los identificadores se utilizan solo para proporcionar compatibilidad incremental. Las entidades reales a las que hace referencia el feed deben especificarse mediante selectores explícitos (consulte EntitySelector a continuación para obtener más información). |
| **is_deleted** | [bool](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Si esta entidad se va a eliminar. Debe proporcionarse solo para feeds con Incrementalidad de DIFFERENTIAL: este campo NO se debe proporcionar para feeds con Incrementalidad de FULL_DATASET. |
| **trip_update** | [TripUpdate](#message-tripupdate) | Condicionalmente obligatorio | Uno | Datos sobre las demoras de salida en tiempo real de un viaje. Se debe proporcionar al menos uno de los campos trip_update, vehicle o alert; todos estos campos no pueden estar vacíos. |
| **vehicle** | [VehiclePosition](#message-vehicleposition) | Condicionalmente obligatorio | Uno | Datos sobre la posición en tiempo real de un vehículo. Se debe proporcionar al menos uno de los campos trip_update, vehicle o alert; todos estos campos no pueden estar vacíos. |
| **alert** | [Alerta](#message-alert) | Condicionalmente obligatorio | Uno | Datos sobre la alerta en tiempo real. Se debe proporcionar al menos uno de los campos trip_update, vehicle o alert; todos estos campos no pueden estar vacíos. |

## _message_ TripUpdate

Actualización en tiempo real sobre el progreso de un vehículo a lo largo de un viaje. Consulte también la discusión general de las [entidades de actualizaciones de viaje] (trip-updates.md).

Dependiendo del valor de ScheduleRelationship, un TripUpdate puede especificar:

*   Un viaje que transcurre a lo largo del horario.
*   Un viaje que continúa a lo largo de una ruta pero no tiene un horario fijo.
*   Un viaje que se ha añadido o eliminado con respecto a lo programado.

Las actualizaciones pueden ser para eventos futuros, pronosticados de llegada / salida, o para eventos pasados que ya ocurrieron. En la mayoría de los casos, la información sobre eventos pasados es un valor medido, por lo que se recomienda que su valor de incertidumbre sea 0\. Aunque podría haber casos en los que esto no sea así, se permite tener un valor de incertidumbre diferente de 0 para eventos pasados. Si la incertidumbre de una actualización no es 0, o la actualización es una predicción aproximada para un viaje que no se ha completado o la medición no es precisa o la actualización fue una predicción para el pasado que no se ha verificado después de ocurrido el evento.

Tenga en cuenta que la actualización puede describir un viaje que ya se ha completado. Para este fin, es suficiente proporcionar una actualización para la última parada del viaje. Si la hora de llegada a la última parada es en el pasado, el cliente concluirá que todo el viaje está en el pasado (es posible, aunque sin importancia, proporcionar actualizaciones para las paradas anteriores). Esta opción es más relevante para un viaje que se ha completado antes de lo programado, pero de acuerdo con el horario, el viaje aún continúa en el momento actual. La eliminación de las actualizaciones para este viaje podría hacer que el cliente asuma que el viaje aún está en curso. Tenga en cuenta que el proveedor de feeds está permitido, pero no es obligatorio, purgar las actualizaciones pasadas; este es un caso en el que esto sería prácticamente útil.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **trip** | [TripDescriptor](#message-tripdescriptor) | Obligatorio | Uno | El viaje al que se aplica este mensaje. Puede haber como máximo una entidad TripUpdate para cada instancia de viaje real. Si no hay ninguno, eso significa que no hay información de predicción disponible. *No* significa que el viaje progrese según lo programado. |
| **vehicle** | [VehicleDescriptor](#message-vehicledescriptor) | Opcional | Uno | Información adicional sobre el vehículo que está realizando este viaje. |
| **stop_time_update** | [StopTimeUpdate](#message-stoptimeupdate) | Condicionalmente obligatorio | Muchos | Actualizaciones de StopTimes para el viaje (tanto futuras, es decir, predicciones, y en algunos casos, pasadas, es decir, las que ya sucedieron). Las actualizaciones se deben ordenar por stop_sequence y se aplican a todas las siguientes paradas del viaje hasta el próximo stop_time_update especificado. Se debe proporcionar al menos un stop_time_update para el viaje, a menos que trip.schedule_relationship sea CANCELADO: si el viaje se cancela, no es necesario proporcionar stop_time_updates. |
| **timestamp** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Momento en el que se midió el progreso en tiempo real del vehículo. En el tiempo POSIX (es decir, la cantidad de segundos desde el 1 de enero de 1970, 00:00:00 UTC). |
| **delay** | [int32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | La desviación del horario actual para el viaje. Delay solo debe especificarse cuando la predicción se da en relación con alguna programación existente en GTFS. <br> Delay (en segundos) puede ser positivo (lo que significa que el vehículo está retrasado) o negativo (lo que significa que el vehículo está adelantado a lo previsto). Delay de 0 significa que el vehículo está exactamente a tiempo. <br> La información de delay en StopTimeUpdates tiene prioridad sobre la información de delay a nivel de viaje, por lo que delay a nivel de viaje solo se propaga hasta la siguiente parada a lo largo del viaje con un valor de StopTimeUpdate delay especificado. <br> Se recomienda encarecidamente a los proveedores de feeds que proporcionen un valor de TripUpdate.timestamp que indique cuándo se actualizó por última vez el valor de delay, para evaluar la actualización de los datos. <br> ** Precaución: ** este campo aún es ** experimental ** y está sujeto a cambios. Puede ser adoptado formalmente en el futuro.|

## _message_ StopTimeEvent

Información de tiempo para un solo evento predicho (ya sea llegada o salida). El tiempo consiste en delay y / o tiempo estimado, y uncertainty.

*   delay se debe utilizar cuando la predicción se da en relación con algún horario existente en GTFS.
*   Debe darse time si hay un horario previsto o no. Si se especifican time y delay, time tendrá prioridad (aunque normalmente, time, si se da para un viaje programado, debe ser igual al tiempo programado en GTFS + delay).

Uncertainty se aplica tanto a time como a delay. Uncertainty especifica aproximadamente el error esperado en la verdadera demora (pero tenga en cuenta que aún no definimos su significado estadístico preciso). Es posible que uncertainty sea 0, por ejemplo, para trenes que son conducidos bajo control de tiempo de computadora.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **delay** | [int32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | Delay (en segundos) puede ser positivo (lo que significa que el vehículo está retrasado) o negativo (lo que significa que el vehículo está adelantado a lo previsto). Delay de 0 significa que el vehículo está exactamente a tiempo. Se debe proporcionar delay o time dentro de un StopTimeEvent: ambos campos no pueden estar vacíos. |
| **time** | [int64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | El evento como tiempo absoluto. En el tiempo POSIX (es decir, número de segundos desde el 1 de enero de 1970, 00:00:00 UTC). Se debe proporcionar una demora o un tiempo dentro de un StopTimeEvent: ambos campos no pueden estar vacíos. |
| **uncertainty** | [int32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Si se omite uncertainty, se interpreta como desconocido. Para especificar una predicción completamente cierta, establezca su uncertainty en 0. |

## _message_ StopTimeUpdate

Actualización en tiempo real para eventos de llegada y/o salida para una parada determinada en un viaje. Consulte también la discusión general sobre las actualizaciones de tiempo de parada en la documentación de [TripDescriptor] (# message-tripdescriptor) y [trip updates entities](trip-updates.md).

Se pueden suministrar actualizaciones para eventos pasados y futuros. Al productor se le permite, aunque no es requerido, abandonar eventos pasados. 
La actualización está vinculada a una parada específica a través de stop_sequence o stop_id, por lo que uno de estos campos debe establecerse necesariamente. Si se visita el mismo stop_id más de una vez en un viaje, entonces se debe proporcionar stop_sequence en todos los StopTimeUpdates para ese stop_id en ese viaje.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **stop_sequence** | [uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | Debe ser el mismo que en stop_times.txt en el feed GTFS correspondiente. Se debe proporcionar stop_sequence o stop_id dentro de StopTimeUpdate; ambos campos no pueden estar vacíos. Se requiere stop_sequence para los viajes que visitan el mismo stop_id más de una vez (por ejemplo, un trayecto circular) para desambiguar para qué se detiene la predicción. |
| **stop_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | Debe ser el mismo que en stops.txt en el feed GTFS correspondiente. Se debe proporcionar stop_sequence o stop_id dentro de StopTimeUpdate; ambos campos no pueden estar vacíos. |
| **arrival** | [StopTimeEvent](#message-stoptimeevent) | Condicionalmente obligatorio | Uno | Si schedule_relationship está vacío o SCHEDULED, la llegada o la salida deben proporcionarse dentro de un StopTimeUpdate: ambos campos no pueden estar vacíos. Tanto la llegada como la salida pueden estar vacías cuando schedule_relationship es SKIPPED. Si schedule_relationship es NO_DATA, la llegada y la salida deben estar vacías. |
| **departure** | [StopTimeEvent](#message-stoptimeevent) | Condicionalmente obligatorio | Uno | Si schedule_relationship está vacío o SCHEDULED, la llegada o la salida deben proporcionarse dentro de un StopTimeUpdate: ambos campos no pueden estar vacíos. Tanto la llegada como la salida pueden estar vacías cuando schedule_relationship es SKIPPED. Si schedule_relationship es NO_DATA, la llegada y la salida deben estar vacías. |
| **schedule_relationship** | [ScheduleRelationship](#enum-schedulerelationship) | Opcional | Uno | La relación por defecto es SCHEDULED. |

## _enum_ ScheduleRelationship

La relación entre este StopTime y el horario estático.

#### Valores

| _**Valor**_ | _**Comentario**_ |
|-------------|---------------|
| **SCHEDULED** | El vehículo avanza de acuerdo con su programa estático de paradas, aunque no necesariamente de acuerdo con las horas del horario. Este es el comportamiento **default**. Se debe proporcionar al menos uno de llegada y salida. |
| **SKIPPED** | Se omite la parada, es decir, el vehículo no se detendrá en esta parada. Llegada y salida son opcionales. Cuando el conjunto `SKIPPED` no se propaga a paradas posteriores en el mismo viaje (es decir, el vehículo se detendrá en paradas posteriores en el viaje a menos que esas paradas también tengan un` stop_time_update` con `schedule_relationship: SKIPPED`). La demora de una parada previa en el viaje * se * propaga sobre la parada `SKIPPED`. Dicho de otro modo, si una predicción `stop_time_update` con `arrival` o` departure` no se establece para una parada después de la parada `SKIPPED`, la predicción corriente arriba de la parada` SKIPPED` se propagará hasta la parada después de la ` SKIPPED` se detiene y paradas posteriores en el viaje hasta que se proporciona un `stop_time_update` para una parada posterior.  |
| **NO_DATA** | No se dan datos para esta parada. Indica que no hay información disponible en tiempo real. Cuando el conjunto NO_DATA se propaga a través de paradas posteriores, esta es la forma recomendada de especificar desde qué parada no hay información en tiempo real. Cuando NO_DATA se establece, no se debe proporcionar la llegada ni la salida. |

## _message_ VehiclePosition

Información de posición en tiempo real para un vehículo determinado.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **trip** | [TripDescriptor](#message-tripdescriptor) | Opcional | Uno | El viaje que está realizando este vehículo. Puede estar vacío o parcial si el vehículo no se puede identificar con una instancia de viaje determinada. |
| **vehicle** | [VehicleDescriptor](#message-vehicledescriptor) | Opcional | Uno | Información adicional sobre el vehículo que está realizando este viaje. Cada entrada debe tener una identificación de vehículo ** única **. |
| **position** | [Position](#message-position) | Opcional | Uno | Posición actual de este vehículo. |
| **current_stop_sequence** | [uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | El índice de secuencia de parada de la parada actual. El significado de current_stop_sequence (es decir, la parada a la que hace referencia) está determinado por current_status. Si falta current_status se asume IN_TRANSIT_TO. |
| **stop_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Identifica la parada actual. El valor debe ser el mismo que en stops.txt en el feed GTFS correspondiente. |
| **current_status** | [VehicleStopStatus](#enum-vehiclestopstatus) | Opcional | Uno | El estado exacto del vehículo con respecto a la parada actual. Se ignora si falta current_stop_sequence. |
| **timestamp** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Momento en el que se midió la posición del vehículo. En el tiempo POSIX (es decir, número de segundos desde el 1 de enero de 1970, 00:00:00 UTC). |
| **congestion_level** | [CongestionLevel](#enum-congestionlevel) | Opcional | Uno |
| _**occupancy_status**_ | _[OccupancyStatus](#enum-occupancystatus)_ | _Opcional_ | Uno | El grado de ocupación de los pasajeros del vehículo. <br> ** Precaución: ** este campo aún es ** experimental ** y está sujeto a cambios. Puede ser adoptado formalmente en el futuro.|

## _enum_ VehicleStopStatus

#### Valores

| _**Valor**_ | _**Comentario**_ |
|-------------|---------------|
| **INCOMING_AT** | El vehículo está a punto de llegar a la parada (en una pantalla de parada, el símbolo del vehículo normalmente parpadea). |
| **STOPPED_AT** | El vehículo está parado en la parada. |
| **IN_TRANSIT_TO** | El vehículo ha salido de la parada anterior y está en tránsito. |

## _enum_ CongestionLevel

Nivel de congestión que está afectando a este vehículo.

#### Valores

| _**Valor**_ |
|-------------|
| **UNKNOWN_CONGESTION_LEVEL** |
| **RUNNING_SMOOTHLY** |
| **STOP_AND_GO** |
| **CONGESTION** |
| **SEVERE_CONGESTION** |

## _enum OccupancyStatus_

El grado de ocupación de los pasajeros del vehículo.

** Precaución: ** este campo aún es ** experimental ** y está sujeto a cambios. Puede ser adoptado formalmente en el futuro.

#### _Valores_

| _**Valor**_ | _**Comentario**_ |
|-------------|---------------|
| _**EMPTY**_ | _El vehículo se considera vacío según la mayoría de las medidas, y tiene pocos o ningún pasajero a bordo, pero sigue aceptando pasajeros._ |
| _**MANY_SEATS_AVAILABLE**_ | _El vehículo tiene un gran porcentaje de asientos disponibles. El porcentaje de asientos libres del total de asientos disponibles que se considerará lo suficientemente grande como para caer en esta categoría se determina a discreción del productor._ |
| _**FEW_SEATS_AVAILABLE**_ | _El vehículo tiene un pequeño porcentaje de asientos disponibles. El porcentaje de asientos libres del total de asientos disponibles que se considerará lo suficientemente pequeño como para caer en esta categoría se determina a discreción del productor._ |
| _**STANDING_ROOM_ONLY**_ | _El vehículo actualmente solo puede acomodar a pasajeros de pie._ |
| _**CRUSHED_STANDING_ROOM_ONLY**_ | _El vehículo actualmente solo puede acomodar a pasajeros de pie y tiene espacio limitado para ellos._ |
| _**FULL**_ | _El vehículo se considera lleno por la mayoría de las medidas, pero aún puede permitir que los pasajeros suban a bordo._ |
| _**NOT_ACCEPTING_PASSENGERS**_ | _El vehículo no puede aceptar pasajeros._ |

## _message_ Alert

Una alerta, que indica algún tipo de incidente en la red de transporte público.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **active_period** | [TimeRange](#message-timerange) | Opcional | Muchos | Hora en que se debe mostrar la alerta al usuario. Si falta, la alerta se mostrará mientras aparezca en el feed. Si se dan múltiples rangos, la alerta se mostrará durante todos ellos. |
| **informed_entity** | [EntitySelector](#message-entityselector) | Obligatorio | Muchos | Entidades cuyos usuarios debemos notificar de esta alerta. Se debe proporcionar al menos una inform_entity. |
| **cause** | [Cause](#enum-cause) | Opcional | Uno |
| **effect** | [Effect](#enum-effect) | Opcional | Uno |
| **url** | [TranslatedString](#message-translatedstring) | Opcional | Uno | La URL que proporciona información adicional sobre la alerta. |
| **header_text** | [TranslatedString](#message-translatedstring) | Obligatorio | Uno | Encabezado por la alerta. Esta cadena de texto sin formato se resaltará, por ejemplo, en negrita. |
| **description_text** | [TranslatedString](#message-translatedstring) | Obligatorio | Uno | Descripción para la alerta. Esta cadena de texto sin formato se formateará como el cuerpo de la alerta (o se mostrará por una solicitud explícita de "leer más" por parte del usuario). La información en la descripción debe reforzar la información del encabezado. |
| ** tts_header_text ** | [TranslatedString](#message-translatedstring) | Opcional | Uno | Texto que contiene el encabezado de la alerta que se utilizará para las implementaciones de texto a voz. Este campo es la versión de texto a voz de header_text. Debería contener la misma información que header_text pero con un formato tal que pueda leerse como texto a voz (por ejemplo, abreviaturas eliminadas, números escritos con letras, etc.) ** Precaución: ** este campo aún es ** experimental ** , y sujeto a cambios. Puede ser adoptado formalmente en el futuro. |
| ** tts_description_text ** | [TranslatedString](#message-translatedstring) | Opcional | Uno | Texto que contiene una descripción de la alerta que se utilizará para las implementaciones de texto a voz. Este campo es la versión de texto a voz de description_text. Debe contener la misma información que description_text pero formateada de modo que pueda leerse como texto a voz (por ejemplo, abreviaturas eliminadas, números escritos con letras, etc.) ** Precaución:** este campo todavía es **experimental**, y sujeto a cambios. Puede ser adoptado formalmente en el futuro. |
| **severity_level** | [SeverityLevel](#enum-severitylevel) | Opcional | Uno | Gravedad de la alerta. <br> ** Precaución: ** este campo aún es ** experimental ** y está sujeto a cambios. Puede ser adoptado formalmente en el futuro. |

## _enum_ Cause

Causa de esta alerta.

#### Valores

| _**Valor**_ |
|-------------|
| **UNKNOWN_CAUSE** |
| **OTHER_CAUSE** |
| **TECHNICAL_PROBLEM** |
| **STRIKE** |
| **DEMONSTRATION** |
| **ACCIDENT** |
| **HOLIDAY** |
| **WEATHER** |
| **MAINTENANCE** |
| **CONSTRUCTION** |
| **POLICE_ACTIVITY** |
| **MEDICAL_EMERGENCY** |

## _enum_ Effect

El efecto de este problema en la entidad afectada.

#### Valores

| _**Valor**_ |
|-------------|
| **NO_SERVICE** |
| **REDUCED_SERVICE** |
| **SIGNIFICANT_DELAYS** |
| **DETOUR** |
| **ADDITIONAL_SERVICE** |
| **MODIFIED_SERVICE** |
| **OTHER_EFFECT** |
| **UNKNOWN_EFFECT** |
| **STOP_MOVED** |
| **NO_EFFECT** |
| **ACCESSIBILITY_ISSUE** |

## _enum_ SeverityLevel

La severidad de la alerta.

** Precaución: ** este campo aún es ** experimental ** y está sujeto a cambios. Puede ser adoptado formalmente en el futuro.

#### Valores
| _**Valor**_ |
|-------------|
| **UNKNOWN_SEVERITY** |
| **INFO** |
| **WARNING** |
| **SEVERE** |

## _message_ TimeRange

Un intervalo de tiempo. El intervalo se considera activo en el tiempo `t` si` t` es mayor o igual que la hora de inicio y menor que la hora de finalización.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **start** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | Hora de inicio, en hora POSIX (es decir, número de segundos desde el 1 de enero de 1970, 00:00:00 UTC). Si falta, el intervalo comienza en menos infinito. Si se proporciona un TimeRange, se debe proporcionar un inicio o un final, ambos campos no pueden estar vacíos. |
| **end** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | Hora de finalización, en hora POSIX (es decir, número de segundos desde el 1 de enero de 1970, 00:00:00 UTC). Si falta, el intervalo termina en más infinito. Si se proporciona un TimeRange, se debe proporcionar un inicio o un final, ambos campos no pueden estar vacíos. |

## _message_ Position

Una posición geográfica de un vehículo.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **latitude** | [float](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obligatorio | Uno | Grados norte, en el sistema de coordenadas WGS-84. |
| **longitude** | [float](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obligatorio | Uno | Grados este, en el sistema de coordenadas WGS-84. |
| **bearing** | [float](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Orientación, en grados, en sentido horario desde norte verdadero, es decir, 0 es norte y 90 es este. Esto puede ser el rumbo de la brújula o la dirección hacia la siguiente parada o la ubicación intermedia. Esto no debe deducirse de la secuencia de posiciones anteriores, que los clientes pueden calcular a partir de datos anteriores. |
| **odometer** | [double](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Valor del odómetro, en metros. |
| **speed** | [float](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Velocidad momentánea medida por el vehículo, en metros por segundo. |

## _message_ TripDescriptor

Un descriptor que identifica una sola instancia de un viaje GTFS.

Para especificar una única instancia de viaje, en muchos casos, un `trip_id` por sí mismo es suficiente. Sin embargo, los siguientes casos requieren información adicional para resolver en una sola instancia de viaje:
* Para los viajes definidos en frequencies.txt, se requieren `start_date` y` start_time` además de `trip_id`
* Si el viaje dura más de 24 horas, o se retrasa de manera tal que colisionara con un viaje programado al día siguiente, se requiere `start_date` además de` trip_id`
* Si no se puede proporcionar el campo `trip_id`, entonces deben proporcionarse `route_id`, `direction_id`,` start_date` y `start_time`

En todos los casos, si se proporciona `route_id` además de` trip_id`, entonces `route_id` debe ser el mismo` route_id` asignado al viaje dado en GTFS trips.txt.

El campo `trip_id` no puede, por sí mismo o en combinación con otros campos TripDescriptor, ser utilizado para identificar múltiples instancias de viaje. Por ejemplo, un TripDescriptor nunca debe especificar trip_id por sí mismo para GTFS frequencies.txt exact_times = 0 trips, porque se requiere start_time también para resolverse en una única instancia de viaje que comienza a una hora específica del día. Si el TripDescriptor no se resuelve en una sola instancia de viaje (es decir, se resuelve en cero o en varias instancias de viaje), se considera un error y la entidad que contiene el TripDescriptor erróneo puede ser descartada por los consumidores.

Tenga en cuenta que si no se conoce el trip_id, entonces los identificadores de secuencia de estación en TripUpdate no son suficientes, y también se deben proporcionar los stop_ids. Además, se deben proporcionar tiempos absolutos de llegada / salida.

TripDescriptor.route_id no se puede usar dentro de un Alert EntitySelector  para especificar una alerta para toda la ruta que afecte a todos los viajes para una ruta; use EntitySelector.route_id en su lugar.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **trip_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | El trip_id del feed GTFS al que hace referencia este selector. Para viajes no basados en frecuencia (viajes no definidos en GTFS frequencies.txt), este campo es suficiente para identificar el viaje de forma única. Para los viajes basados en frecuencia definidos en GTFS frequencies.txt, trip_id, start_time y start_date son todos necesarios. Para viajes basados en horarios (viajes no definidos en GTFS frequencies.txt), trip_id solo se puede omitir si el viaje se puede identificar de forma única mediante una combinación de route_id, direction_id, start_time y start_date, y se proporcionan todos esos campos. |
| **route_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | El route_id del GTFS al que hace referencia este selector. Si se omite trip_id, route_id, direction_id, start_time y schedule_relationship=SCHEDULED deben configurarse para identificar una instancia de viaje. TripDescriptor.route_id no debe usarse dentro de un Alert EntitySelector para especificar una alerta de ruta que afecte a todos los viajes de una ruta; use EntitySelector.route_id en su lugar. |
| **direction_id** | [uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | La direction_id del archivo GTFS feed trips.txt, que indica la dirección de viaje para los viajes a los que hace referencia este selector. Si se omite trip_id, se debe proporcionar direction_id. <br> ** Precaución: ** este campo aún es ** experimental ** y está sujeto a cambios. Puede ser adoptado formalmente en el futuro. <br>|
| **start_time** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | La hora de inicio inicialmente programada de esta instancia de viaje. Cuando el trip_id corresponde a un viaje no basado en frecuencia, este campo debe omitirse o debe ser igual al valor en el feed GTFS. Cuando trip_id se corresponde con un viaje basado en frecuencia definido en GTFS frequencies.txt, start_time es obligatorio y debe especificarse para las actualizaciones de viaje y las posiciones del vehículo. Si el viaje corresponde a exact_times=1 registro GTFS, entonces start_time debe ser un múltiplo (incluido cero) de headway_secs más tarde que frequencies.txt start_time durante el período de tiempo correspondiente. Si el viaje corresponde a exact_times=0, entonces start_time puede ser arbitrario, y se espera inicialmente que sea la primera salida del viaje. Una vez establecido, el start_time de este viaje exact_times=0 basado en frecuencia debe considerarse inmutable, incluso si la primera hora de salida cambia, ese cambio de hora puede reflejarse en un StopTimeUpdate. Si se omite trip_id, se debe proporcionar start_time. El formato y la semántica del campo son los mismos que los de GTFS/frequencies.txt/start_time, por ejemplo, 11:15:35 o 25:15:35. |
| **start_date** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | La fecha de inicio de esta instancia de viaje en formato AAAAMMDD. Para viajes programados (viajes no definidos en GTFS frequencies.txt), este campo debe proporcionarse para desambiguar los viajes que llegan tan tarde como para coincidir con un viaje programado para el día siguiente. Por ejemplo, para un tren que sale a las 8:00 y a las 20:00 todos los días, y llega 12 horas tarde, habrá dos viajes distintos al mismo tiempo. Este campo se puede proporcionar, pero no es obligatorio para los horarios en los que tales conflictos son imposibles, por ejemplo, un servicio que se ejecuta en un horario por hora en el que un vehículo que se retrasa una hora ya no se considera que esté relacionado con el horario. Este campo es obligatorio para los viajes basados en frecuencia definidos en GTFS frequencies.txt. Si se omite trip_id, se debe proporcionar start_date. |
| **schedule_relationship** | [ScheduleRelationship](#enum-schedulerelationship-1) | Opcional | Uno | La relación entre este viaje y el horario estático. Si TripDescriptor se proporciona en un Alert `EntitySelector`, los consumidores ignoran el campo` schedule_relationship` cuando identifican la instancia de viaje correspondiente.

## _enum_ ScheduleRelationship

La relación entre este viaje y el horario estático. Si un viaje se realiza de acuerdo con el cronograma temporal, no se refleja en GTFS, entonces no se debe marcar como SCHEDULED, sino como ADDED.

#### Valores

| _**Valor**_ | _**Comentario**_ |
|-------------|---------------|
| **SCHEDULED** | El viaje que se está realizando de acuerdo con su programación de GTFS, o está lo suficientemente cerca del viaje programado para estar asociado con él. |
| **ADDED** | Un viaje adicional que se agregó además de un programa en realización, por ejemplo, para reemplazar un vehículo roto o para responder a la aumenta repentina de pasajeros. |
| **UNSCHEDULED** | Un viaje que se está ejecutando sin un horario asociado: este valor se usa para identificar viajes definidos en GTFS frequencies.txt con exact_times = 0. No se debe usar para describir viajes no definidos en GTFS frequencies.txt, o viajes en GTFS frequencies.txt con exact_times = 1. |
| **CANCELED** | Un viaje que existía en el horario pero fue eliminado. |

## _message_ VehicleDescriptor

Información de identificación del vehículo que realiza el viaje.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Identificación interna del sistema del vehículo. Debería ser **único** por vehículo, y se utiliza para rastrear el vehículo a medida que avanza a través del sistema. Esta identificación no debe hacerse visible para el usuario final; para ello usar el campo **etiqueta** |
| **label** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | Etiqueta visible para el usuario, es decir, algo que debe mostrarse al pasajero para ayudar a identificar el vehículo correcto. |
| **license_plate** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Uno | La matrícula del vehículo. |

## _message_ EntitySelector

Un selector para una entidad en un feed GTFS. Los valores de los campos deben corresponder a los campos apropiados en el feed GTFS. Se debe dar al menos un especificador. Si se dan varias, se deben interpretar como unidas por el operador lógico `AND`. Además, la combinación de especificadores debe coincidir con la información correspondiente en el feed GTFS. En otras palabras, para que una alerta se aplique a una entidad en GTFS, debe coincidir con todos los campos de EntitySelector proporcionados. Por ejemplo, un EntitySelector que incluye los campos `route_id: "5"` y `route_type: "3"` se aplica solo al bus de `route_id: "5"` - no se aplica a ninguna otra ruta de `route_type:  "3"`. Si un productor quiere que se aplique una alerta a `route_id: "5"` así como `route_type: "3"`, debe proporcionar dos EntitySelectors separados, uno que haga referencia a `rroute_id: "5"` y otro que haga referencia a `route_type: "3"`.

Se debe proporcionar al menos un especificador: todos los campos en un EntitySelector no pueden estar vacíos.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **agency_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | El agency_id del feed GTFS al que hace referencia este selector.
| **route_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | El route_id del GTFS al que hace referencia este selector. Si se proporciona direction_id, route_id también debe proporcionarse.
| **route_type** | [int32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | route_type del GTFS al que hace referencia este selector.
| **direction_id** | [uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | El direction_id del archivo GTFS feed trips.txt, usado para seleccionar todos los viajes en una dirección para una ruta, especificado por route_id. Si se proporciona direction_id, route_id también debe proporcionarse. <br> ** Precaución: ** este campo aún es ** experimental ** y está sujeto a cambios. Puede ser adoptado formalmente en el futuro. <br>|
| **trip** | [TripDescriptor](#message-tripdescriptor) | Condicionalmente obligatorio | Uno | La instancia de viaje del GTFS al que hace referencia este selector. Este TripDescriptor debe resolverse en una sola instancia de viaje en los datos de GTFS (por ejemplo, un productor no puede proporcionar solo un trip_id para exact_times=0 viajes). Si el campo ScheduleRelationship se completa dentro de este TripDescriptor, los consumidores lo ignorarán cuando intenten identificar el viaje GTFS.
| **stop_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | El stop_id del feed GTFS al que hace referencia este selector.

## _message_ TranslatedString

Un mensaje internacionalizado que contiene versiones por idioma de un fragmento de texto o una URL. Una de las cadenas de un mensaje será recogida. La resolución procede de la siguiente manera: si el idioma de la interfaz de usuario coincide con el código de idioma de una traducción, se selecciona la primera traducción coincidente. Si un idioma predeterminado de la interfaz de usuario (por ejemplo, inglés) coincide con el código de idioma de una traducción, se selecciona la primera traducción coincidente. Si alguna traducción tiene un código de idioma no especificado, se elige esa traducción.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **translation** | [Translation](#message-translation) | Obligatorio | Muchos | Se debe proporcionar al menos una traducción. |

## _message_ Translation

Una cadena localizada asignada a un idioma.

#### Campos

| _**Nombre del campo**_ | _**Tipo**_ | _**Obligatorio**_ | _ ** Cardinalidad ** _ | _**Descripción**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **text** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obligatorio | Uno | Una cadena UTF-8 que contiene el mensaje. |
| **language** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Condicionalmente obligatorio | Uno | Código de idioma BCP-47. Se puede omitir si se desconoce el idioma o si no se realiza ninguna internacionalización para el feed. Como máximo, una traducción puede tener una etiqueta de idioma no especificada: si hay más de una traducción, se debe proporcionar el idioma. |
