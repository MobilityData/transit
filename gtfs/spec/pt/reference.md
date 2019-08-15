## Referência para Especificação Geral do Feed de Transporte Público

**Revisado em 17 de Janeiro de 2019. Ver [Histórico de Revisão] (../../CHANGES.md) para mais detalhes.**

Este documento define o formato e estruura dos arquivos que constituem um conjunto de dados GTFS.

## Tabela de Conteúdos

1.  [Definição dos Termos](#term-definitions)
2.  [Tipos de Campos](#field-types)
3.  [Arquivos do Conjunto de Dados](#dataset-files)
4.  [Requisitos do Arquivo](#file-requirements)
5.  [Definições dos Campos](#field-definitions)
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

## Definições dos Termos

Esta seção define os termos que serão usados ao longo deste documento.


* **Conjunto de dados** - Um conjunto completo de arquivos definidos por este documento de referência. A alteração deste conjunto cria uma nova versão do mesmo. Os conjuntos de dados devem ser publicados em uma URL permanente e pública, incluindo o nome do arquivo zip. (por exemplo, https://www.agency.org/gtfs/gtfs.zip).
* **Registro** - Uma estrutura de dados básica composta de vários campos de diferentes valores que descrevem uma única entidade (por exemplo, agência de trânsito, parada, rota, etc.). Representado como uma linha numa tabela.
* **Campo** - Uma propriedade de um objeto ou entidade. Representado, numa tabela, como coluna.
* **Valor do Campo** - Uma inserção individual em um campo. Representada, em uma tabela, como uma única célula.
* **Obrigatório** - O campo deve ser obrigatoriamente incluído no conjunto de dados e com um valor devidamente fornecido para cada registro. Alguns campos obrigatórios permitem um intervalo vazio como valor (indicado nesta especificação como vazio). Para inserir um intervalo vazio, basta omitir qualquer valor entre as vírgulas para este campo.
* **Opcional** - O campo pode ser omitido do conjunto de dados. Se uma coluna opcional estiver incluída, algumas das entradas neste campo podem ser intervalos vazios. Para inserir um intervalo vazio, basta omitir qualquer valor entre as vírgulas para este campo. Observe que um campo omitido é equivalente a um campo totalmente vazio. 
* **Facultativo** - O campo, ou arquivo, serão obrigatórios sob certas condições descritas no campo ou na descrição do arquivo. Exceto nesses casos, esse campo ou arquivo é opcional.
* **Expediente** - O expediente é um período usado para indicar o agendamento de rotas. A definição exata do expediente varia de agência para agência, mas dias de expediente geralmente não correspondem aos dias de calendário. Um expediente pode exceder as 24:00:00 se o serviço começar em um dia e terminar no seguinte. Por exemplo, o horário de funcionamento das 08:00:00 de sexta até 02:00:00 de sábado pode ser considerado como um único expediente das 08:00:00 às 26:00:00.

## Tipos de Campos

- **Cor** - A cor deve ser informada em um código numérico hexadecimal. Consulte [https://html-color-codes.info/Codigos-de-Cores-HTML/](https://html-color-codes.info/Codigos-de-Cores-HTML/) para gerar um código válido (o "#" não é utilizado). <br> *Exemplo: `FFFFFF` para branco, `000000` para preto ou `0039A6` para as linhas A,C,E de um serviço.*
- **Código da moeda** - Um código alfabético ISO 4217 de moeda. Para obter a lista atual de códigos monetário consulte [https://en.wikipedia.org/wiki/ISO_4217#Active\_codes](https://en.wikipedia.org/wiki/ISO_4217#Active_codes) (em inglês).<br> *Exemplo: `CAD` para dólares canadenses, `EUR` para euros ou `JPY` para ienes.*
- **Data** - Dia de expediente no formato AAAAMMDD. Como o tempo dentro de um expediente pode ser superior a 24:00:00, um dia de expediente geralmente contém informações para o(s) dia(s) seguinte(s).<br> *Exemplo: `20180913` para 13 de setembro de 2018.*
- **E-mail** - Um endereço de e-mail. <br> *Exemplo: `examplo@examplo.com`*
- **Enum** - Uma opção de um conjunto de constantes predefinidas na coluna "Descrição". <br> *Exemplo: O campo `route_type` consta` 0` para bonde,`1` para metrô ...*
- **ID** - Uma sequência de caracteres UTF-8 utilizada unicamente para identificar uma entidade, não necessariamente um registro específico numa tabela. IDs definidos em um arquivo .txt geralmente são utilizados como referência para outro arquivo .txt. Um valor no campo ID não é exibido para o usuário, e trata-se de uma sequência qualquer de caracteres UTF-8, porém recomenda-se utilizar somente caracteres ASCII publicáveis. <br> *Exemplo: O campo `stop_id` em [stops.txt](#stops.txt) é um ID. O campo `stop_id` em [stop_times.txt](#stop_times.txt) é um ID vinculando `stops.stop_id`.*
- **Código do idioma** - Um código de idioma IETF BCP 47. Para uma introdução ao IETF BCP 47, consulte [http://www.rfc-editor.org/rfc/bcp/bcp47.txt](http://www.rfc-editor.org/rfc/bcp/bcp47.txt) e [http://www.w3.org/International/articles/language-tags/](http://www.w3.org/International/articles/language-tags/) (ambos em inglês). <br> *Exemplo: `en` para inglês,` en-US` para inglês estadunidense ou `de` para alemão.*
- **Latitude** - Latitude WGS84 em graus decimais. O valor deve ser maior ou igual a -90.0 e menor ou igual a 90.0. *<br> Exemplo: `41.890169` para o Coliseu de Roma*
- **Longitude** - Longitude WGS84 em graus decimais. O valor deve ser maior ou igual a -180.0 e menor ou igual a 180.0. <br> *Exemplo: `12.492269` para o Coliseu de Roma.*
- **Flutuante Não Negativo** - Um ponto flutuante maior ou igual a 0.
- **Inteiro não negativo** - Um número inteiro maior ou iguala 0.
- **Telefone** - Um número telefônico.
- **Horário** - Horário no formato HH:MM:SS (H:MM:SS também é aceito). A contagem do tempo é feita a partir do "meio-dia menos 12 horas" do expediente em questão (de fato meia-noite, exceto nos dias em que ocorrem alterações no horário de verão). Para ocorrência de horários pós meia-noite, insira a hora como valor maior que 24:00:00 em HH:MM:SS hora local do dia em que a viagem está programada para iniciar. <br> *Exemplo: `14:30:00` para 2:30 PM ou` 25:35:00` para 1:35 AM do dia seguinte.*
- **Texto** - Uma linha de caracteres UTF-8, destinada a ser exibida e, portanto, deve ser humanamente legível.
- **Fuso horário** -  Fuso horário FH [https://www.iana.org/time-zones](https://www.iana.org/time-zones) (em inglês). A nomenclatura de fuso horário não contêm o caractere de espaço, mas pode conter um sublinhado. Consulte [http://en.wikipedia.org/wiki/List\_of\_tz\_zones](http://en.wikipedia.org/wiki/List\_of\_tz\_zones) (em inglês) para obter uma lista de valores válidos. <br>*Exemplo: `Asia/Tokyo`, `America/Los_Angeles` ou `Africa/Cairo`. *
- **URL** -  Um URL completo qualificado que inclui http:// ou https://; quaisquer caracteres especiais no URL devem ser corretamente evitados. Consulte [http://www.w3.org/Addressing/URL/4\_URI\_Recommentations.html](http://www.w3.org/Addressing/URL/4\_URI\_Recommentations.html) (em inglês) para uma descrição de como criar valores qualificados de URL.

## Arquivos do conjunto de dados

Esta especificação define os seguintes arquivos:

|  Nome do arquivo | Requisito | Definições |
|  ------ | ------ | ------ |
|  [agency.txt](#agencytxt) | **Obrigatório** | Agências de trânsito cujo serviço está representado neste conjunto de dados. |
|  [stops.txt](#stopstxt) | **Obrigatório** | Paradas onde veículos coletam ou desembarcam passageiros. Também define estações e entradas de estações.  |
|  [routes.txt](#routestxt) | **Obrigatório** | Rotas de trânsito. Uma rota é um grupo de trajetos que são exibidos aos passageiros como um único serviço. |
|  [trips.txt](#tripstxt)  | **Obrigatório** | Trajetos para cada rota. Um trajeto é uma sequencia de duas ou mais paradas que ocorrem durante um período de tempo específico. |
|  [stop_times.txt](#stop_timestxt)  | **Obrigatório** | Tempo que um veículo gasta nas paradas de cada trajeto. |
|  [calendar.txt](#calendartxt)  | **Facultativo** | Datas de expediente do serviço especificadas por calendario semanal com datas de início e término. Este arquivo é obrigatório exceto se todos os dias de serviço estiverem definidos em [calendar_dates.txt](#calendar_datestxt). |
|  [calendar_dates.txt](#calendar_datestxt)  | **Facultativo** | Exceções para os serviços definidos no [calendar.txt](#calendartxt). Se [calendar.txt](#calendartxt) for omitido, [calendar_dates.txt](#calendar_dates.txt) será obrigatório e deverá conter todas as datas de expediente. |
|  [fare_attributes.txt](#fare_attributestxt)  | Opcional | Informações tarifárias por rota aplicadas pela agência de trânsito. |
|  [fare_rules.txt](#fare_rulestxt)  | Opcional | Regras para aplicação de tarifas nos itinerários. |
|  [shapes.txt](#shapestxt)  | Opcional | Regras para mapeamento de trajeto dos veículos, às vezes chamado de alinhamento de rotas. |
|  [frequencies.txt](#frequenciestxt)  | Opcional | Intervalo (tempo entre trajetos) para serviços com frequencia variável ou uma representação compacta de serviços com horário fixo. |
|  [transfers.txt](#transferstxt)  | Opcional | Regras para fazer conexões em pontos de reembarque entre rotas. |
|  [pathways.txt](#pathwaystxt)  | Opcional | Caminhos conectando locais dentro de estações. |
|  [levels.txt](#levelstxt)  | Opcional | Andares dentro das estações. |
|  [feed_info.txt](#feed_infotxt)  | Opcional | Informações gerais sobre o conjunto de dados, incluindo responsável pela publicação, versão e prazo de validade da informação. |

## Requisitos dos Arquivos

 Os seguintes requisitos se aplicam ao formato e conteúdo dos arquivos do conjunto de dados:

* Todos os arquivos devem ser salvos como texto separado por vírgulas.
* A primeira linha de cada arquivo deve conter os nomes dos campos. Cada subseção da seção [Definições dos Campos](#field-definitions) corresponde a um dos arquivos em um conjunto de dados GTFS, e lista os nomes dos campos que podem ser usados neste.
* Todos os nomes dos campos diferenciam maiúsculas e minusculas.
* Os valores dos campos não podem conter tabulações, quebras de linha ou novas linhas.
* Valores de campos que contenham aspas ou virgulas devem estar entre aspas. Além disso, cada aspa no no valor do campo deve ser precedida de outra. Isso condiz com a maneira como o Microsoft Excel gera arquivos delimitados por vírgula (CSV). Para mais informações sobre formatos de arquivo CSV, consulte [http://tools.ietf.org/html/rfc4180](http://tools.ietf.org/html/rfc4180) (em inglês).
O exemplo a seguir demonstra como um valor do campo apareceria em um arquivo delimitado por vírgula:
  * **Valor original do campo:** `Contém "aspas", vírgulas e texto`
  * **Valor do campo no arquivo CSV:** `"Contém ""aspas"", vírgulas e texto"`
* Os valores dos campos não podem conter tags HTML, comentários ou sequências de escapes.
* Remova quaisquer espaços extras entre campos ou nomes dos campos. Muitos analisadores sintáticos (parsers) consideram os espaços como parte do valor, o que pode causar erros.
* Cada linha deve terminar com um caractere de quebra de linha CRLF ou LF.
* Os arquivos devem ser codificados em UTF-8 para suportar todos os caracteres Unicode. Arquivos que incluam marca de ordem de byte (BOM) Unicode são aceitáveis. Veja [http://unicode.org/faq/utf_bom.html#BOM](http://unicode.org/faq/utf_bom.html#BOM) (em inglês) para mais informações sobre o caractere BOM e UTF-8.
* Todos os arquivos do conjunto de dados devem ser compactados em conjunto.

## Definições dos Campos

### agency.txt

Arquivo: **Obrigatório**

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `agency_id` | ID | **Facultativo** | Identifica uma empresa de transporte público, geralmente identificada como agência de trânsito. Observe que, em alguns casos, como quando uma única agência opera múltiplos serviços, as agências e empresas são distintas. Este documento usa o termo "agência" no lugar de "empresa". Um conjunto de dados pode conter dados de várias agências. Este campo é obrigatório quando o conjunto de dados contém informações de várias agências de transporte público, caso contrário, é opcional. |
|  `agency_name` | Texto | **Obrigatório** | Nome completo da agência de trânsito. |
|  `agency_url` | URL | **Obrigatório** | Site da agência de trânsito. |
|  `agency_timezone` | Fuso horário | **Obrigatório** | Fuso horário do local onde se encontra a agência de trânsito. Se o conjunto de dados englobar informações de múltiplas agências, todas devem conter o mesmo `agency_timezone`. |
|  `agency_lang` | Código do Idioma | Opcional | Idioma principal usado pela agência de trânsito. Este campo auxilia os usuários do GTFS a escolherem regras de capitalização (maiúsculas) e outras configurações específicas de idioma para o conjunto de dados. |
|  `agency_phone` | Número Telefônico | Opcional | Um número de telefone válido para a agência em questão. Este campo é uma sequência de caracteres que deve conter o número de contato no formato típico da área de serviço. Pode e deve conter caracteres que agrupem os dígitos ou números. Também são permitidos textos discáveis (como o exemplo da TriMet's, "503-238-RIDE"), mas o campo não pode conter nenhum outro texto descritivo. |
|  `agency_fare_url` | URL | Opcional | URL de uma página onde o passageiro pode comprar passagens, ou acessar outros serviços tarifários da agência online. |
|  `agency_email` | E-mail | Opcional | Endereço de e-mail ativo e monitorado pelo serviço de atenção ao consumidor. Este e-mail deve ser uma linha de contato direto entre consumidor e agência de trânsito. |

### stops.txt

Arquivo: **Obrigatório**

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `stop_id` | ID | **Obrigatório** | Identifica uma parada, estação, ou entrada de estação. O termo "entrada de estação" refere-se às entradas e saidas das estações. Paradas, estações, ou entradas de estações são coletivamente denominadas como "locais". Múltiplas rotas poderão utilizar a mesma parada. |
|  `stop_code` | Texto | Opcional | Texto curto ou um número que identifica a parada aos passageiros. Esses códigos costumam ser usados em sistemas de informações de transporte para celular ou impressos como sinalização para facilitar o acesso à informação pelos usuários em determinado local. O `stop_code` pode ser o mesmo que o` stop_id` se este for exibido ao público. Este campo deve ser deixado vazio para locais sem um código apresentado aos passageiros. |
|  `stop_name` | Texto | **Facultativo** | Nome do local. Use um nome que nativos e turistas entendam.<br><br>Quando o local for uma área de embarque (`location_type =4`), o `stop_name` deve conter o nome da área de embarque conforme exibido pela agência. Pode ser apenas uma letra (como em algumas estações ferroviárias europeias interurbanas), texto como "Área de Embarque para Cadeirantes" (metrô de Nova York) ou "Parte Frontal dos Trens Curtos" (RER de Paris).<br><br>Facultativo:<br>•**Obrigatório** para locais que são paradas (`location_type=0`), estações (`location_type =1`) ou entradas/saídas (`location_type=2`).<br>• Opcional para locais genéricos (`location_type=3`) ou áreas de embarque (`location_type=4`).|
|  `stop_desc` | Texto | Opcional | Descrição do local que fornece informações úteis e de qualidade. Não duplique simplesmente o nome do local. |
|  `stop_lat` | Latitude | **Opcional** | Latitude do local.<br><br>Facultativo:<br>• **Obrigatório** para paradas (`location_type0`), estações (`location_type=1`) ou entradas/saídas (`location_type =2`). <br>• Opcional para locais genéricos (`location_type = 3`) ou áreas de embarque (`location_type = 4`).|
|  `stop_lon` | Longitude | **Facultativo** | Longitude do local.<br><br>Facultativo: <br>• **Obrigatório** para paradas (`location_type0`), estações (`location_type=1`) ou entradas/saídas (`location_type =2`). <br>• Opcional para locais genéricos (`location_type = 3`) ou áreas de embarque (`location_type = 4`). | 
|  `zone_id` | ID | **Facultativo** | Identifica a zona tarifária para uma parada. Este campo é necessário para fornecer informações de tarifas usando [fare_rules.txt] (#fare_rulestxt), caso contrário, é opcional. Se este registro representa uma estação ou entrada de estação, o `zone_id` é ignorado. |
|  `stop_url` | URL | Opcional | URL de uma página da web sobre o local. Deve ser diferente dos valores dos campos `agency.agency_url` e `routes.route_url`. |
|  `location_type` | Enum | Opcional | Tipo da local:<br>• `0` (ou em branco): **Parada** (ou **Plataforma**). Local onde os passageiros embarcam ou desembarcam de um veículo de trânsito. É chamado de plataforma quando definido dentro de uma `parent_station`.<br>• `1`: **Estação**. Uma estrutura física ou área que contém uma ou mais plataformas.<br>• `2`: **Entrada/Saída**. Um local onde os passageiros podem entrar ou sair de uma estação para a rua. Se uma entrada/saída pertencer a várias estações, ela pode ser vinculada a ambas, mas o provedor de dados deve escolher uma delas como padrão.<br>• `3`: **Local Genérico**. Um local dentro de uma estação, não correspondendo a nenhum outro `location_type`, que pode ser usado para vincular caminhos definidos em pathways.txt.<br>• `4`: **Área de Embarque**. Um local específico em uma plataforma, onde os passageiros podem embarcar e/ou desembarcar.|
|  `parent_station` | ID vinculando `stops.stop_id` | **Facultativo** | Define a hierarquia entre os diferentes locais definidos em `stops.txt`. Ele contém o ID do local principal, como segue:<br>• **Parada/plataforma** (`location_type =0`): o campo `parent_station` contém o ID de uma estação.<br>• **Estação** (`location_type = 1`): este campo deve estar vazio.<br>• **Entrada/Saída** (`location_type=2`) ou **Local genérico** (`location_type =3`): o campo `parent_station` contém o ID de uma estação (`location_type=1`)<br>• **Área de embarque** (`location_type=4`): o campo `parent_station` contém o ID de uma plataforma.<br><br> Facultativo:<br>• **Obrigatório** para locais que são entradas (`location_type=2`), locais genéricos (`location_type=3`) ou áreas de embarque (`location_type=4`).<br>• Opcional para paradas/plataformas (`location_type=0`).<br>• Proibido para estações (`location_type=1`).|
|  `stop_timezone` | Fuso horário | Opcional | Fuso horário do local. Se o local possuir uma estação vinculada, ele herdará o fuso horário desta estação invés de aplicar o próprio. Estações e paradas sem estações vinculadas com `stop_timezone` vazio herdam o fuso horário especificado por `agency.agency_timezone`. Se os valores `stop_timezone` forem fornecidos, os tempos em [stop_times.txt](#stop_timetxt) devem ser inseridos como o tempo a partir da meia-noite no fuso horário especificado por `agency.agency_timezone`. Isso garante que os valores de tempo sempre aumentem ao longo de uma viagem, independentemente de quais fusos horários a viagem cruze. |
|  `wheelchair_boarding` | Enum | Opcional | Indica se embarques para cadeiras de rodas são possíveis no local. Opções válidas são: <br><br>Para paradas sem estação padrão:<br>`0` ou vazio - Nenhuma informação de acessibilidade para a parada.<br>`1` - Alguns veículos nesta parada possibilitam o embarque em cadeira de rodas.<br>`2` - O embarque na cadeira de rodas não é possível nesta parada. <br><br>Paradas secundárias: <br>`0` ou vazio - A parada herdará seu comportamento` wheelchair_boarding` da estação vinculada, se especificado.<br>`1` - Existe um acesso especial de fora da estação para a parada/plataforma específica.<br>`2` - Não existe acesso especial de fora da estação para a parada/plataforma específica.<br><br> Para entradas/saídas de estações: <br>`0` ou vazio - A entrada da estação herdará seu comportamento ` wheelchair_boarding` da estação vinculada, se especificado.<br>`1` - A entrada da estação é acessível à cadeiras de rodas.<br>`2` - Não há caminho acessível desde a entrada da estação até as paradas/plataformas. |
|  `level_id` | ID vinculando `levels.level_id` | Opcional | Andar do local. O mesmo andar pode ser usado por várias estações desvinculadas.|
|  `platform_code` | Texto | Opcional | Identificador de plataforma para uma parada em plataforma (uma parada pertencente a uma estação). Este deve ser apenas o identificador de plataforma (p. ex. , "G" ou n"3";). Palavras como "plataforma" ou "linha" (ou o equivalente no idioma da fonte) não devem ser incluídas. Isso permite que os consumidores do feed internacionalizem e localizem com mais facilidade o identificador de plataforma em outros idiomas. |  

### routes.txt

Arquivo: **Obrigatório**

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `route_id` | ID | **Obrigatório** | Identifica uma rota. |
|  `agency_id` | ID vinculando `agency.agency_id` | **Facultativo** | Agência para a rota especificada. Este campo é obrigatório quando o conjunto de dados fornece infos para rotas de mais de uma agência em [agency.txt](#agency), caso contrário, é opcional.  |
|  `route_short_name` | Texto | **Facultativo** | Nome abreviado de uma rota. Geralmente será um identificador curto e abstrato, como "32", "100X" ou "Verde", usado pelos passageiros para identificar uma rota, mas que não fornece nenhuma indicação de quais locais a rota atende. Tanto `route_short_name` quanto `route_long_name` devem ser especificados, se apropriado. |
|  `route_long_name` | Texto | **Facultativo** | Nome completo de uma rota. Esse nome é geralmente mais descritivo que o `route_short_name` e geralmente inclui destino ou parada da rota. Tanto `route_short_name` quanto `route_long_name` devem ser especificados, se apropriado. |
|  `route_desc` | Texto | Opcional | Descrição de uma rota que fornece informações úteis e de qualidade. Não simplesmente duplique o nome da rota. <hr> _Exemplo: Trens "A" operam entre a Rua "B" e Avenida "C" em todos os horários. Além disso, entre 6h e meia-noite, trens adicionais "A" operam entre a Rua "B" e Avenida "D" (trens normalmente alternam entre as Avenidas "C" e "D")._ |
|  `route_type` | Enum | **Obrigatório** | Indica o tipo de transporte usado em uma rota. Opções válidas são: <br><br>`0` - Bonde, tram, pequeno trem. Qualquer veículo leve sobre trilhos ou ruas em área metropolitana.<br>`1` - Metrô. Qualquer sistema ferroviário subterrâneo dentro de uma área metropolitana.<br>`2` - Trem. Usado para viagens ferroviárias interurbanas ou de longa distância.<br>`3` - Ônibus. Usado para rotas de ônibus de curta e longa distância.<br>`4` - Balsa. Usado para serviço de barco de curta e longa distância.<br>`5` - Bondinho. Usado para bondes ao nível da rua, onde o cabo passa debaixo do veículo.<br>`6` - Teleférico, Gôndolas Suspensas. Normalmente usado para teleféricos aéreos, onde a cabine é suspensa no cabo.<br>`7` - Funicular. Qualquer sistema ferroviário projetado para declives íngremes. |
|  `route_url` | URL | Opcional | Página web sobre a rota específica. Deve ser diferente do valor `agency.agency_url`. |
|  `route_color` | Cor | Opcional | Designa cor para uma rota que corresponde ao material exibido ao público. O padrão será branco (`FFFFFF`) quando omitido ou não informado. A diferença de cor entre `route_color` e `route_text_color` deve fornecer contraste suficiente quando visualizada em uma tela preta e branca. |
|  `route_text_color` | Cor | Opcional | Define a cor a ser usada no texto em contraste à cor da rota que ficará de fundo `route_color`. O padrão será preto (`000000`) quando omitido ou não informado. A diferença de cor entre `route_color` e `route_text_color` deve fornecer contraste suficiente quando visualizada em uma tela preta e branca. |
|  `route_sort_order` | Inteiro não negativo | Opcional | Ordena as rotas de maneira ideal para apresentação aos clientes. Rotas com valores menores em `route_sort_order` devem ser exibidas primeiro. |

### trips.txt

Arquivo: **Obrigatório**

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `route_id` | ID vinculando `routes.route_id` | **Obrigatório** | Identifica uma rota. |
|  `service_id` | ID vinculando `calendar.service_id` ou `calendar_dates.service_id` | **Obrigatório** | Identifica um conjunto de datas com disponibilidade de serviço para uma ou mais rotas. |
|  `trip_id` | ID | **Obrigatório** | Identifica um trajeto. |
|  `trip_headsign` | Texto | Opcional | Texto que aparece na sinalização identificando o destino do trajeto para os passageiros. Use este campo para distinguir entre diferentes padrões de serviço na mesma rota. Se o letreiro é alterado durante um trajeto, o `trip_headsign` pode ser sobreposto por valores especificados em `stop_times.stop_headsign`. |
|  `trip_short_name` | Texto | Opcional | Texto exibido ao público usado para identificar o trajeto aos passageiros, por exemplo, para identificar números de trens em trajetos ferroviárias. Se os passageiros não costumam confiar nas nomenclaturas, deixe este campo vazio. Um valor `trip_short_name`, se fornecido, deve identificar exclusivamente um trajeto dentro de um expediente; não deve ser usado para nomes de destinos ou designações limitadas/expressas. |
|  `direction_id` | Enum | Opcional | Indica a direção de viagem para um trajeto. Este campo não é usado no planejamento de rotas; ele fornece uma maneira de separar os trajetos por direção ao publicar as tabelas de horários. Opções válidas são: <br><br>`0` - Trajeto em direção única (p. ex. trajeto de ida).<br>`1` - Trajeto na direção oposta (p. ex. trajeto de retorno).<hr>*Exemplo: Os campos `trip_headsign` e `direction_id` podem ser usados juntos para atribuir um nome a um trajeto em cada direção, num conjunto de viagens. Um arquivo [trips.txt](#tripstxt) pode conter esses registros para uso em tabelas de horários:* <br>`trip_id,..., trip_headsign, direction_id` <br> `1234,...,Aeroporto,0`  <br> `1505,...,Centro,1` |
|  `block_id` | ID | Opcional | Identifica o bloco ao qual o trajeto pertence. Um bloco consiste em um único trajeto ou múltiplos trajetos sequenciais usando o mesmo veículo, definido por expedientes compartilhados e o `block_id`. Um `block_id` pode ter trajetos com diferentes expedientes, gerando blocos distintos. Veja o [exemplo abaixo](#examplo-blocos-e-expediente) |
|  `shape_id` | ID vinculando `shapes.shape_id` | Opcional | Identifica o formato geospacial que descreve o caminho percorrido pelo veículo durante um trajeto. |
|  `wheelchair_accessible` | Enum | Opcional | Indica acessibilidade para cadeirante. Opções válidas são:<br><br>`0` ou vazio - Nenhuma informação de acessibilidade para a viagem.<br>`1` - O veículo utilizado neste trajeto em particular pode acomodar pelo menos um passageiro cadeirante.<br>`2` - Nenhum passageiro cadeirante pode ser acomodado neste trajeto. |
|  `bikes_allowed` | Enum | Opcional | Indica se bicicletas são permitidas. Opções válidas são:<br><br>`0` ou vazio - Nenhuma informação sobre bicicletas no trajeto.<br>`1` - O veículo utilizado neste trajeto em particular pode acomodar pelo menos uma bicicleta.<br>`2` - Não são permitidas bicicletas neste trajeto. |

#### Exemplo: blocos e expediente

O exemplo abaixo é válido, com blocos distintos todos os dias da semana.

| route_id | trip_id | service_id                     | block_id | <span style="font-weight:normal">*(horário da primeira parada)*</span> | <span style="font-weight:normal">*(horário da ultima parada)*</span> |
|----------|---------|--------------------------------|----------|-----------------------------------------|-------------------------|
| vermelho      | viagem_1  | seg-ter-qua-qui-sex-sab-dom | ver_circular | 22:00:00                                | 22:55:00                |
| vermelho      | viagem_2  | sex-sab-dom                    | ver_circular | 23:00:00                                | 23:55:00                |
| vermelho      | viagem_3  | sex-sab                        | ver_circular | 24:00:00                                | 24:55:00                |
| vermelho      | viagem_4  | seg-ter-qua-qui             | ver_circular | 20:00:00                                | 20:50:00                |
| vermelho      | viagem_5  | seg-ter-qua-qui             | ver_circular | 21:00:00                                | 21:50:00                |

Observações sobre a tabela acima:

* Na madrugada de sexta-feira para sábado, um único veículo opera na  `viagem_1`, `viagem_2`, e `viagem_3` (10:00 PM até 12:55 AM). Note que a última viagem ocorre no sábado, entre meia-noite e 00:55, mas faz parte do "expediente" da sexta-feira, já que os horários são de 24:00:00 até 24:55:00.
* Na segunda, terça, quarta e quinta-feira, um único veículo opera a `viagem_1`, `viagem_4`, e `viagem_5` em um bloco das 8:00 PM às 10:55 PM.

### stop_times.txt

Arquivo: **obrigatório**

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `trip_id` | ID vinculando `trips.trip_id` | **Obrigatório** | Identifica uma viagem.  |
|  `arrival_time` | Hora | **Facultativo** | Horário de chegada em uma parada específica de determinado trajeto de uma rota. Se não houverem intervalos programados para uma parada, insira os mesmos valores para `arrival_time` e `departure_time`. Para horários após a meia-noite, insira-os como um valor maior que 24:00:00 no horário local HH:MM:SS para o dia em que a programação do trajeto inicia.<br/><br/>Paradas agendadas onde o veículo segue rigorosamente os horários de chegada e partida específicos são pontos temporais. Se não for o caso, use um valor vazio no intervalo do campo `arrival_time` ou forneça um horário interpolado. Além disso, indique que os horários interpolados são fornecidos pelo campo `timepoint`=`0`. Se os interpolados forem representados como `timepoint`=`0`, então os pontos de tempo terão que ser informados como `timepoint`=`1`. Forneça horários de chegada para todas as paradas que são pontos de tempo. Horários de chegada devem ser especificados para a primeira e a última parada num trajeto. |
|  `departure_time` | Hora | **Facultativo** | Horário de partida de determinado parada de uma viagem específica pertencente a um trajeto. Para horários após a meia-noite, insira-os como um valor maior que 24:00:00 no horário local HH:MM:SS para o dia em que a programação do trajeto inicia. Se não existirem horários distintos de chegada e partida em uma parada, insira o mesmo valor para `arrival_time` e `departure_time`. Veja a descrição de `arrival_time` para maiores detalhes sobre o uso correto dos pontos de tempo. |
|  `stop_id` | ID vinculando `stops.stop_id` | **Obrigatório** | Identifica uma parada atendida. Todas as paradas atendidas durante um trajeto devem possuir um registro em [stop_times.txt](#stop_timestxt). Esses locais devem estar definidos como paradas, não estações nem entradas de estações. Uma parada pode ser atentida várias vezes no mesmo trajeto, e múltiplos trajetos e rotas podem atender a mesma parada. |
|  `stop_sequence` | Inteiro não negativo | **Obrigatório** | Ordem das paradas em um trajeto específico. Esses números devem crescer no decorrer do trajeto.<hr>*Exemplo: A primeira parada pode ter o valor `stop_sequence`=`1`, a segunda parada `stop_sequence`=`23`, a terceira `stop_sequence`=`40`, e assim por diante.* |
|  `stop_headsign` | Texto | Opcional | Texto exibido no letreiro que identifica o destino do trajeto para os passageiros. Esse campo substitui o padrão `trips.trip_headsign` quando o letreiro muda entre as paradas. Caso um letreiro único seja exibido no trajeto inteiro, use apenas `trips.trip_headsign`. |
|  `pickup_type` | Enum | Opcional | Indica a forma de embarque. Opções válidas são:<br><br>`0` ou vazio - Embarque no horário regular.<br>`1` - Embarque indisponível.<br>`2` - Necessário ligar para agência e agendar embarque.<br>`3` - Necessário coordenar embarque com o motorista. |
|  `drop_off_type` | Enum | Opcional | Indica a forma de desembarque. As opções válidas são:<br><br> `0` ou vazio - Desembarque no horário regular.<br>`1` - Desembarque indisponível.<br>`2` - Necessário ligar para agência e agendar o desembarque.<br>`3` - Necessário coordenar o desembarque com o motorista. |
|  `shape_dist_traveled` | Flutuante não negativo | Opcional | Representa a distância real percorrida ao longo do mapa do trajeto, da primeira parada até a especificada neste registro. Este campo especifica a proporção do trajeto a demarcar no desenho, entre quaisquer duas paradas durante uma trajeto. As unidades devem ser as mesmas usadas em [shapes.txt](#shapestxt). Valores usados para `shape_dist_traveled` devem crescer ao decorrer da `stop_sequence`; e não podem ser aplicados para trajetos inversos ao longo de uma rota.<hr>*Exemplo: Se um ônibus viaja 5.25 km do início do trajeto até a parada, `shape_dist_traveled`=`5.25`.*|
|  `timepoint` | Enum | Opcional | Indica se os veículos estão cumprindo com rigor os horários de parada definidos, ou se estas contagens são aproximadas e/ou interpoladas. Permite ao gerador do GTFS prover horários de paradas interpolados, indicando que são aproximados. Valores válidos são:<br><br>`0` - Horários são considerados aproximados.<br>`1` ou vazio - Horários são considerados exatos. |


### calendar.txt

Arquivo: **Facultativo**

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ |------ |
|  `service_id` | ID | **Obrigatório** | Identifica exclusivamente um conjunto de datas em que o serviço estará disponível para uma ou mais rotas. Cada valor `service_id` pode aparecer no máximo uma vez no arquivo [calendar.txt](#calendartxt). |
|  `monday` | Enum | **Obrigatório** | Indica se a linha opera todas as segundas-feiras do intervalo definido nos campos `start_date` e `end_date`. Exceções devem informadas em  [calendar_dates.txt](#calendar_datestxt). Opções válidas são:<br><br>`1` - A linha opera todas as segundas-feiras deste intervalo. <br>`0` - A linha não opera nas segundas-feiras deste intervalo. |
|  `tuesday` | Enum | **Obrigatório** | Mesmas funcionalidades do `monday`, porém aplicadas às terças-feiras. |
|  `wednesday` | Enum | **Obrigatório** | Mesmas funcionalidades do `monday`, porém aplicadas às quartas-feiras.  |
|  `thursday` | Enum | **Obrigatório** | Mesmas funcionalidades do `monday`, porém aplicadas às quintas-feiras.  |
|  `friday` | Enum | **Obrigatório** | Mesmas funcionalidades do `monday`, porém aplicadas às sextas-feiras.  |
|  `saturday` | Enum | **Obrigatório** | Mesmas funcionalidades do `monday`, porém aplicadas aos sábados. |
|  `sunday` | Enum | **Obrigatório** | Mesmas funcionalidades do `monday`, porém aplicadas aos domingos. |
|  `start_date` | Data | **Obrigatório** | Data de início do expediente para o intervalo. |
|  `end_date` | Data | **Obrigatório** | Data de término do expediente para o intervalo. Esta data também é incluída no intervalo do serviço. |

### calendar_dates.txt

Arquivo: **Facultativo**

A tabela  [calendar_dates.txt](#calender_datestxt) pode ativar ou desativar expedientes por data. Ela pode ser usada de duas formas:

* Recomendada: Use [calendar_dates.txt](#calender_datestxt) juntamente com o [calendar.txt](#calendartxt) para definir as exceções aos expedientes padrões estabelecidos em [calendar.txt](#calendartxt). Esta é a configuração recomendada se o serviço sofre poucas alterações, apenas em dias específicos (como feriados, ou datas comemorativas) . Neste caso `calendar_dates.service_id` é um ID vinculando `calendar.service_id`.
* Alternativa: Omitir [calendar.txt](#calendartxt), e definir cada expediente individualmente em [calendar_dates.txt](#calenderdatestxt). Esta opção é recomendada para linhas que tenham expedientes excepcionais e considerável variação de horários. Neste caso o `service_id` é o ID referência.

|  Nome do campo | Tipo | **Requisito** | Descrição |
|  ------ | ------ | ------ | ------ |
|  `service_id` | ID vinculando `calendar.service_id` ou ID | **Obrigatório** | Identifica exceções de expedientes para rotas específicas. Cada par (`service_id`, `date`) só deve aparecer uma vez em  [calendar_dates.txt](#calender_datestxt) se [calendar.txt](#calendartxt) e [calendar_dates.txt](#calender_datestxt) estiverem sendo usados em conjunto. Se um valor `service_id` aparecer em ambos [calendar.txt](#calendartxt) e [calendar_dates.txt](#calender_datestxt), a informação contida em [calendar_dates.txt](#calenderdatestxt) modifica as informações do serviço em [calendar.txt](#calendartxt). |
|  `date` | Data | **Obrigatório** | Data em que ocorrem exceções nos expedientes. |
|  `exception_type` | Enum | **Obrigatório** | Indica se o serviço opera na data informada no campo respectivo. As opções válidas são: <br><br>`1` -Serviço disponível na data especificada.<br>`2` - Serviço indisponível na data especificada.<hr>*Exemplo: Supondo que uma linha possua conjuntos de viagens diferentes para datas regulares e comemorativas. Um `service_id` pode corresponder ao serviço regular e o outro `service_id` à programação de serviço para feriados. Para um feriado específico, pode-se adicionar um `service_id` ao arquivo [calendar_dates.txt](#calender_datestxt) e remover esta data do `service_id` regular.* |

### fare_attributes.txt

Arquivo: **Opcional**

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `fare_id` | ID | **Obrigatório** | Identifica a classe tarifária. |
|  `price` | Flutuante não negativo | **Obrigatório** | Preço da tarifa, na moeda informada em `currency_type`. |
|  `currency_type` | Código da Moeda | **Obrigatório** | Moeda aceita para o pagamento da tarifa. |
|  `payment_method` | Enum | **Obrigatório** | Indica quando a tarifa deve ser paga. As opções válidas são:<br><br>`0` - Tarifa paga no embarque.<br>`1` - Tarifa deve ser paga antes do embarque. |
|  `transfers` | Enum | **Obrigatório** | Indica quantos reembarques são permitidos para esta tarifa. Este campo pode ser deixado vazio, mas é uma exceção aos campos obrigatórios. Opções válidas são:<br><br>`0`- Não é permitido o reembarque nesta tarifa.<br> `1` - Passageiros podem reembarcar uma vez. <br>`2` - Passageiros podem reembarcar duas vezes.<br> vazio - Reembarques ilimitados.  |
|  `agency_id` | ID vinculando `agency.agency_id` | **Facultativo** | Identifica a agência responsável pela tarifa. Este campo é obrigatório para conjuntos de dados que possuam mais de uma agência definidas em [agency.txt](#agencytxt), caso contrário é opcional. |
|  `transfer_duration` | Inteiro não negativo | Opcional | Intervalo de tempo em segundos, em que o reembarque é permitido. Quando `transfers`=`0` este campo pode ser usado para indicar a a validade do bilhete ou pode ser deixado vazio. |

### fare_rules.txt

Arquivo:**Opcional**

A tabela [fare_rules.txt](#farerulestxt) mostra como a tarifação em [fare_attributes.txt](#fare_attributestxt) se aplica para um itinerário. A maioria das regras de tarifação se baseiam em combinações das alternativas:

* A tarifa depende das estações de origem e destino.
* A tarifa depende das zonas por onde passa o itinerário.
* A tarifa depende da rota que o itinerário segue.

Para exemplos de como estruturar a tarifação com [fare_rules.txt](#farerulestxt) e [fare_attributes.txt](#fareattributestxt), veja [https://code.google.com/p/googletransitdatafeed/wiki/FareExamples](https://code.google.com/p/googletransitdatafeed/wiki/FareExamples) (em inglês) no projeto wiki open source Google Transit Data Feed.

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `fare_id` | ID vinculando `fare_attributes.fare_id`  | **Obrigatório** | Identifica a classe tarifária. |
|  `route_id` | ID vinculando `routes.routes_id` | Opcional | Identifica uma rota associada à classe tarifária. Se várias rotas com a mesma tarifa se baseiam nos mesmos atributos, crie um registro em [fare_rules.txt](#fare_rules.txt) para cada uma.<hr>*Exemplo: Se a classificação de tarifa "b" é válida para as rotas "TSW" e "TSE", o arquivo [fare_rules.txt](#fare_rules.txt) deve conter os registos tarifários desta forma:* <br> `fare_id,route_id`<br>`b,TSW`<br>`b,TSE`|
|  `origin_id` | ID vinculando `stops.zone_id` | Opicional | Identifica a zona de origem. Se a classificação tarifária engloba vária zonas de origem, crie um registro em [fare_rules.txt](#fare_rules.txt) para cada `origin_id`.<hr>*Exemplo: Se a classificação "b" é válida pra viagens com origem tanto na zona "2" quanto na zona "8", o arquivo [fare_rules.txt](#fare_rules.txt) deve conter os seguintes registros para tarifação:* <br> `fare_id,...,origin_id` <br>`b,...,2`<br>`b,...,8` |
|  `destination_id` | ID vinculando `stops.zone_id` | Opcional | Identifica a zona de destino. Se a classificação tarifária engloba várias zonas de destino, crie um registro em  [fare_rules.txt](#fare_rules.txt) para cada `destination_id`. <hr>*Exemplo: Os campos `origin_id` e `destination_id` podem ser usados juntos para definir que a classificação tarifária "b" é válida para viagens entre as zonas 3 e 4, e entre 3 e 5; assim o [fare_rules.txt](#fare_rules.txt) deve conter os seguintes registros para a tarifação:* <br>`fare_id,...,origin_id,destination_id` <br>`b,...,3,4`<br> `b,...,3,5` |
|  `contains_id` | ID vinculando `stops.zone_id`  | Opcional | Identifica as zonas de cobertura para a tarifa aplicada; também é usado em sistemas para calcular o valor correto da tarifa.<hr>*Exemplo: Se a classificação "c"  da tarifa está associada com todas as viagens da rota GRT que passam pelas zonas 5, 6, e 7 o [fare_rules.txt](#fare_rules.txt) deve conter os seguintes registros:* <br> `fare_id,route_id,...,contains_id` <br> `c,GRT,...,5` <br>`c,GRT,...,6` <br>`c,GRT,...,7` <br>*Como todas as zonas `contains_id` precisam corresponder à tarifa aplicável, um itinerário que passa pelas zonas 5 e 6 mas não pela zona 7 não teria a classe tarifária "c". Para mais detalhes, consulte [https://code.google.com/p/googletransitdatafeed/wiki/FareExamples](https://code.google.com/p/googletransitdatafeed/wiki/FareExamples) (em inglês) no projeto wiki open source Google Transit Data Feed.* |

### shapes.txt

Arquivo: **Opcional**

Descreve o caminho físico que um veículo percorre como um conjunto de latitudes e longitudes coordenadas. O campo traça tais coordenadas para gerar o mapa do trajeto do veículo. As coordenadas não precisam coincidir com os locais das paradas.

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `shape_id` | ID | **Obrigatório** | Identifica um formato. |
|  `shape_pt_lat` | Latitude | **Obrigatório** | Latitude de um ponto no trajeto. Cada registro em [shapes.txt](#shapestxt) representa um ponto no trajeto para definir o formato. |
|  `shape_pt_lon` | Longitude | **Obrigatório** | Latitude de um ponto no trajeto. |
|  `shape_pt_sequence` | Inteiro não negativo | **Obrigatório** | Sequência na qual os pontos do trajeto se conctam para definir o formato. Os valores devem crescer ao decorrer da viagem, não obrigatoriamente de forma consecutiva.<hr>*Exemplo: Se o mapa "A_shp"  possuir três pontos nesta definição, o arquivo [shapes.txt](#shapestxt) deve conter os seguintes registros para definir o mapa do trajeto:*<br>`shape_id,shape_pt_lat,shape_pt_lon,shape_pt_sequence`<br> `A_shp,37.61956,-122.48161,0` <br>`A_shp,37.64430,-122.41070,6` <br>`A_shp,37.65863,-122.30839,11` |
|  `shape_dist_traveled` | Flutuante não negativo | Opcional | Distância total percorrida no mapa, calculada desde o início da viagem até ponto especificado neste registro. O valor deve crescer ao decorrer do `shape_pt_sequence` e não pode ser usado em um trajeto de retorno de uma rota. A unidade de medida deve ser consistente com a usada em  [stop_times.txt](#stop_timestxt).<hr>*Exemplo: Se um ônibus percorre os três pontos definidos no trajeto  A_shp acima descrito,  os valores `shape_dist_traveled` (aqui exibidos em quilômetros) devem parecer com estes:* <br>`shape_id,shape_pt_lat,shape_pt_lon,shape_pt_sequence,shape_dist_traveled` <br> `A_shp,37.61956,-122.48161,0,0`<br>`A_shp,37.64430,-122.41070,6,6.8310` <br> `A_shp,37.65863,-122.30839,11,15.8765` |

### frequencies.txt

Arquivo: **Opcional**

[Frequencies.txt](#frequenciestxt) representa trajetos que operam com intervalos regulares (tempo entre trajetos). Esse arquivo pode ser usado para dois tipos de serviço:

* Serviço recorrente (`exact_times`=`0`), quando não há uma programação de horários seguida ao decorrer do dia. Os operadores se esforçam para manter a regularidade dos intervalos predeterminados para os trajetos.
* Uma representação compacta dos serviços com regularidade programada (`exact_times`=`1`) que seguem o mesmo intervalo de tempo entre trajetos num período de tempo específico. Este serviço induz os operadores a cumprirem rigorosamente o cronograma agendado.


|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `trip_id` | ID vinculando `trips.trip_id` | **Obrigatório** | Identifica um trajeto ao qual o intervalo do serviço espcificado se aplica. |
|  `start_time` | Hora | **Obrigatório** | Hora em que o primeiro veículo parte da primeira parada do trajeto com intervalo especificado. |
|  `end_time` | Hora | **Obrigatório** | Hora em que o intervalo do serviço sofre alteração (ou se encerra) na primeira parada do trajeto. |
|  `headway_secs` | Inteiro não negativo | **Obrigatório** | Tempo, em segundos, entre partidas (intervalo) da mesma parada, definido pela diferença entre os valores `start_time` e `end_time`. Vários intervalos são permitidos para um mesmo trajeto, mas não devem se sobrepor. Novos intervalos podem iniciar no exato momento em que o anterior se encerra.  |
|  `exact_times` | Enum | Opcional | Indica o tipo de serviço para a viagem. Veja a descrição do arquivo para mais informações. Opções válidas são:<br><br>`0` ou vazio - Trajetos baseados na frequência em que ocorrem. <br>`1` - Trajetos baseados em cronograma com intervalos exatos durante o expediente. Neste caso, o valor `end_time` deve ser maior que `start_time` do último trajeto desejado, mas menor que o start_time do último trajeto + `headway_secs`.  |

### transfers.txt

Arquivo: **Opcional**

Ao calcular um itinerário, os aplicativos de GTFS baseiam as regras de reembarque considerando o tempo e a proximidade das paradas. O arquivo [Transfers.txt](#transferstxt) especifica regras adicinais e anula reembarques selecionados.

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `from_stop_id` | ID vinculando `stops.stop_id` | **Obrigatório** | Identifica a parada ou estação onde uma conexão entre rotas se inicia. Se referenciar uma estação, as regras de reembarque serão válidas para todas as paradas vinculadas à ela. |
|  `to_stop_id` | ID vinculando `stops.stop_id` | **Obrigatório** | Identifica uma parada ou estação onde se encerram as conexões entre rotas.  Se referenciar uma estação, as regras de reembarque serão válidas para todas as paradas vinculadas à ela. |
|  `transfer_type` | Enum | **Obrigatório** | Indica o tipo de conexão para os valores especificados (`from_stop_id`, `to_stop_id`). Opções válidas são:<br><br>`0` ou vazio - Ponto de reembarque recomendado.<br>`1` - Reembarque com intervalo entre rotas. O veiculo de partida aguarda o veículo de chegada para dar tempo aos passageiros reembarcarem.<br>`2` - Transferência requer um intervalo mínimo, definido em `min_transfer_time`, para que as conexões possam acontecer. <br>`3` - Reembarques não permitidos entre as rotas no local. |
|  `min_transfer_time` | Inteiro não negativo | Opcional | Quantidade de tempo, em segundos, que deve estar disponível para que o reembarque aconteça. O `min_transfer_time` deve ser suficiente para permitir que um  passageiro se desloque em velocidade média entre duas paradas, incluindo tempo extra de segurança para variações de cronograma em cada rota. |

### pathways.txt

Arquivo: **Opcional**

A extensão GTFS-Pathways usa uma representação gráfica do metrô ou trem, identificando pontos (os locais) e limites (os caminhos).

Para ir de uma entrada (marcação representando um local em location_type=2) até uma plataforma (marcação representando um local com location_type=0) o passageiro irá transitar por passarelas, escadas, catracas, etc. (devidamente representadas como caminhos). Também é possível adicionar "locais genéricos" para representar, por exemplo, um cruzamento de passarelas.

**Atenção: Os caminhos devem ser bem detalhados em uma estação.** Assim sendo, uma plataforma (como parada), entrada de estação ou local numa estação que tiver um caminho vinculado, deve possuir  uma descrição detalhada de seus caminhos. Portanto, as seguintes regras de senso comum se aplicam:
- Locais devem estar conectados: se qualquer local dentro de uma estação tiver uma caminho de acesso, então todos os demais locais também devem possuir (exceto para as plataformas com área de embarque).
- Plataformas não podem estar bloqueadas: cada plataforma deve estar conectada a pelo menos um acesso por meio de uma cadeia de caminhos. É raro que existam estações onde você não possa sair.
- Plataforma com áreas de embarque não deve possuir caminhos: uma plataforma com áreas de embarque é tratada como estação, não como um ponto. Ela não deve possuir vias associadas. Todos os caminhos devem direcionar para áreas de embarque.

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `pathway_id` | ID | **Obrigatório** | O campo `pathway_id` possui um ID que identifica um único caminho. O  `pathway_id`  é usado pelos sistemas como um identificador interno desse registro (por exemplo, chave primária no banco de dados) e, portanto, o` pathway_id` deve ser um conjunto de dados exclusivo. <br> Caminhos diferentes podem ir do mesmo `from_stop_id` para o mesmo `to_stop_id`. Por exemplo, isso acontece quando duas escadas rolantes estão lado a lado na direção oposta, ou quando uma escada está próxima de um elevador e ambos vão do mesmo lugar para o mesmo lugar.|
|  `from_stop_id` | ID vinculando ao `stops.stop_id` | **Obrigatório** | Local onde o caminho inicia. Contém um `stop_id` que identifica uma plataforma, entrada/saída, local genérico ou área de embarque do arquivo `stops.txt`.|
|  `to_stop_id` | ID vinculando `stops.stop_id` | **Obrigatório** | Local onde o caminho termina. Ele contém um `stop_id` que identifica uma plataforma, entrada/saída, local genérico ou área de embarque do arquivo` stops.txt`.|
|  `pathway_mode` | Enum | **Obrigatório** | Caminho especificado no intervalo (`from_stop_id`, `to_stop_id`). Valores válidos são: <br> • 1: passarela <br> • 2: escada <br> • 3: esteira rolante <br> • 4: escada rolante <br> • 5: elevador <br> • 6: guichê tarifário (catraca): um caminho onde há a necessidade de se comprovar a compra do bilhete (geralmente pago em algum guichê físico).<br>Guichês podem separar as áreas pagas das não pagas numa estação, ou separar diferentes áreas tarifárias dentro da mesma estação. Esta informação deve ser usada para evitar que os passageiros realizem pagamentos desnecessários ao usar atalhos, como atravessar uma plataforma de metrô para pegar um ônibus. <br> • 7: portão de saída: Indica um caminho livre de guichês para a saída.|
|  `is_bidirectional` | Enum | **Obrigatório** | Indica a direção do caminho:<br>• 0: unidirecional, apenas de `from_stop_id` para `to_stop_id`.<br>• 1: bidirecional, pode ser usado para ambos os sentidos.<br><br>guichês tarifários (`pathway_mode=6`) e portões de saída (`pathway_mode=7`) serão sempre em sentido único.|
| `length` | Flutuante não negativo | Opcional | Comprimento horizontal, em metros, do caminho desde o local de origem (definido em `from_stop_id`) até o local de destino (definido em`to_stop_id`).<br><br> Este campo é recomendado para passarelas (`pathway_mode=1`), guichês tarifários (`pathway_mode=6`)  e portões de saída (`pathway_mode=7`).|
| `traversal_time` | Inteiro Positivo | Opcional | Tempo médio, em segundos, necessário para ir da origem (definido em `from_stop_id`)  até o destino (definido em `to_stop_id`).<br><br> Este campo é recomendado para passageiros que usam esteiras rolantes (`pathway_mode=3`), escadas rolantes (`pathway_mode=4`) e elevadores (`pathway_mode=5`).|
| `stair_count` | Inteiro não nulo | Opcional | Quantidade de escadas no caminho.<br><br>Boas práticas: para gerar valoes aproxímados, podemos definir que 1 andar equivale a 15 degarus.<br><br> `stair_count` positivo indica que o passageiro deve subir do `from_stop_id` para`to_stop_id`. E `stair_count` negativo indica que o passageiro deve descer do `from_stop_id` para `to_stop_id`.<br><br>Este campo é recomendado para escadas (`pathway_mode=2`).|
| `max_slope` | Racional | Opcional | Relação de inclinação máxima da via. Valores válidos são:<br>• 0 or (vazio): sem inclinação.<br>• Variação: relação de inclinação do caminho, positivo para elevação, negativo para declive.<br><br>Esse campo deve ser usado somente para passarelas (`pathway_type=1`) e esteiras rolantes (`pathway_type=3`).<br><br> Exemplo: Nos EUA, 0,083 (ou 8,3%) é a relação máxima de inclinação para cadeira de rodas, o que significa um aumento de 0,083m (ou 8,3cm) para cada metro.|
| `min_width` | Racional Positivo | Opcional | Largura mínima do caminho em metros.<br><br>Este campo é altamente recomendado quando a largura for menor que 1 metro.|
| `signposted_as` | Texto | Opcional | Sinalização em texto visível para os passageiros. A sinalização pode ser usada para fornecer instruções aos usuários, como "siga a sinalização para". O idioma deve aparecer neste campo exatamente como é publicado nas sinalizações - não deve ser traduzido.|
| `reversed_signposted_as` | Texto | Opcional | Idem ao `signposted_as`, porém usado para o sentido oposto do caminho, de `to_stop_id` para `from_stop_id`.|

### levels.txt

Arquivo: **Opional**

Descreve os diferentes níveis de uma estação. É mais útil quando usado em conjunto com `pathways.txt`, e é obrigatório para elevadores (`pathway_mode=5`),  ao solicitar ao usuário que pegue o elevador até o nível "Mezanino" ou "Plataforma".

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `level_id` | ID | **Obrigatório** | Id do andar que pode ser vinculado de `stops.txt`.|
|  `level_index` | Racional | **Obrigatório** | Indica a posição deste andar em relação aos demais em um índice numérico (supomos que andares com índices mais elevados estejam localizados acima de andares com índices mais baixos).<br><br>O andar térreo deve ter índice 0 (zero), com níveis acima do solo indicados por índices positivos e níveis abaixo do solo por índices negativos.|
|  `level_name` | Texto | Opcional | Nome opcional do andar (correspondente ao letreiro/numeração usado dentro das estações). Útil para a rota do elevador (p. ex: “pegue o elevador até o “Mezanino” ou “Plataformas”  ou “-1”).|


### feed_info.txt

Arquivo: **Opcional**

O arquivo contém informações sobre o próprio conjunto de dados, ao invés dos serviços nele descritos. Note que, em alguns casos, o gerador do conjunto de dados pode ser diferente de qualquer uma das agências.

|  Nome do campo | Tipo | Requisito | Descrição |
|  ------ | ------ | ------ | ------ |
|  `feed_publisher_name` | Texto | **Obrigatório** | Nome completo da organização geradora do conjunto de dados. Pode ser o mesmo que um dos valores em `agency.agency_name`, |
|  `feed_publisher_url` | URL | **Obrigatório** | URL do site da organização geradora do conjunto de dados. Pode ser o mesmo que um dos valores em `agency.agency_url`. |
|  `feed_lang` | Código do Idioma | **Obrigatório** | Idioma padrão usado para o texto neste conjunto de dados. Essa configuração ajuda os consumidores do GTFS a escolher regras de capitalização (maiúsculas) e outras configurações específicas de idioma para o conjunto de dados. |
|  `feed_start_date` | Data | Opcional | O conjunto de dados fornece informações de programação completas e confiáveis para o serviço no período que inicia em `feed_start_date` e vai até `feed_end_date`. Ambas as datas podem ser deixadas em branco se indisponíveis. A data do `feed_end_date` não pode preceder `feed_start_date` quando ambos forem informados. Recomenda-se que os provedores do conjunto de dados forneçam também dados do cronograma fora desse período, a fim de informar sobre possíveis serviços futuros, mas os consumidores do conjunto de dados devem tratá-lo com atenção ao seu status mutável. Se `feed_start_date` ou `feed_end_date` se estenderem além das datas definidas em [calendar.txt](#calendartxt) e [calendar_dates.txt](#calendar_datestxt), o conjunto de dados está explicitamente declarando que não há expediente para datas contidas no intervalo entre `feed_start_date` e `feed_end_date`, exceto as inclusas nas datas ativas do calendário. |
|  `feed_end_date` | Data | Opcional | (Veja acima) |
|  `feed_version` | Texto | Opcional | Intervalo de texto que indica a versão atual do conjunto de dados do GTFS. Os aplicativos consumidores de GTFS podem exibir esse valor para ajudar os geradores de conjuntos de dados a determinar qual foi o mais recente conjunto incorporado. |
|  `feed_contact_email` | E-mail | Opcional | Endereço de e-mail para comunicação sobre o conjunto de dados GTFS e políticas de divulgação de dados. `feed_contact_email` é um suporte técnico para aplicativos consumidores de GTFS. Fornece informações de contato para atendimento ao cliente por meio do [agency.txt](#agencytxt). |
|  `feed_contact_url` | URL | Opcional | URL para informações de contato, formulário online, suporte técnico ou outras ferramentas de comunicação relacionadas às políticas de divulgação do conjunto de dados GTFS. `feed_contact_url`é um suporte técnico para aplicativos consumidores de GTFS. Fornece informações de contato para atendimento ao cliente por meio de [agency.txt](#agencytxt). |
