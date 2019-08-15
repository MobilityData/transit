Um feed GTFS em tempo real permite que agências de transporte público forneçam aos usuários informações em tempo real sobre interrupções em seus serviços (estações fechadas, linhas inoperantes, atrasos importantes, etc.), localização de seus veículos e horários de chegada estimados.

A versão 2.0 da especificação do feed está exposta e documentada neste site. As versões válidas até então são "2.0" e "1.0".

### Definição dos Termos

#### Obrigatório

No GTFS-realtime v2.0 e versões superiores, a coluna *Requisito* descreve quais campos devem ser fornecidos por um produtor, para que os dados de trânsito sejam válidos e funcionais a aplicativos consumidores desses dados.

Os seguintes valores são usados no campo *Requisito*:

*   **Obrigatório**: campo que deve obrigatoriamente ser fornecido por um produtor de conteúdo GTFS-realtime.
*   **Facultativo**: campo que é obrigatório sob certas condições a serem descritas no campo *Descrição* do arquivo. Nos outros casos, esse campo será opcional.
*   **Opcional**: campo opcional e não precisa obrigatoriamente ser implementado pelos produtores. No entanto, se os dados estiverem disponíveis nos sistemas automáticos de localização de veículos (p. ex. VehiclePosition `timestamp`), recomenda-se que sejam fornecidos sempre que possível.

