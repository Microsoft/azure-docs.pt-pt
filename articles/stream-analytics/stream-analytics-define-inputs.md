---
title: Transmita dados como entrada no Azure Stream Analytics
description: Saiba mais sobre a criação de uma ligação de dados no Azure Stream Analytics. As inputs incluem um fluxo de dados de eventos, e também dados de referência.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 388f43fec9242f6a4b448483d9486aa4413d2612
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254472"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Transmita dados como entrada no Stream Analytics

O Stream Analytics tem integração de primeira classe com fluxos de dados azure como inputs de três tipos de recursos:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estes recursos de entrada podem viver na mesma subscrição do Azure que o seu trabalho stream analytics ou uma subscrição diferente.

### <a name="compression"></a>Compressão

Stream Analytics suporta compressão em todas as fontes de entrada de fluxo de dados. Os tipos de compressão suportado são: Nenhum, GZip e desinchar compressão. O suporte à compressão não está disponível para dados de referência. Se o formato de entrada for em dados Avro que são comprimidos, é manuseado de forma transparente. Não é necessário especificar o tipo de compressão com a serialização do Avro. 

## <a name="create-edit-or-test-inputs"></a>Criar, editar ou testar inputs

Pode utilizar o [portal Azure,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)e Visual [Studio Code](quick-create-vs-code.md) para adicionar e visualizar ou editar as inputs existentes no seu trabalho de streaming. Também pode testar ligações de entrada e consultas de [teste](stream-analytics-manage-job.md#test-your-query) a partir de dados de amostras do portal Azure, [Estúdio Visual](stream-analytics-vs-tools-local-run.md)e Código de Estúdio [Visual](visual-studio-code-local-run.md). Quando escreve uma consulta, lista a entrada na cláusula FROM. Pode obter a lista de inputs disponíveis a partir da página **Consulta** no portal. Se desejar utilizar várias inputs, pode `JOIN` `SELECT` ou escrever múltiplas consultas.


## <a name="stream-data-from-event-hubs"></a>Transmitir dados a partir dos Hubs de Eventos

O Azure Event Hubs oferece ingestores de eventos de subscrição de editoras altamente escaláveis. Um hub de eventos pode recolher milhões de eventos por segundo para que possa processar e analisar as quantidades massivas de dados produzidos pelos seus dispositivos e aplicações conectados. Juntos, os Centros de Eventos e o Stream Analytics fornecem uma solução de ponta a ponta para análiseem em tempo real. O Event Hubs permite-lhe alimentar eventos em Azure em tempo real, e os trabalhos da Stream Analytics podem processar esses eventos em tempo real. Por exemplo, pode enviar cliques web, leituras de sensores ou eventos de registo on-line para Event Hubs. Em seguida, pode criar trabalhos de Stream Analytics para usar os Event Hubs como os fluxos de dados de entrada para filtragem, agregação e correlação em tempo real.

`EventEnqueuedUtcTime`é o carimbo temporal da chegada de um evento num centro de eventos e é o carimbo padrão dos eventos que vêm de Event Hubs a Stream Analytics. Para processar os dados como um fluxo utilizando uma marca de tempo na carga útil do evento, deve utilizar a palavra-chave [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="event-hubs-consumer-groups"></a>Grupos de consumidores de Hubs de Eventos

Deve configurar cada entrada de hub de eventos stream analytics para ter o seu próprio grupo de consumidores. Quando um trabalho contém uma auto-ligação ou tem várias inputs, algumas inputs podem ser lidas por mais de um leitor a jusante. Esta situação afeta o número de leitores num único grupo de consumidores. Para evitar exceder o limite de Cinco leitores por grupo de consumidores por grupo de consumidores por partição, é uma boa prática designar um grupo de consumidores para cada trabalho da Stream Analytics. Há também um limite de 20 grupos de consumidores para um centro de eventos de nível Standard. Para mais informações, consulte [as inputs de Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Criar uma entrada de Centros de Eventos

A tabela a seguir explica cada imóvel na nova página de **entrada** no portal Azure para transmitir informação de um hub de eventos:

| Propriedade | Descrição |
| --- | --- |
| **Pseudónimo de entrada** |Um nome amigável que usa na consulta do trabalho para fazer referência a esta entrada. |
| **Subscrição** | Escolha a subscrição em que existe o recurso do hub do Evento. | 
| **Espaço de nomes do hub de eventos** | O espaço de nome do Event Hub é um recipiente para um conjunto de entidades de mensagens. Quando cria um novo centro de eventos, também cria o espaço de nome. |
| **Nome do Hub do Evento** | O nome do centro do evento para usar como entrada. |
| **Nome da política do Hub de Eventos** | A política de acesso partilhado que dá acesso ao Centro de Eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. Esta opção é automaticamente povoada, a menos que selecione a opção de fornecer manualmente as definições do Event Hub.|
| **Grupo de consumidores do Event Hub** (recomendado) | É altamente recomendado utilizar um grupo de consumidores distinto para cada trabalho da Stream Analytics. Esta cadeia identifica o grupo de consumidores para usar para ingerir dados do centro do evento. Se nenhum grupo de consumidores for especificado, o trabalho da Stream Analytics utiliza o grupo de consumidores $Default.  |
| **Chave de partição** | Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade. As chaves de partição são opcionais e são usadas para melhorar o desempenho da sua consulta se incluir uma cláusula DE PARTIÇÃO BY ou GROUP BY nesta propriedade. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [Outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados que está a chegar.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o líder 0 para números decimais. |
| **Codificação** | O UTF-8 é atualmente o único formato de codificação suportado. |
| **Tipo de compressão de eventos** | O tipo de compressão utilizado para ler o fluxo de dados que chegava, como nenhum (padrão), GZip ou Deflate. |

Quando os seus dados provêm de uma entrada de fluxo de Event Hub, você tem acesso aos seguintes campos de metadados na sua consulta Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventoProcessadoUtcTime** |A data e hora em que o evento foi processado pela Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e hora que o evento foi recebido pelo Event Hubs. |
| **Partida** |O ID de partição baseado em zero para o adaptador de entrada. |

Por exemplo, usando estes campos, pode escrever uma consulta como o seguinte exemplo:

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

## <a name="stream-data-from-iot-hub"></a>Dados de streaming do IoT Hub

O Azure IoT Hub é um evento de subscrição de subscrição de publicações altamente escalável otimizado para cenários IoT.

O carimbo de tempo padrão dos eventos provenientes de um Hub IoT em Stream Analytics `EventEnqueuedUtcTime`é o carimbo temporal que o evento chegou ao IoT Hub, que é . Para processar os dados como um fluxo utilizando uma marca de tempo na carga útil do evento, deve utilizar a palavra-chave [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="iot-hub-consumer-groups"></a>Grupos de consumidores DoIOT Hub

Deve configurar cada entrada do Stream Analytics IoT Hub para ter o seu próprio grupo de consumidores. Quando um trabalho contém uma auto-ligação ou quando tem várias inputs, alguma sonora pode ser lida por mais de um leitor a jusante. Esta situação afeta o número de leitores num único grupo de consumidores. Para evitar exceder o limite do Hub Azure IoT de cinco leitores por grupo de consumidores por partição, é uma melhor prática designar um grupo de consumidores para cada trabalho da Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configure um Hub IoT como uma entrada de fluxo de dados

A tabela seguinte explica cada imóvel na nova página de **entrada** no portal Azure quando configura um Hub IoT como entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Pseudónimo de entrada** | Um nome amigável que usa na consulta do trabalho para fazer referência a esta entrada.|
| **Subscrição** | Escolha a subscrição em que existe o recurso IoT Hub. | 
| **IoT Hub** | O nome do IoT Hub para usar como entrada. |
| **Ponto Final** | O ponto final para o IoT Hub.|
| **Nome da política de acesso partilhado** | A política de acesso partilhado que dá acesso ao IoT Hub. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| **Chave da política de acesso partilhado** | A chave de acesso partilhada usada para autorizar o acesso ao IoT Hub.  Esta opção é automaticamente povoada, a menos que selecione a opção de fornecer manualmente as definições do Hub Iot. |
| **Grupo de consumidores** | É altamente recomendável que utilize um grupo de consumidores diferente para cada trabalho da Stream Analytics. O grupo de consumidores está habituado a ingerir dados do IoT Hub. O Stream Analytics utiliza o grupo de consumidores $Default, a menos que especifique o contrário.  |
| **Chave de partição** | Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade. As chaves de partição são opcionais e são usadas para melhorar o desempenho da sua consulta se incluir uma cláusula DE PARTIÇÃO BY ou GROUP BY nesta propriedade. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [Outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados que está a chegar.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o líder 0 para números decimais. |
| **Codificação** | O UTF-8 é atualmente o único formato de codificação suportado. |
| **Tipo de compressão de eventos** | O tipo de compressão utilizado para ler o fluxo de dados que chegava, como nenhum (padrão), GZip ou Deflate. |


Quando utiliza dados de streaming de um Hub IoT, tem acesso aos seguintes campos de metadados na consulta Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventoProcessadoUtcTime** | A data e hora que o evento foi processado. |
| **EventEnqueuedUtcTime** | A data e hora que o evento foi recebido pelo IoT Hub. |
| **Partida** | O ID de partição baseado em zero para o adaptador de entrada. |
| **IoTHub.MessageId** | Uma identificação que é usada para correlacionar a comunicação bidirecional no IoT Hub. |
| **IoTHub.CorrelationId** | Um ID que é usado em respostas de mensagens e feedback no IoT Hub. |
| **IoTHub.ConnectionDeviceId** | O ID de autenticação usado para enviar esta mensagem. Este valor é carimbado em mensagens de serviço pelo IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | A identificação de geração do dispositivo autenticado que foi usado para enviar esta mensagem. Este valor é carimbado em mensagens de serviço pelo IoT Hub. |
| **IoTHub.EnqueuedTime** | O momento em que a mensagem foi recebida pelo IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Dados de streaming do armazenamento blob
Para cenários com grandes quantidades de dados não estruturados para armazenar na nuvem, o armazenamento da Blob Azure oferece uma solução rentável e escalável. Os dados no armazenamento blob são geralmente considerados dados em repouso; no entanto, os dados blob podem ser processados como um fluxo de dados pelo Stream Analytics. 

O processamento de registoé um cenário comumente utilizado para usar inputs de armazenamento Blob com Stream Analytics. Neste cenário, os ficheiros de dados de telemetria foram capturados a partir de um sistema e precisam de ser analisados e processados para extrair dados significativos.

O carimbo de tempo padrão dos eventos de armazenamento blob no Stream Analytics `BlobLastModifiedUtcTime`é o carimbo temporal que a bolha foi modificada pela última vez, ou seja. Se uma bolha for enviada para uma conta de armazenamento às 13:00, e o trabalho do Azure Stream Analytics começar a usar a opção *Agora* às 13:01, a bolha não será captada à medida que o seu tempo modificado cai fora do período de execução do trabalho.

Se uma bolha for enviada para um contentor de conta de armazenamento às 13:00, e o trabalho do Azure Stream Analytics for iniciado a usar o *Costume às* 13:00 ou mais cedo, a bolha será recolhida à medida que o seu tempo modificado cai dentro do período de execução do trabalho.

Se um trabalho de Azure Stream Analytics for iniciado a ser usado *Agora* às 13:00, e uma bolha for enviada para o contentor da conta de armazenamento às 13:01, o Azure Stream Analytics irá recolher a bolha.

Para processar os dados como um fluxo utilizando uma marca de tempo na carga útil do evento, deve utilizar a palavra-chave [TIMESTAMP BY.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) Um trabalho da Stream Analytics retira dados da entrada de armazenamento do Azure Blob a cada segundo se o ficheiro blob estiver disponível. Se o ficheiro blob não estiver disponível, existe um recuo exponencial com um atraso máximo de 90 segundos.

As inputs formatadas cSV requerem uma linha de cabeçalho para definir campos para o conjunto de dados, e todos os campos da linha de cabeçalho devem ser únicos.

> [!NOTE]
> O Stream Analytics não suporta a adição de conteúdo a um ficheiro blob existente. O Stream Analytics verá cada ficheiro apenas uma vez, e quaisquer alterações que ocorram no ficheiro após a leitura dos dados não são processadas. A melhor prática é carregar todos os dados para um ficheiro blob de uma só vez e, em seguida, adicionar eventos adicionais mais recentes a um ficheiro blob diferente.

Carregar um grande número de bolhas ao mesmo tempo pode fazer com que o Stream Analytics ignore a leitura de algumas bolhas em casos raros. Recomenda-se o upload de bolhas com pelo menos 2 segundos de diferença para o armazenamento blob. Se esta opção não for viável, pode utilizar os Centros de Eventos para transmitir grandes volumes de eventos. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configure o armazenamento blob como uma entrada de fluxo 

A tabela seguinte explica cada imóvel na nova página de **entrada** no portal Azure quando configura o armazenamento blob como entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Pseudónimo de entrada** | Um nome amigável que usa na consulta do trabalho para fazer referência a esta entrada. |
| **Subscrição** | Escolha a subscrição em que existe o recurso IoT Hub. | 
| **Conta de armazenamento** | O nome da conta de armazenamento onde estão localizados os ficheiros blob. |
| **Chave da conta de armazenamento** | A chave secreta associada à conta de armazenamento. Esta opção é automaticamente povoada, a menos que selecione a opção de fornecer manualmente as definições de armazenamento Blob. |
| **Contentor** | O recipiente para a entrada blob. Os contentores fornecem um agrupamento lógico para bolhas armazenadas no serviço Microsoft Azure Blob. Quando enviar uma bolha para o serviço de armazenamento Azure Blob, deve especificar um recipiente para essa bolha. Pode escolher ou utilizar o recipiente **existente** ou **criar um novo** recipiente para ter um novo recipiente criado.|
| **Padrão de caminho** (opcional) | O caminho de ficheiro utilizado para localizar as bolhas dentro do recipiente especificado. Se quiser ler bolhas da raiz do recipiente, não detete um padrão de caminho. Dentro do caminho, pode especificar uma ou mais instâncias `{date}` `{time}`das seguintes três variáveis: , ou`{partition}`<br/><br/>Exemplo 1:`cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2:`cluster1/logs/{date}`<br/><br/>O `*` personagem não é um valor permitido para o prefixo do caminho. Só são <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">permitidos caracteres de blob Azure válidos.</a> Não inclua nomes de contentores ou nomes de ficheiros. |
| **Formato de data** (opcional) | Se utilizar a variável data no caminho, o formato de data em que os ficheiros são organizados. Exemplo: `YYYY/MM/DD` |
| **Formato de tempo** (opcional) |  Se utilizar a variável de tempo no caminho, o formato de tempo em que os ficheiros são organizados. Atualmente, o único `HH` valor suportado é por horas. |
| **Chave de partição** | Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade. As chaves de partição são opcionais e são usadas para melhorar o desempenho da sua consulta se incluir uma cláusula DE PARTIÇÃO BY ou GROUP BY nesta propriedade. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [Outros (Protobuf, XML, proprietário...)](custom-deserializer.md)) do fluxo de dados que está a chegar.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o líder 0 para números decimais. |
| **Codificação** | Para CSV e JSON, o UTF-8 é atualmente o único formato de codificação suportado. |
| **Compressão** | O tipo de compressão utilizado para ler o fluxo de dados que chegava, como nenhum (padrão), GZip ou Deflate. |

Quando os seus dados provêm de uma fonte de armazenamento Blob, você tem acesso aos seguintes campos de metadados na sua consulta Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **BlobName** |O nome da entrada blob de onde o evento veio. |
| **EventoProcessadoUtcTime** |A data e hora em que o evento foi processado pela Stream Analytics. |
| **BlobLastModificadoUtcTime** |A data e a hora em que a bolha foi modificada pela última vez. |
| **Partida** |O ID de partição baseado em zero para o adaptador de entrada. |

Por exemplo, usando estes campos, pode escrever uma consulta como o seguinte exemplo:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
