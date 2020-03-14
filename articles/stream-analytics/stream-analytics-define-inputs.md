---
title: Dados de Stream como entrada para o Azure Stream Analytics
description: Saiba mais sobre como configurar uma ligação de dados no Azure Stream Analytics. Entradas incluem um fluxo de dados de eventos e também os dados de referência.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254472"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Dados de Stream como entrada para o Stream Analytics

Stream Analytics tem integração de classe empresarial com fluxos de dados do Azure como entradas dentre três tipos de recursos:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estes recursos de entrada podem residir na mesma subscrição do Azure que a tarefa de Stream Analytics ou numa subscrição diferente.

### <a name="compression"></a>Compressão

Stream Analytics suporta a compressão em origens de entrada todos os dados stream. Os tipos de compressão suportado são: Nenhum, GZip e desinchar compressão. Suporte para compressão não está disponível para os dados de referência. Se o formato de entrada é dados Avro são compactados, ele é tratado de forma transparente. Não tem de especificar o tipo de compactação com a serialização do Avro. 

## <a name="create-edit-or-test-inputs"></a>Criar, editar ou entradas de teste

Pode utilizar o [portal Azure,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)e Visual [Studio Code](quick-create-vs-code.md) para adicionar e visualizar ou editar as inputs existentes no seu trabalho de streaming. Também pode testar ligações de entrada e consultas de [teste](stream-analytics-manage-job.md#test-your-query) a partir de dados de amostras do portal Azure, [Estúdio Visual](stream-analytics-vs-tools-local-run.md)e Código de Estúdio [Visual](visual-studio-code-local-run.md). Quando escreve uma consulta, lista a entrada na cláusula FROM. Pode obter a lista de inputs disponíveis a partir da página **Consulta** no portal. Se desejar utilizar várias inputs, pode `JOIN`-las ou escrever múltiplas consultas `SELECT`.


## <a name="stream-data-from-event-hubs"></a>Transmitir dados a partir dos Hubs de Eventos

Os Hubs de eventos do Azure fornece altamente dimensionável ingestors de eventos de publicação-subscrição. Um hub de eventos pode recolher milhões de eventos por segundo para que possa processar e analisar as quantidades massivas de dados produzidos pelos seus dispositivos e aplicações conectados. Juntos, os Hubs de eventos e Stream Analytics proporcionam uma solução de ponto-a-ponto para análise em tempo real. Os Hubs de eventos permite-lhe o feed de eventos para o Azure em tempo real, e as tarefas do Stream Analytics podem processar esses eventos em tempo real. Por exemplo, pode enviar cliques da web, as leituras dos sensores ou online de registo de eventos para Hubs de eventos. Em seguida, pode criar tarefas do Stream Analytics para utilizar os Hubs de eventos como os fluxos de dados de entrada para em tempo real filtrar, Agregar e correlação.

`EventEnqueuedUtcTime` é o carimbo temporal da chegada de um evento num centro de eventos e é o carimbo padrão dos eventos que vêm de Event Hubs a Stream Analytics. Para processar os dados como um fluxo utilizando uma marca de tempo na carga útil do evento, deve utilizar a palavra-chave [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="event-hubs-consumer-groups"></a>Grupos de consumidores de Hubs de Eventos

Deve configurar cada hub de eventos do Stream Analytics para ter seu próprio grupo de consumidores de entrada. Quando uma tarefa contém uma associação automática ou tem várias entradas, algumas entradas pode ser lidos por mais do que um leitor de downstream. Esta situação afeta o número de leitores num grupo de consumidor. Para evitar exceder o limite de Hubs de eventos de cinco de leitores por grupo de consumidor por partição, é recomendável designar um grupo de consumidores para cada tarefa do Stream Analytics. Há também um limite de 20 grupos de consumidores para um centro de eventos de nível Standard. Para mais informações, consulte [as inputs de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Criar uma entrada de Centros de Eventos

A tabela a seguir explica cada imóvel na nova página de **entrada** no portal Azure para transmitir informação de um hub de eventos:

| Propriedade | Descrição |
| --- | --- |
| **Pseudónimo de entrada** |Um nome amigável que utiliza na consulta da tarefa para fazer referência a esta entrada. |
| **Subscrição** | Escolha a subscrição na qual o recurso de hub de eventos existe. | 
| **Espaço de nome do Hub de Eventos** | O espaço de nomes do Hub de eventos é um contentor para um conjunto de entidades de mensagens. Quando cria um novo hub de eventos, também é criar o espaço de nomes. |
| **Nome do Hub do Evento** | O nome do hub de eventos para utilizar como entrada. |
| **Nome da política do Hub do Evento** | A política de acesso partilhado que fornece acesso ao Hub de eventos. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. Esta opção é preenchida automaticamente, a menos que selecionar a opção para fornecer as definições do Hub de eventos manualmente.|
| **Grupo de consumidores do Event Hub** (recomendado) | É altamente recomendado a utilizar um grupo de consumidores distintos para cada tarefa do Stream Analytics. Esta cadeia identifica o grupo de consumidor a utilizar para ingestão de dados do hub de eventos. Se não for especificado nenhum grupo de consumidores, a tarefa do Stream Analytics utiliza o grupo de consumidores $Default.  |
| **Chave de partição** | Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade. As chaves de partição são opcionais e são usadas para melhorar o desempenho da sua consulta se incluir uma cláusula DE PARTIÇÃO BY ou GROUP BY nesta propriedade. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [Outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados que está a chegar.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o 0 na frente para números decimais. |
| **Codificação** | UTF-8 atualmente é o único formato de codificação suportado. |
| **Tipo de compressão de eventos** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, tais como None (predefinição), GZip e Deflate. |

Quando seus dados é proveniente de uma entrada de fluxo do Hub de eventos, tem acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventoProcessadoUtcTime** |A data e hora em que o evento foi processado pelo Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e hora em que o evento foi recebido dos Hubs de eventos. |
| **Partida** |O ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, utilizar estes campos, pode escrever uma consulta semelhante ao seguinte exemplo:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Ao utilizar o Event Hub como ponto final para as rotas do Hub IoT, pode aceder aos metadados do IoT Hub utilizando a [função GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Dados de Stream do IoT Hub

O Azure IoT Hub é um evento de subscrição de subscrição de publicações altamente escalável otimizado para cenários IoT.

O carimbo de tempo padrão dos eventos provenientes de um Hub IoT em Stream Analytics é o carimbo temporal que o evento chegou ao IoT Hub, que é `EventEnqueuedUtcTime`. Para processar os dados como um fluxo utilizando uma marca de tempo na carga útil do evento, deve utilizar a palavra-chave [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="iot-hub-consumer-groups"></a>Grupos de consumidores DoIOT Hub

Deve configurar cada IoT Hub do Stream Analytics para ter seu próprio grupo de consumidores de entrada. Quando uma tarefa contém uma associação automática, ou quando tem várias entradas, alguma entrada pode ser lidos por mais do que um leitor de downstream. Esta situação afeta o número de leitores num grupo de consumidor. Para evitar exceder o limite do IoT Hub do Azure de cinco de leitores por grupo de consumidor por partição, é recomendável designar um grupo de consumidores para cada tarefa do Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurar um IoT Hub como um fluxo de dados de entrada

A tabela seguinte explica cada imóvel na nova página de **entrada** no portal Azure quando configura um Hub IoT como entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Pseudónimo de entrada** | Um nome amigável que utiliza na consulta da tarefa para fazer referência a esta entrada.|
| **Subscrição** | Escolha a subscrição na qual existe o recurso do IoT Hub. | 
| **Hub IoT** | O nome do IoT Hub para utilizar como entrada. |
| **Ponto final** | O ponto final do IoT Hub.|
| **Nome da política de acesso partilhado** | A política de acesso partilhado que fornece acesso ao IoT Hub. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. |
| **Chave da política de acesso partilhado** | A chave de acesso partilhado utilizada para autorizar o acesso ao IoT Hub.  Esta opção é preenchida automaticamente no, a menos que selecionar a opção para fornecer o Iot Hub as definições manualmente. |
| **Grupo de consumidores** | É altamente recomendado que utilize um grupo de consumidores diferentes para cada tarefa do Stream Analytics. O grupo de consumidores é utilizado para ingerir dados a partir do IoT Hub. Stream Analytics utiliza o grupo de consumidores $Default, a menos que especifique de outra forma.  |
| **Chave de partição** | Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade. As chaves de partição são opcionais e são usadas para melhorar o desempenho da sua consulta se incluir uma cláusula DE PARTIÇÃO BY ou GROUP BY nesta propriedade. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [Outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados que está a chegar.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o 0 na frente para números decimais. |
| **Codificação** | UTF-8 atualmente é o único formato de codificação suportado. |
| **Tipo de compressão de eventos** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, tais como None (predefinição), GZip e Deflate. |


Quando usa a transmitir dados a partir de um IoT Hub, tem acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventoProcessadoUtcTime** | A data e hora em que o evento foi processado. |
| **EventEnqueuedUtcTime** | A data e hora em que o evento foi recebido pelo IoT Hub. |
| **Partida** | O ID de partição com base em zero para o adaptador de entrada. |
| **IoTHub.MessageId** | Um ID que é utilizado para correlacionar a comunicação bidirecional no IoT Hub. |
| **IoTHub.CorrelationId** | Um ID que é utilizado em respostas de mensagem e comentários no IoT Hub. |
| **IoTHub.ConnectionDeviceId** | O ID de autenticação utilizado para enviar a mensagem. Este valor está marcado em mensagens de servicebound pelo IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | O ID de geração do dispositivo autenticado que foi utilizado para enviar a mensagem. Este valor está marcado em mensagens de servicebound pelo IoT Hub. |
| **IoTHub.EnqueuedTime** | A hora quando a mensagem foi recebida pelo IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Dados de Stream de armazenamento de BLOBs
Para cenários com grandes quantidades de dados não estruturados para armazenar na cloud, o armazenamento de Blobs do Azure oferece uma solução económica e dimensionável. Normalmente, os dados no armazenamento de BLOBs são considerados o dados em repouso; No entanto, os dados de BLOBs podem ser processados como um fluxo de dados pelo Stream Analytics. 

Processamento do registo é um cenário usado para usar entradas de armazenamento de Blobs com o Stream Analytics. Neste cenário, os arquivos de dados de telemetria tenham sido capturados a partir de um sistema e tem de ser analisado e processado para extrair dados significativos.

O carimbo de tempo padrão dos eventos de armazenamento blob no Stream Analytics é o carimbo temporal que a bolha foi modificada pela última vez, o que é `BlobLastModifiedUtcTime`. Se uma bolha for enviada para uma conta de armazenamento às 13:00, e o trabalho do Azure Stream Analytics começar a usar a opção *Agora* às 13:01, a bolha não será captada à medida que o seu tempo modificado cai fora do período de execução do trabalho.

Se uma bolha for enviada para um contentor de conta de armazenamento às 13:00, e o trabalho do Azure Stream Analytics for iniciado a usar o *Costume às* 13:00 ou mais cedo, a bolha será recolhida à medida que o seu tempo modificado cai dentro do período de execução do trabalho.

Se um trabalho de Azure Stream Analytics for iniciado a ser usado *Agora* às 13:00, e uma bolha for enviada para o contentor da conta de armazenamento às 13:01, o Azure Stream Analytics irá recolher a bolha.

Para processar os dados como um fluxo utilizando uma marca de tempo na carga útil do evento, deve utilizar a palavra-chave [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) Uma tarefa do Stream Analytics extrai dados da entrada de armazenamento de Blobs do Azure cada segundo se o arquivo de Blobs está disponível. Se o ficheiro blob não estiver disponível, há um término exponencial com um atraso de tempo máximo de 90 segundos.

As inputs formatadas cSV requerem uma linha de cabeçalho para definir campos para o conjunto de dados, e todos os campos da linha de cabeçalho devem ser únicos.

> [!NOTE]
> Stream Analytics não suporta a adição de conteúdo a um arquivo de blob existente. Stream Analytics irão ver apenas uma vez cada arquivo e quaisquer alterações que ocorrem no ficheiro depois da tarefa tem de ler os dados não são processadas. É melhor prática carregar todos os dados para um ficheiro de BLOBs de uma só vez e, em seguida, adicionar outros eventos mais recentes para um arquivo de blob diferente, novo.

Carregar um grande número de bolhas ao mesmo tempo pode fazer com que o Stream Analytics ignore a leitura de algumas bolhas em casos raros. Recomenda-se o upload de bolhas com pelo menos 2 segundos de diferença para o armazenamento blob. Se esta opção não for viável, pode utilizar os Centros de Eventos para transmitir grandes volumes de eventos. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurar o armazenamento de BLOBs como um fluxo de entrada 

A tabela seguinte explica cada imóvel na nova página de **entrada** no portal Azure quando configura o armazenamento blob como entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Pseudónimo de entrada** | Um nome amigável que utiliza na consulta da tarefa para fazer referência a esta entrada. |
| **Subscrição** | Escolha a subscrição na qual existe o recurso do IoT Hub. | 
| **Conta de armazenamento** | O nome da conta de armazenamento onde estão localizados os ficheiros de blob. |
| **Chave da conta de armazenamento** | A chave secreta associada à conta de armazenamento. Esta opção é preenchida automaticamente no, a menos que selecionar a opção para fornecer as definições de armazenamento de BLOBs manualmente. |
| **Contentor** | O contentor para o blob de entrada. Os contentores oferecem um agrupamento lógico para blobs armazenados no serviço de Blobs do Microsoft Azure. Ao carregar um blob para o serviço de armazenamento de Blobs do Azure, tem de especificar um contentor para esse blob. Pode escolher ou utilizar o recipiente **existente** ou **criar um novo** recipiente para ter um novo recipiente criado.|
| **Padrão de caminho** (opcional) | O caminho de ficheiro utilizado para localizar os blobs no contentor especificado. Se quiser ler bolhas da raiz do recipiente, não detete um padrão de caminho. Dentro do caminho, pode especificar uma ou mais instâncias das seguintes três variáveis: `{date}`, `{time}`ou `{partition}`<br/><br/>Exemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2: `cluster1/logs/{date}`<br/><br/>O carácter `*` não é um valor permitido para o prefixo do caminho. Só são <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">permitidos caracteres de blob Azure válidos.</a> Não inclua nomes de contentores ou nomes de ficheiros. |
| **Formato de data** (opcional) | Se usar a variável de data no caminho, o formato de data em que os ficheiros estão organizados. Exemplo: `YYYY/MM/DD` |
| **Formato de tempo** (opcional) |  Se usar a variável de tempo no caminho, o formato de hora em que os ficheiros estão organizados. Atualmente, o único valor suportado é `HH` por horas. |
| **Chave de partição** | Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade. As chaves de partição são opcionais e são usadas para melhorar o desempenho da sua consulta se incluir uma cláusula DE PARTIÇÃO BY ou GROUP BY nesta propriedade. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [Outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados que está a chegar.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o 0 na frente para números decimais. |
| **Codificação** | Para CSV e JSON, UTF-8 é atualmente o único formato de codificação suportado. |
| **Compressão** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, tais como None (predefinição), GZip e Deflate. |

Quando seus dados é proveniente de uma origem de armazenamento de BLOBs, tem acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **BlobName** |O nome do blob de entrada que vieram com o evento. |
| **EventoProcessadoUtcTime** |A data e hora em que o evento foi processado pelo Stream Analytics. |
| **BlobLastModificadoUtcTime** |A data e hora em que o blob foi modificado pela última vez. |
| **Partida** |O ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, utilizar estes campos, pode escrever uma consulta semelhante ao seguinte exemplo:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Quickstart: Criar um trabalho de Stream Analytics utilizando o portal Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