*Note que os requisitos semânticos não foram definidos no GTFS-realtime versão 1.0 e, portanto, os feeds com `gtfs_realtime_version` da versão `1` podem não atender a esses requisitos (consulte a [proposta de definição para requisitos semânticos](https://github.com/google/transit/pull/64)  (em inglês) para maiores detalhes).*

#### Cardinalidade

*Cardinalidade* representa o quantidade de elementos que podem ser fornecidos para um campo específico, com os seguintes valores:

* **Um** - Um único elemento deve ser fornecido para este campo. Veja o mapeamento do valor para [cardinalidades *obrigatórias* e *opcionais* no buffer de protocolo](https://developers.google.com/protocol-buffers/docs/proto#simple) (em inglês).
* **Vários** - Vários elementos (0, 1 ou mais) podem ser fornecidos neste campo. Veja o mapeamento do valor para [cardinalidades *repetidas* no buffer de protocolo](https://developers.google.com/protocol-buffers/docs/proto#simple) (em inglês).

Sempre atente aos campos *Requisito* e *Descrição* para ver quando um campo é obrigatório, facultativo ou opcional. Por favor, consulte [`gtfs-realtime.proto`](https://github.com/google/transit/blob/master/gtfs-realtime/proto/gtfs-realtime.proto) (em inglês) para a cardinalidade do buffer de protocolo.

#### Tipos de dados do buffer de protocolo

Os seguintes buffers de protocolo são usados para descrever elementos no feed:

*   **mensagem**: Tipo complexo
*   **enum**: Lista de valores fixos

#### Campos experimentais

Campos marcados como **experimentais** estão sujeitos a alterações e ainda não foram formalmente implementados. Um campo **experimental** pode ser adotado formalmente no futuro.

## Índice de Elementos

*   [FeedMessage](#message-feedmessage)
    *   [FeedHeader](#message-feedheader)
        *   [Incrementality](#enum-incrementality)
    *   [FeedEntity](#message-feedentity)
        *   [TripUpdate](#message-tripupdate)
            *   [TripDescriptor](#message-tripdescriptor)
                *   [ScheduleRelationship](#enum-schedulerelationship-1)
            *   [VehicleDescriptor](#message-vehicledescriptor)
            *   [StopTimeUpdate](#message-stoptimeupdate)
                *   [StopTimeEvent](#message-stoptimeevent)
                *   [ScheduleRelationship](#enum-schedulerelationship)
        *   [VehiclePosition](#message-vehicleposition)
            *   [TripDescriptor](#message-tripdescriptor)
                *   [ScheduleRelationship](#enum-schedulerelationship-1)
            *   [VehicleDescriptor](#message-vehicledescriptor)
            *   [Position](#message-position)
            *   [VehicleStopStatus](#enum-vehiclestopstatus)
            *   [CongestionLevel](#enum-congestionlevel)
            *   [OccupancyStatus](#enum-occupancystatus)
        *   [Alert](#message-alert)
            *   [TimeRange](#message-timerange)
            *   [EntitySelector](#message-entityselector)
                *   [TripDescriptor](#message-tripdescriptor)
                    *   [ScheduleRelationship](#enum-schedulerelationship-1)
            *   [Cause](#enum-cause)
            *   [Effect](#enum-effect)
            *   [TranslatedString](#message-translatedstring)
                *   [Translation](#message-translation)
            *   [SeverityLevel](#enum-severitylevel)

# Elementos

## _message_ FeedMessage

O conteúdo da mensagem no feed. Cada mensagem é obtida como resposta de uma requisição HTTP GET adequada. O feed em tempo real sempre estará relacionado a um feed GTFS já existente. Todos os ids de entidade são determinados em relação ao feed GTFS correspondente.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
|**header** | [FeedHeader](#message-feedheader) | Obrigatório | Um | Metadados deste feed e mensagens do feed. |
|**entity** | [FeedEntity](#message-feedentity) | Facultativo | Vários | Conteúdo do feed. Este campo deve ser fornecido sempre que houver informações disponíveis em tempo real para o sistema de trânsito. Se o campo estiver vazio, os consumidores de dados entenderão que não há informações em tempo real disponíveis no sistema. |

## _mensagem_ FeedHeader

Metadados de um feed, inclusos nas mensagens deste.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **gtfs_realtime_version** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obrigatório | Um | Versão da especificação do feed. A versão atual é 2.0. |
| **incrementality** | [Incrementality](#enum-incrementality) | Obrigatório | Um |
| **timestamp** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obrigatório | Um | Este registro de data e hora identifica o momento em que o conteúdo deste feed foi criado (hora do servidor). De acordo com a padronização POSIX (contagem em segundos desde 1º de janeiro de 1970 - 00:00:00 UTC). Para evitar discrepâncias de tempo entre sistemas que produzem e consomem informações em tempo real, é altamente recomendável basear o registro de tempo a partir de um servidor de tempo. É completamente aceitável usar Stratum 3 ou servidores inferiores, já que diferenças de poucos segundos são toleráveis. |

## _enum_ Incrementality

Determina se a atualização do feed é incremental.

*   **FULL_DATASET**: essa atualização do feed sobrescreve todas as informações de tempo real anteriores. Assim, é esperado que esta atualização forneça um compilado de todas as informações em tempo real conhecidas.
*   **DIFFERENTIAL**: atualmente, este modo **não é suportado** e o comportamento do feed que usa este modo **não foi especificado**. Existem alguns debates na [lista de e-mails do GTFS Realtime](http://groups.google.com/group/gtfs-realtime) (em inglês) sobre a definição completa do comportamento no modo DIFFERENTIAL, e quando o debate finalizar, atualizaremos a documentação.

#### Valores

| _**Valor**_ |
|-------------|
| **FULL_DATASET** |
| **DIFFERENTIAL** |

## _message_ FeedEntity

Definição (ou atualização dos dados) de uma entidade no feed de trânsito. Se a entidade não estiver sendo excluída, um dos campos 'trip_update', 'vehicle' ou 'alert' devem ser precisamente fornecidos.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obrigatório | Um | Identifica especificamente um feed para esta entidade. Os ids são usados apenas para fornecer suporte a incrementos. As reais entidades do feed devem ser explicitamente definidas por seus respectivos seletores (veja EntitySelector abaixo para detalhes). |
| **is_deleted** | [bool](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Estabelece se esta entidade dever ser deletada. Deve ser fornecido apenas para feeds com incremento de DIFFERENCIAL - este campo NÃO deve ser fornecido para feeds com incremento de FULL_DATASET. |
| **trip_update** | [TripUpdate](#message-tripupdate) | Facultativo | Um | Dados sobre os atrasos de partida em tempo real de um trajeto. Pelo menos um dos campos trip_update, vehicle ou alert deve ser fornecido - estes campos não podem estar todos vazios. |
| **vehicle** | [VehiclePosition](#message-vehicleposition) | Facultativo | Um | Dados sobre a posição em tempo real de um veículo. Pelo menos um dos campos trip_update, vehicle ou alert deve ser fornecido - estes campos não podem estar todos vazios. |
| **alert** | [Alert](#message-alert) | Facultativo | Um | Dados sobre o alerta em tempo real. Pelo menos um dos campos trip_update, vehicle ou alert deve ser fornecido - estes campos não podem estar todos vazios. |

## _message_ TripUpdate

Atualização em tempo real sobre o deslocamento do veículo ao longo de uma viagem. Por favor, consulte também a discussão geral sobre [entidades de atualização de viagens](trip-updates.md).

Dependendo do valor em ScheduleRelationship, um TripUpdate pode definir:

*   Um trajeto que avança conforme o cronograma.
*   Um trajeto que avança por uma rota, mas não possui cronograma fixo.
*   Uma trajeto que foi adicionado ou removido do cronograma.

As atualizações podem ser para eventos de chegada/partida previstos ou para eventos passados já ocorridos. Na maioria dos casos, informações sobre eventos passados ​​tendem à incerteza, por isso o valor recomendado para estas previsões é 0\. Podem haver casos onde é permitido que o valor para essa incerteza seja diferente de 0 para tais eventos passados. Se o valor for diferente de 0, a atualização trata de uma previsão aproximada para um trajeto que não foi concluído, a medição não foi precisa, ou a atualização era uma previsão passada que não foi verificada após o evento ter ocorrido.

Observe que a atualização pode descrever um trajeto já concluído. Para isso, basta fornecer uma atualização para a última parada do trajeto. Se a hora de chegada da última parada estiver no passado, o usuário concluirá que o trajeto inteiro está no passado (é possível, embora irrelevante, também fornecer atualizações para as paradas anteriores). Essa opção é mais relevante para um trajeto concluído antes do previsto, mas, de acordo com o cronograma, ainda estaria ocorrendo. Remover as atualizações para este trajeto pode fazer o usuário acreditar que ela ainda está em andamento. Observe que o produtor de conteúdo do feed tem permissão, mas não obrigatoriedade, de limpar atualizações anteriores - esse é um dos casos em que proceder assim seria útil.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisto**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **trip** | [TripDescriptor](#message-tripdescriptor) | Obrigatório | Um | O trajeto ao qual esta mensagem se aplica. Apenas uma entidade TripUpdate é permitida para cada trajeto em curso. Se não houver nenhuma, significa que não há informações de previsão disponíveis. Isso *não* significa que o trajeto está seguindo o cronograma. |
| **vehicle** | [VehicleDescriptor](#message-vehicledescriptor) | Opcional | Um | Informações adicionais sobre o veículo que está sendo usado neste trajeto. |
| **stop_time_update** | [StopTimeUpdate](#message-stoptimeupdate) | Facultativo | Vários | Atualizações para StopTimes do trajeto (tanto futuros, como previsões e, em alguns casos, passados, ou seja, já ocorridos). As atualizações devem seguir a ordem stop_sequence e serem aplicadas a todas as paradas seguintes até o próximo stop_time_update. Pelo menos um stop_time_update deve ser fornecido para o trajeto, a menos que o trip.schedule_relationship for CANCELED - se o trajeto for cancelado, não é necessário fornecer stop_time_updates. |
| **timestamp** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Momento exato em que o progresso do veículo em tempo real foi medido. De acordo com a padronização POSIX (contagem em segundos desde 1º de janeiro de 1970 às 00:00:00 UTC). |
| **delay** | [int32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | A alteração no cronograma do trajeto. Só será definido como atraso se houver algum cronograma no GTFS.<br>O atraso (em segundos) pode ser positivo (o que significa que o veículo está atrasado) ou negativo (o que significa que o veículo está adiantado). Atraso de 0 significa que o veículo está exatamente no horário. <br>Os atrasos em StopTimeUpdates tomam como base as informações na evolução de percurso do trajeto, de modo que o atraso é propagado apenas até a próxima parada ao longo deste com um valor de atraso de StopTimeUpdate fornecido.<br>Os produtores de conteúdo do feed são fortemente aconselhados a fornecer um valor de TripUpdate.timestamp indicando quando o atraso foi registrado pela última vez, para confirmar o grau de atualização dos dados. <br>**Atenção**: este campo ainda é **experimental** e sujeito a alterações. Poderá ser formalmente adotado no futuro.|

## _message_ StopTimeEvent

Informação de contagem de tempo para um único evento previsto (chegada ou partida). A contagem consiste em atraso e/ou estimativa; e incerteza.

*   "delay" deve ser usado quando há uma previsão relativa a um cronograma GTFS existente.
*   "time" deve ser fornecido havendo ou não um cronograma previsto. Se ambos "time" e "delay" forem fornecidos, "time" será prioritário (embora "time", normalmente, quando dado para um trajeto programado, é igual à soma do tempo previsto no GTFS + "delay").

"Uncertainty" se aplica igualmente ao "tempo" e "delay". A incerteza basicamente especifica um erro esperado convertido num atraso real (mas note que ainda não define seu significado estatístico preciso). É possível que a incerteza seja 0, por exemplo, para trens que são acionados sob o controle cronometrado de um computador.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **delay** | [int32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O 'delay" (em segundos) pode ser positivo (o que significa que o veículo está atrasado) ou negativo (o que significa que o veículo está adiantado). "Delay" 0 significa que o veículo está exatamente no horário. Tanto "delay" quanto "time" devem ser fornecidos dentro de um StopTimeEvent - não podem estar ambos vazios.  |
| **time** | [int64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | Tempo absoluto do evento, de acordo com o padrão POSIX (contagem em segundos desde 1º de janeiro de 1970 às 00:00:00 UTC). Tanto "delay" quanto "time" devem ser fornecidos dentro de um StopTimeEvent - não podem estar ambos vazios.  |
| **uncertainty** | [int32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Se "uncertainty" for omitido, será interpretado como desconhecido. Para especificar uma previsão como totalmente correta, defina "uncertainty" como 0. |

## _message_ StopTimeUpdate

Atualização em tempo real para eventos de chegada e/ou partida em determinada parada de um trajeto. Favor consultar também a discussão geral sobre atualizações de tempo em paradas [TripDescriptor](#message-tripdescriptor) e [entidades de atualizações de viagem](trip-updates.md).

Atualizações podem ser fornecidas para eventos passados ​​e futuros. O produtor de conteúdo pode, embora sem obrigação, abandonar eventos passados.
A atualização está vinculada a uma parada específica por stop_sequence ou stop_id, portanto, um desses campos deve ser fornecido. Se o mesmo stop_id for visitado mais de uma vez em um trajeto, stop_sequence deverá ser fornecida em todos os StopTimeUpdates para esse stop_id correspondente ao trajeto.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **stop_sequence** | [uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | Deve ser o mesmo que em stop_times.txt no feed GTFS correspondente. Tanto stop_sequence quanto stop_id devem ser fornecidos dentro de um StopTimeUpdate - não podem estar ambos vazios. stop_sequence é obrigatório para trajetos que visitam o mesmo stop_id mais de uma vez (p.ex., uma linha circular) para especificar a previsão de cada parada correspondente. |
| **stop_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | Deve ser o mesmo que em stops.txt no feed GTFS correspondente. Tanto stop_sequence quanto stop_id devem ser fornecidos dentro de um StopTimeUpdate - não podem estar ambos vazios. |
| **arrival** | [StopTimeEvent](#message-stoptimeevent) | Facultativo | Um | Se schedule_relationship estiver vazio ou SCHEDULED, "arrival" ou "departure" devem ser fornecidos dentro de um StopTimeUpdate - não podem estar ambos vazios. "arrival" e "departure" podem estar vazios quando o schedule_relationship for SKIPPED. Se schedule_relationship for NO_DATA, "arrival" e "departure" deverão estar vazios. |
| **departure** | [StopTimeEvent](#message-stoptimeevent) | Facultativo | Um | Se schedule_relationship estiver vazio ou SCHEDULED, "arrival" ou "departure" devem ser fornecidos dentro de um StopTimeUpdate - não podem estar ambos vazios. "arrival" e "departure" podem estar vazios quando schedule_relationship for SKIPPED. Se schedule_relationship for NO_DATA, "arrival" e "departure" deverão estar vazios. |
| **schedule_relationship** | [ScheduleRelationship](#enum-schedulerelationship) | Opcional | Um | A relação padrão é SCHEDULED. |

## _enum_ ScheduleRelationship

A relação entre este StopTime e o cronograma estático.

#### Valores

| _**Valor**_ | _**Comentário**_ |
|-------------|---------------|
| **SCHEDULED** | O veículo avança de acordo com seu cronograma estático de paradas, embora não necessariamente de acordo com os horários da programação. Esse é o comportamento **padrão**. Pelo menos um "arrival" e "departure" devem ser fornecidos. |
| **SKIPPED** | A parada não acontece, ou seja, o veículo não para neste ponto. "arrival" e "departure" são opcionais. Quando marcada como `SKIPPED`, essa definição não se propaga às paradas seguintes (ou seja, o veículo deve parar subsequentemente de acordo com a programação do trajeto, a não ser que essas paradas também tenham um `stop_time_update` com `schedule_relationship: SKIPPED`). O atraso da parada anterior se *propaga* sobre uma parada `SKIPPED`. Em outras palavras,  se um `stop_time_update` com um `arrival` ou `departure` não definidos para uma parada posterior à `SKIPPED`, a previsão se repetirá como `SKIPPED` após a parada `SKIPPED` e a sequência de paradas no trajeto, até que seja fornecido um `stop_time_update` para uma parada subsequente.  |
| **NO_DATA** | Nenhum dado é informado para esta parada. Indica que não há informações em tempo real disponíveis. Quando uma parada é marcada como NO_DATA esta definição se propaga ao decorrer paradas seguintes. Esta é a maneira recomendada de especificar a partir de qual parada não há informações em tempo real. Quando NO_DATA está definido, nem "arrival" nem "departure" precisam ser fornecidos. |

## _message_ VehiclePosition

Posição em tempo real de determinado veículo.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **trip** | [TripDescriptor](#message-tripdescriptor) | Opcional | Um | O trajeto que este veículo está realizando. Pode ser vazio ou parcial se o veículo não puder ser identificado em determinada instância do trajeto. |
| **vehicle** | [VehicleDescriptor](#message-vehicledescriptor) | Opcional | Um | Informações adicionais sobre o veículo que está percorrendo este trajeto. Cada entrada deve ter um ID **exclusivo** para cada veículo. |
| **position** | [Position](#message-position) | Opcional | Um | Posição atual do veículo. |
| **current_stop_sequence** | [uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | O índice de paradas subsequentes a partir do ponto atual. O significado de current_stop_sequence (isto é, a parada referida) é determinada por current_status. Se current_status não for informado IN_TRANSIT_TO será assumido. |
| **stop_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Identifica a parada atual. O valor deve ser o mesmo que em stops.txt no feed GTFS correspondente. |
| **current_status** | [VehicleStopStatus](#enum-vehiclestopstatus) | Opcional | Um | O status exato do veículo em relação à parada atual. Será ignorado se current_stop_sequence estiver faltando. |
| **timestamp** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Optional | Um | Momento no qual a posição do veículo foi medida. De acordo com a padronização POSIX (contagem em segundos desde 1º de janeiro de 1970 às 00:00:00 UTC). |
| **congestion_level** | [CongestionLevel](#enum-congestionlevel) | Opcional | Um |
| _**occupancy_status**_ | _[OccupancyStatus](#enum-occupancystatus)_ | _Opcional_ | Um | O grau de lotação de passageiros no veículo.<br>**Atenção**: este campo ainda é **experimental** e está sujeito a alterações. Poderá ser formalmente adotado no futuro.|

## _enum_ VehicleStopStatus

#### Valores

| _**Valor**_ | _**Comentário**_ |
|-------------|---------------|
| **INCOMING_AT** | O veículo está prestes a chegar na parada (no display da parada, o símbolo do veículo geralmente pisca). |
| **STOPPED_AT** | O veículo está parado na parada. |
| **IN_TRANSIT_TO** | O veículo partiu da parada anterior e está em trânsito. |

## _enum_ CongestionLevel

Nível de congestionamento que afeta o veículo.

#### Valores

| _**Valor**_ |
|-------------|
| **UNKNOWN_CONGESTION_LEVEL** |
| **RUNNING_SMOOTHLY** |
| **STOP_AND_GO** |
| **CONGESTION** |
| **SEVERE_CONGESTION** |

## _enum OccupancyStatus_

O grau de lotação de passageiros para o veículo.

**Atenção**: este campo ainda é **experimental** e está sujeito a alterações. Poderá ser formalmente adotado no futuro.

#### _Valores_

| _**Valor**_ | _**Comentário**_ |
|-------------|---------------|
| _**EMPTY**_ | _O veículo é considerado vazio, e tem poucos ou nenhum passageiro a bordo, ainda aceita passageiros._ |
| _**MANY_SEATS_AVAILABLE**_ | _O veículo tem um grande percentual de assentos disponíveis. A percentagem de assentos livres deve ser considerada grande o suficiente para se enquadrar nesta categoria e é determinada a critério do produtor de conteúdo._ |
| _**FEW_SEATS_AVAILABLE**_ | _O veículo tem um pequeno percentual de assentos disponíveis. A porcentagem de assentos livres disponíveis deve ser considerada pequena o suficiente para se enquadrar nesta categoria e é determinada a critério do produtor de conteúdo._ |
| _**STANDING_ROOM_ONLY**_ | _O veículo atualmente pode acomodar apenas passageiros em pé._ |
| _**CRUSHED_STANDING_ROOM_ONLY**_ | _O veículo atualmente pode acomodar apenas passageiros em pé e num espaço limitado para eles._ |
| _**FULL**_ | _O veículo é considerado lotado, mas ainda pode permitir que passageiros embarquem._ |
| _**NOT_ACCEPTING_PASSENGERS**_ | _O veículo não pode aceitar passageiros._ |

## _message_ Alert

Um alerta, indicando algum tipo de incidente na rede de transporte público.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **active_period** | [TimeRange](#message-timerange) | Opcional | Vários | Hora em que o alerta deve ser exibido ao usuário. Se não for definido, o alerta será exibido enquanto aparecer no feed. Se vários intervalos forem fornecidos, o alerta será exibido durante todos eles. |
| **informed_entity** | [EntitySelector](#message-entityselector) | Obrigatório | Vários | Entidades cujos usuários devem receber este alerta específico. Pelo menos uma informed_entity deve ser fornecida. |
| **cause** | [Cause](#enum-cause) | Opcional | Um |
| **effect** | [Effect](#enum-effect) | Opcional | Um |
| **url** | [TranslatedString](#message-translatedstring) | Opcional | Um | URL que fornece informações adicionais sobre o alerta. |
| **header_text** | [TranslatedString](#message-translatedstring) | Obrigatório | Um | Cabeçalho do alerta. Este intervalo de texto simples será destacado, por exemplo, em negrito. |
| **description_text** | [TranslatedString](#message-translatedstring) | Obrigatório | Um | Descrição do alerta. Este linha de texto simples será formatado como o corpo do alerta (ou exibido ao usuário usuário após uma solicitação explícita de "ver mais"). As informações na descrição devem ser complementares ao cabeçalho. |
| **tts_header_text** | [TranslatedString](#message-translatedstring) | Opcional | Um | Texto que contém o cabeçalho a ser usado para implementações de conversão de texto-para-fala. Este campo é a versão texto-para-fala do header_text. Deve conter as mesmas informações do header_text, formatado de tal forma que possa ser lido como texto-para-fala (por exemplo, abreviações removidas, números soletrados etc.) **Cuidado**: esse campo ainda é **experimental** e está sujeito a alterações. Poderá ser formalmente adotado no futuro. |
| **tts_description_text** | [TranslatedString](#message-translatedstring) | Opcional | Um | Texto que contém uma descrição para o alerta a ser usado em implementações de conversão de texto-para-fala. Este campo é a versão texto-para-fala de description_text. Deve conter as mesmas informações que description_text, formatadas de modo que possam ser lidas como texto-para-fala (por exemplo, abreviações removidas, números soletrados, etc.) **Cuidado:** esse campo ainda é **experimental** e sujeito a alterações. Poderá ser formalmente adotado no futuro. |
| **severity_level** | [SeverityLevel](#enum-severitylevel) | Opcional | Um | Gravidade do alerta. <br>**Atenção**: este campo ainda é **experimental** e está sujeito a alterações. Poderá ser formalmente adotado no futuro. |

## _enum_ Cause

Causa do alerta.

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

O efeito desse problema na entidade afetada.

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

A gravidade do alerta

**Atenção:** este campo ainda é **experimental** e está sujeito a alterações. Poderá ser formalmente adotado no futuro.

#### Valores
| _**Valor**_ |
|-------------|
| **UNKNOWN_SEVERITY** |
| **INFO** |
| **WARNING** |
| **SEVERE** |

## _message_ TimeRange

Um intervalo de tempo. O intervalo é considerado ativo no tempo `t` se `t` for maior ou igual ao horário de início e menor que o horário de término.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **start** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | Hora de início, de acordo com a padronização POSIX (contagem em segundos desde 1º de janeiro de 1970 às 00:00:00 UTC). Se ausente, o intervalo começa em infinito negativo. Se um TimeRange é fornecido, start ou end devem ser fornecidos - não podem estar ambos vazios. |
| **end** | [uint64](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | Hora do término, de acordo com a padronização POSIX (contagem em segundos desde 1º de janeiro de 1970 às 00:00:00 UTC). Se ausente, o intervalo começa em infinito positivo. Se um TimeRange é fornecido, start ou end devem ser fornecidos - não podem estar ambos vazios. |

## _message_ Position

Geolocalização do veículo.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **latitude** | [float](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obrigatório | Um | Graus ao Norte, no sistema de coordenadas WGS-84. |
| **longitude** | [float](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obrigatório | Um | Graus a Leste, no sistema de coordenadas WGS-84. |
| **bearing** | [float](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Orientação, em graus, no sentido horário a partir do norte verdadeiro, ou seja, 0 é norte e 90 é leste. Esta pode ser a orientação da bússola, a direção para a próxima parada ou um local intermediário. Não deve ser deduzido a partir da sequência de posições anteriores, que os usuários podem computar a partir dos dados prévios. |
| **odometer** | [double](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Valor do odômetro, em metros. |
| **speed** | [float](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Velocidade momentânea medida pelo veículo, em metros por segundo. |

## _message_ TripDescriptor

Uma descrição que identifica uma única instância para um trajeto GTFS.

Para especificar um único trajeto em vigor, em muitos casos, uma `trip_id` por si só é suficiente. No entanto, os casos a seguir requerem informações adicionais para definir uma determinada instância específica de um trajeto:
* Par trajetos definidos em frequencies.txt, `start_date` e `start_time` são obrigatórios, além do `trip_id`
* Se um trajeto dura mais de 24 horas, ou se atrasar de tal forma que colidiria com uma trajeto programado no dia seguinte, então `start_date` será obrigatório, juntamente com `trip_id`
* Se o `trip_id` não puder ser fornecido, então `route_id`, `direction_id`, `start_date`, e `start_time` devem todos ser fornecidos.

Em todos os casos, se `route_id` for fornecido junto com `trip_id`, então, `route_id` deve ser o mesmo `route_id` atribuído ao trajeto especificado no arquivo trips.txt do GTFS.

O campo `trip_id` não pode, sozinho ou em combinação com outros campos do TripDescriptor, ser usado para identificar múltiplas ocorrências de trajetos. Por exemplo, um TripDescriptor nunca deve especificar trip_id por si só no frequencies.txt exact_times=0 do GTFS, porque start_time também é necessário para definir uma única instância do trajeto que inicia em uma hora específica do dia. Se o TripDescriptor não determina um trajeto (ou seja, abrange zero ou várias instâncias de trajeto), será considerado um erro e a entidade que contém o TripDescriptor errôneo poderá ser descartada pelos consumidores de dados.

Observe que, se o trip_id for desconhecido, os IDs das estações seguintes no TripUpdate não serão suficientes e os stop_ids também deverão ser fornecidos. Além disso, valores absolutos para os horários de chegada/partida devem ser fornecidos.

TripDescriptor.route_id não pode ser usado em um Alert EntitySelector para especificar um alerta para um rota inteira, o que afetaria todas as viagens desta - neste caso use EntitySelector.route_id.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **trip_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O trip_id do feed GTFS ao qual esse seletor se refere. Para trajetos não baseados em frequência (trajetos não definidos no arquivo GTFS frequencies.txt), esse campo basta para identificar exclusivamente um trajeto. Para trajetos baseados em frequência definidos em frequencies.txt, trip_id, start_time e start_date são obrigatórios. Para trajetos programados (definidos no arquivo GTFS frequencies.txt), trip_id só pode ser omitido se o trajeto puder ser identificado exclusivamente por uma combinação de route_id, direction_id, start_time e start_date, e todos esses campos forem fornecidos. |
| **route_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O route_id do GTFS ao qual este seletor se refere. Se trip_id for omitido, route_id, direction_id, start_time e schedule_relationship=SCHEDULED devem todos ser definidos para identificar uma instância do trajeto. TripDescriptor.route_id não deve ser usado em um Alert EntitySelector para definir um alerta que afeta todos os trajetos para uma rota  - neste caso, use EntitySelector.route_id. |
| **direction_id** | [uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O direction_id do arquivo trips.txt do feed GTFS, indicando a direção da viagem para trajetos aos quais este seletor se refere. Se trip_id for omitido, direction_id deve ser fornecido. <br>**Atenção**: este campo ainda é **experimental** e está sujeito a alterações. Poderá ser formalmente adotado no futuro.<br>|
| **start_time** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | A hora de início programada para este trajeto em especial. Quando o trip_id corresponde a uma viagem não baseada em frequência, esse campo deve ser omitido ou igual ao valor no feed GTFS. Quando o trip_id corresponde a um trajeto baseado em frequência definida no arquivo GTFS frequencies.txt, start_time é necessário e deve ser definido para atualizações de trajeto e posição do veículo. Se a viagem corresponder a exact_times=1  no GTFS, então start_time deverá ter múltiplos valores (incluindo zero) de headway_secs maiores que frequencies.txt start_time para o período correspondente. Se o trajeto corresponder a exact_times=0, então o seu start_time pode ser arbitrário, e inicialmente é esperado que seja a primeira partida para este trajeto. Uma vez estabelecido o start_time desse trajeto, baseado em frequência, como exact_times=0, então será considerado imutável, mesmo que o primeiro horário de partida seja alterado - essa mudança de horário pode ser refletida em um StopTimeUpdate. Se trip_id for omitido, start_time deve ser fornecido. A formatação e semântica do campo é a mesma usada para GTFS/frequencies.txt/start_time, p.ex., 11:15:35 ou 25:15:35. |
| **start_date** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | A data de início deste trajeto em especial no formato AAAAMMDD. Para trajetos programados (não definidas em frequencies.txt do GTFS), esse campo deve ser fornecido para desambiguar trajetos atrasados prestes a colidir com uma trajeto programado para o dia seguinte. Por exemplo, para um trem que parte às 8:00 e 20:00 todos os dias e está 12 horas atrasado, haveriam, em tese, dois trajetos distintos ocorrendo ao mesmo tempo. Esse campo pode ser fornecido, mas não é obrigatório para cronogramas nos quais tais colisões são impossíveis - por exemplo, um serviço ativo de hora em hora no cronograma,  onde um veículo que está uma hora atrasado não é mais considerado ativo no cronograma. Este campo é obrigatório para trajetos baseados em frequência definidas no arquivo GTFS frequencies.txt. Se trip_id for omitido, start_date deve ser fornecido. |
| **schedule_relationship** | [ScheduleRelationship](#enum-schedulerelationship-1) | Opcional | Um | A relação entre este trajeto e o cronograma estático. Se o TripDescriptor for fornecido em um Alert `EntitySelector`, o campo `schedule_relationship` será ignorado pelos consumidores de dados ao identificarem o trajeto correspondente.

## _enum_ ScheduleRelationship

A relação entre este trajeto e o cronograma estático. Se um trajeto é feito de acordo com a programação temporária, sem reflexos no GTFS, ele não deve ser marcado como SCHEDULED, mas sim como ADDED.

#### Valores

| _**Valor**_ | _**Comentário**_ |
|-------------|---------------|
| **SCHEDULED** | O trajeto que está ocorrendo de acordo com sua programação GTFS, ou está próximo o suficiente do trajeto programado para ser associada à ele. |
| **ADDED** | Um trajeto extra que foi adicionado a um cronograma ativo, por exemplo, para substituir um veículo quebrado ou para atender a uma demanda súbita de passageiros. |
| **UNSCHEDULED** | Um trajeto que está sendo executado sem programação associada a ele - esse valor é usado para identificar os trajetos definidos em frequencies.txt do GTFS com exact_times=0. Não deve ser usado para descrever trajetos não definidos ou trajetos com exact_times=1. |
| **CANCELED** | Um trajeto que existia no cronograma, mas foi removido. |

## _message_ VehicleDescriptor

Informações de identificação do veículo que realiza o tajeto.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Sistema de identificação interna do veículo. Deve ser **exclusivo** por veículo e é usado para rastreá-lo conforme se move pelo sistema. Esse id não deve ficar visível para o usuário final; para esse fim, use o campo **label**. |
| **label** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Marcação identificadora do veículo, ou seja, algo que deve ser exibido ao passageiro para ajudar a identificar o veículo correto. |
| **license_plate** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Opcional | Um | Placa do veículo. |

## _message_ EntitySelector

Um seletor para uma entidade que a identifique no feed GTFS. Todos os campos de valor devem estar devidamente sincronizados com o informado no GTFS. Pelo menos um dado verificador deve ser fornecido. Se vários dados são fornecidos, eles devem ser interpretados como sendo agrupados pelo operador lógico `AND`. Além disso, a a combinação de especificações deve corresponder às informações já previamente fornecidas no feed GTFS. Em outras palavras, para que um alerta seja aplicado a uma entidade no GTFS, ele deve confirmar todos os campos EntitySelector fornecidos. Por exemplo, um EntitySelector que contém os campos `route_id:"5"` e `route_type"3"` se aplica somente ao veículo `route_id:"5"` - não se aplica a nenhuma outra rota `route_type:"3"`. Se um produtor quer aplicar um alerta para `route_id:"5"`, e para `route_type:"3"`, ele deve fornecer dois EntitySelectors, um para `route_id:"5"`e outro para `route_type:"3"`.

Pelo menos um dado de verificação deve ser fornecido - não se pode deixar todos os campos em EntitySelector vazios.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **agency_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O agency_id do feed GTFS ao qual esse seletor se refere.
| **route_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O route_id do GTFS ao qual este seletor se refere. Se direction_id for fornecido, route_id também deve ser.
| **route_type** | [int32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O route_type do GTFS ao qual este seletor se refere.
| **direction_id** | [uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O direction_id do arquivo trips.txt no feed GTFS, usado para selecionar todos os trajetos em direção única para uma rota, especificados por route_id. Se direction_id for fornecido, route_id também deve ser.<br>**Atenção:** este campo ainda é **experimental** e está sujeito a alterações. Poderá ser formalmente adotado no futuro.<br>|
| **trip** | [TripDescriptor](#message-tripdescriptor) | Facultativo | Um | O trajeto iminente do GTFS ao qual este seletor se refere. Esse TripDescriptor deve determinar somente um único trajeto nos dados GTFS (por exemplo, um produtor não pode fornecer apenas um trip_id para trajetos com exact_times=0). Se o campo ScheduleRelationship for preenchido neste TripDescriptor, será ignorado pelos consumidores de dados ao tentar identificar o trajeto GTFS.
| **stop_id** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | O stop_id do feed GTFS ao qual esse seletor se refere.

## _message_ TranslatedString

Uma mensagem universal contendo versões por idioma de um fragmento de texto ou uma URL. Uma das strings de uma mensagem será escolhida. A resolução procede da seguinte forma: Se o idioma da interface do usuário for igual ao código de idioma da tradução, a primeira tradução combinada será selecionada. Se o idioma padrão da interface do usuário (por exemplo, português) corresponder ao código do idioma de uma tradução, a primeira tradução correspondente será selecionada. Se alguma tradução não tiver um código de idioma definido, essa tradução será a selecionada.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **translation** | [Translation](#message-translation) | Obrigatório | Vários | Pelo menos uma tradução deve ser fornecida. |

## _message_ Translation

Uma string traduzida designada para um idioma.

#### Campos

| _**Nome do Campo**_ | _**Tipo**_ | _**Requisito**_ | _**Cardinalidade**_ | _**Descrição**_ |
|------------------|------------|----------------|-------------------|-------------------|
| **text** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Obrigatório | Um | Uma linha de caracteres UTF-8 contendo a mensagem. |
| **language** | [string](https://developers.google.com/protocol-buffers/docs/proto#scalar) | Facultativo | Um | Código de idioma BCP-47. Pode ser omitido se o idioma for desconhecido ou se nenhuma internacionalização for feita para o feed. Apenas uma tradução, no máximo, pode conter uma marcação de idioma como desconhecida - se houver mais de uma tradução, o idioma deve ser fornecido. |
