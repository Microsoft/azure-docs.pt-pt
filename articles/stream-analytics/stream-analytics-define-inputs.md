---
title: Fluxo de dados como entrada no Azure Stream Analytics
description: Saiba como configurar uma ligação de dados no Azure Stream Analytics. As entradas incluem um fluxo de dados de eventos, e também dados de referência.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: fb5aca1739fbb4a77cbcb7eed6b9dce1b3ccc182
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027589"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Fluxo de dados como entrada no Stream Analytics

O Stream Analytics tem integração de primeira classe com fluxos de dados Azure como entradas de três tipos de recursos:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estes recursos de entrada podem viver na mesma subscrição Azure que o seu trabalho Stream Analytics ou uma subscrição diferente.

### <a name="compression"></a>Compressão

O Stream Analytics suporta a compressão em todas as fontes de entrada de fluxo de dados. Os tipos de compressão suportados são: Nenhum, GZip e Deflate compressão. O suporte à compressão não está disponível para dados de referência. Se o formato de entrada for dados da Avro que são comprimidos, é tratado de forma transparente. Não precisa de especificar o tipo de compressão com a serialização do Avro. 

## <a name="create-edit-or-test-inputs"></a>Criar, editar ou testar entradas

Pode utilizar o [portal Azure,](stream-analytics-quick-create-portal.md) [Visual Studio](stream-analytics-quick-create-vs.md)e Visual [Studio Code](quick-create-visual-studio-code.md) para adicionar e visualizar ou editar as entradas existentes no seu trabalho de streaming. Também pode testar ligações de entrada e consultas de [teste](stream-analytics-manage-job.md#test-your-query) a partir de dados de amostra do portal Azure, [Visual Studio](stream-analytics-vs-tools-local-run.md)e Visual Studio [Code](visual-studio-code-local-run.md). Quando escreve uma consulta, lista a entrada na cláusula FROM. Pode obter a lista de entradas disponíveis na página **'Consulta'** no portal. Se desejar utilizar várias entradas, pode `JOIN` fazê-las ou escrever várias `SELECT` consultas.


## <a name="stream-data-from-event-hubs"></a>Transmitir dados a partir dos Hubs de Eventos

A Azure Event Hubs fornece investidores de eventos altamente escaláveis. Um centro de eventos pode recolher milhões de eventos por segundo para que possa processar e analisar as quantidades massivas de dados produzidos pelos seus dispositivos e aplicações conectados. Em conjunto, os Event Hubs e stream Analytics fornecem uma solução de ponta a ponta para análises em tempo real. O Event Hubs permite-lhe alimentar eventos em Azure em tempo real, e os trabalhos do Stream Analytics podem processar esses eventos em tempo real. Por exemplo, pode enviar cliques web, leituras de sensores ou eventos de registo on-line para Os Centros de Eventos. Em seguida, pode criar trabalhos stream Analytics para usar os Centros de Eventos como fluxos de dados de entrada para filtragem, agregação e correlação em tempo real.

`EventEnqueuedUtcTime` é o tempo de chegada de um evento num centro de eventos e é o horário padrão de eventos vindos de Event Hubs para Stream Analytics. Para processar os dados como um stream utilizando um timetamp na carga útil do evento, deve utilizar a palavra-chave [TIMETAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="event-hubs-consumer-groups"></a>Grupos de consumidores de centros de eventos

Você deve configurar cada entrada do centro de eventos Stream Analytics para ter o seu próprio grupo de consumidores. Quando um trabalho contém uma união autónoma ou tem múltiplas entradas, algumas entradas podem ser lidas por mais de um leitor a jusante. Esta situação tem impacto no número de leitores num único grupo de consumidores. Para evitar exceder o limite de cinco leitores por grupo de consumidores por divisão, é uma boa prática designar um grupo de consumidores para cada trabalho do Stream Analytics. Existe também um limite de 20 grupos de consumidores para um centro de eventos standard tier. Para obter mais informações, consulte as [entradas Troubleshoot Azure Stream Analytics](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Criar uma entrada a partir de Centros de Eventos

A tabela seguinte explica cada propriedade na página **de entrada Nova** no portal Azure para transmitir a entrada de dados a partir de um centro de eventos:

| Propriedade | Descrição |
| --- | --- |
| **Inserir pseudónimo** |Um nome amigável que usa na consulta do trabalho para fazer referência a esta entrada. |
| **Subscrição** | Escolha a subscrição em que o recurso do hub do Evento existe. | 
| **Espaço de nomes do hub de eventos** | O espaço de nomes Do Event Hub é um recipiente para um conjunto de entidades de mensagens. Quando cria um novo centro de eventos, também cria o espaço de nomes. |
| **Nome do Hub de Eventos** | O nome do centro de eventos para usar como entrada. |
| **Nome da política do Hub de Eventos** | A política de acesso partilhado que dá acesso ao Centro de Eventos. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. Esta opção é preenchida automaticamente, a menos que selecione a opção para fornecer manualmente as definições do Centro de Eventos.|
| **Grupo de consumidores Event Hub** (recomendado) | É altamente recomendado utilizar um grupo de consumidores distinto para cada trabalho stream Analytics. Esta cadeia identifica o grupo de consumidores para usar para ingerir dados do centro de eventos. Se nenhum grupo de consumidores for especificado, o trabalho stream Analytics utiliza o grupo de consumidores $Default.  |
| **Chave de partição** | Este é um campo opcional que só está disponível se o seu trabalho estiver configurado para utilizar o nível de [compatibilidade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) 1.2 ou superior. Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade aqui. Isto é usado para melhorar o desempenho da sua consulta se inclui uma cláusula PARTITION BY ou GROUP BY neste imóvel. Se este trabalho utilizar o nível de compatibilidade 1.2 ou superior, este campo não é de "PartitionId". |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro, ou [Outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Certifique-se de que o formato JSON está alinhado com a especificação e não inclui a liderança 0 para números decimais. |
| **Encoding** (Codificação) | UtF-8 é atualmente o único formato de codificação suportado. |
| **Tipo de compressão de eventos** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, como Nenhum (padrão), GZip ou Deflate. |

Quando os seus dados provêm de uma entrada de fluxo do Event Hub, tem acesso aos seguintes campos de metadados na sua consulta Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** |A data e hora em que o evento foi processado pela Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e hora em que o evento foi recebido pelo Event Hubs. |
| **PartitionId** |O ID de partição de base zero para o adaptador de entrada. |

Por exemplo, usando estes campos, pode escrever uma consulta como o seguinte exemplo:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Ao utilizar o Event Hub como ponto final para rotas IoT Hub, pode aceder aos metadados IoT Hub utilizando a [função GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Dados de fluxo do IoT Hub

Azure IoT Hub é um evento altamente escalável de publicação-subscrição ou otimizado para cenários IoT.

O horário padrão de eventos provenientes de um Hub IoT em Stream Analytics é o tempotampido que o evento chegou ao IoT Hub, que é `EventEnqueuedUtcTime` . Para processar os dados como um stream utilizando um timetamp na carga útil do evento, deve utilizar a palavra-chave [TIMETAMP BY.](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)

### <a name="iot-hub-consumer-groups"></a>Grupos de consumidores Iot Hub

Você deve configurar cada entrada Stream Analytics IoT Hub para ter o seu próprio grupo de consumidores. Quando um trabalho contém uma união auto-união ou quando tem múltiplas entradas, algumas entradas podem ser lidas por mais de um leitor a jusante. Esta situação tem impacto no número de leitores num único grupo de consumidores. Para evitar exceder o limite do Azure IoT Hub de cinco leitores por grupo de consumidores por divisão, é uma boa prática designar um grupo de consumidores para cada trabalho do Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configure um Hub IoT como entrada de fluxo de dados

A tabela seguinte explica cada propriedade na página **de entrada Nova** no portal Azure quando configura um Hub IoT como entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Inserir pseudónimo** | Um nome amigável que usa na consulta do trabalho para fazer referência a esta entrada.|
| **Subscrição** | Escolha a subscrição em que existe o recurso IoT Hub. | 
| **Hub IoT** | O nome do Hub IoT para usar como entrada. |
| **Ponto final** | O ponto final para o Hub IoT.|
| **Nome da política de acesso partilhado** | A política de acesso partilhado que dá acesso ao IoT Hub. Cada política de acesso partilhado tem um nome, permissões que definiu e chaves de acesso. |
| **Chave de política de acesso compartilhado** | A chave de acesso partilhada usada para autorizar o acesso ao IoT Hub.  Esta opção é automaticamente preenchida a menos que selecione a opção para fornecer manualmente as definições do Iot Hub. |
| **Grupo de consumidores** | É altamente recomendável que utilize um grupo de consumidores diferente para cada trabalho stream Analytics. O grupo de consumidores é utilizado para ingerir dados do IoT Hub. O Stream Analytics utiliza o grupo de consumidores $Default, a menos que especifique o contrário.  |
| **Chave de partição** | Este é um campo opcional que só está disponível se o seu trabalho estiver configurado para utilizar o nível de [compatibilidade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) 1.2 ou superior. Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade aqui. Isto é usado para melhorar o desempenho da sua consulta se inclui uma cláusula PARTITION BY ou GROUP BY neste imóvel. Se este trabalho utilizar o nível de compatibilidade 1.2 ou superior, este campo não é de "PartitionId". |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro, ou [Outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Certifique-se de que o formato JSON está alinhado com a especificação e não inclui a liderança 0 para números decimais. |
| **Encoding** (Codificação) | UtF-8 é atualmente o único formato de codificação suportado. |
| **Tipo de compressão de eventos** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, como Nenhum (padrão), GZip ou Deflate. |


Quando utiliza dados de fluxo a partir de um Hub IoT, tem acesso aos seguintes campos de metadados na sua consulta Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** | A data e a hora em que o evento foi processado. |
| **EventEnqueuedUtcTime** | A data e hora em que o evento foi recebido pelo IoT Hub. |
| **PartitionId** | O ID de partição de base zero para o adaptador de entrada. |
| **IoTHub.MessageId** | Uma identificação que é usada para correlacionar comunicação bidirecional no IoT Hub. |
| **IoTHub.CorrelationId** | Um ID que é usado em respostas de mensagens e feedback no IoT Hub. |
| **IoTHub.ConnectionDeviceId** | A identificação de autenticação usada para enviar esta mensagem. Este valor é carimbado em mensagens de serviço pelo IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | A geração ID do dispositivo autenticado que foi usado para enviar esta mensagem. Este valor é carimbado em mensagens de serviço pelo IoT Hub. |
| **IoTHub.EnqueuedTime** | O momento em que a mensagem foi recebida pelo IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Dados de fluxo do armazenamento blob
Para cenários com grandes quantidades de dados não estruturados para armazenar na nuvem, o armazenamento Azure Blob oferece uma solução rentável e escalável. Os dados relativos ao armazenamento blob são geralmente considerados dados em repouso; no entanto, os dados blob podem ser tratados como um fluxo de dados pelo Stream Analytics. 

O processamento de registos é um cenário comumente utilizado para a utilização de entradas de armazenamento Blob com Stream Analytics. Neste cenário, os ficheiros de dados de telemetria foram capturados a partir de um sistema e precisam de ser analisados e processados para extrair dados significativos.

O tempo de tempo padrão dos eventos de armazenamento Blob em Stream Analytics é o tempotampido que a bolha foi modificada pela última vez, que é `BlobLastModifiedUtcTime` . Se uma bolha for enviada para uma conta de armazenamento às 13:00, e o trabalho do Azure Stream Analytics for iniciado usando a opção *Agora* às 13:01, a bolha não será levantada à medida que o seu tempo modificado cai fora do período de funcionamento do trabalho.

Se uma bolha for enviada para um recipiente de conta de armazenamento às 13:00, e o trabalho do Azure Stream Analytics for iniciado usando *o Tempo Personalizado* às 13:00 ou mais cedo, a bolha será recolhida à medida que o seu tempo modificado cai dentro do período de funcionamento do trabalho.

Se um trabalho do Azure Stream Analytics for iniciado a ser utilizado *agora* às 13:00, e uma bolha for enviada para o contentor da conta de armazenamento às 13:01, o Azure Stream Analytics irá recolher a bolha. O tempotabo atribuído a cada bolha baseia-se apenas `BlobLastModifiedTime` em . A pasta em que se encontra a bolha não tem qualquer relação com o tempotando atribuído. Por exemplo, se houver uma bolha *2019/10-01/b1.txt* com um `BlobLastModifiedTime` de 2019-11-11, então o tempotando atribuído a esta bolha é 2019-11-11.

Para processar os dados como um stream utilizando um timetamp na carga útil do evento, deve utilizar a palavra-chave [TIMETAMP BY.](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) Um trabalho stream Analytics retira dados da entrada de armazenamento Azure Blob a cada segundo se o ficheiro blob estiver disponível. Se o ficheiro blob não estiver disponível, existe um recuo exponencial com um atraso máximo de 90 segundos.

As entradas formatadas por CSV requerem uma linha de cabeçalho para definir campos para o conjunto de dados, e todos os campos de linha de cabeçalho devem ser únicos.

> [!NOTE]
> O Stream Analytics não suporta a adição de conteúdo a um ficheiro blob existente. Stream Analytics irá ver cada ficheiro apenas uma vez, e quaisquer alterações que ocorram no ficheiro após a leitura do trabalho os dados não são processados. A melhor prática é carregar todos os dados para um ficheiro blob de uma só vez e, em seguida, adicionar eventos mais recentes adicionais a um novo e diferente ficheiro blob.

Em cenários em que muitas bolhas são continuamente adicionadas e stream Analytics está processando as bolhas à medida que são adicionadas, é possível que algumas bolhas sejam ignoradas em casos raros devido à granularidade do `BlobLastModifiedTime` . Pode atenuar isto carregando bolhas com pelo menos dois segundos de intervalo. Se esta opção não for viável, pode utilizar o Event Hubs para transmitir grandes volumes de eventos.

### <a name="configure-blob-storage-as-a-stream-input"></a>Configure o armazenamento blob como entrada de fluxo 

A tabela seguinte explica cada propriedade na página **de entrada Nova** no portal Azure quando configura o armazenamento blob como entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Inserir pseudónimo** | Um nome amigável que usa na consulta do trabalho para fazer referência a esta entrada. |
| **Subscrição** | Escolha a subscrição em que existe o recurso IoT Hub. | 
| **Conta de armazenamento** | O nome da conta de armazenamento onde estão os ficheiros blob. |
| **Chave da conta de armazenamento** | A chave secreta associada à conta de armazenamento. Esta opção é automaticamente preenchida a menos que selecione a opção de fornecer manualmente as definições de armazenamento Blob. |
| **Container** (Contentor) | O recipiente para a entrada do blob. Os recipientes fornecem um agrupamento lógico para bolhas armazenadas no serviço Microsoft Azure Blob. Quando fizer o upload de uma bolha para o serviço de armazenamento Azure Blob, deve especificar um recipiente para essa bolha. Pode escolher o recipiente **existente** ou  **criar um novo** recipiente para criar um novo recipiente.|
| **Padrão de caminho** (opcional) | O caminho do ficheiro utilizado para localizar as bolhas dentro do recipiente especificado. Se quiser ler bolhas da raiz do recipiente, não descreva um padrão de caminho. Dentro do caminho, pode especificar uma ou mais instâncias das seguintes três variáveis: `{date}` `{time}` , ou `{partition}`<br/><br/>Exemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2: `cluster1/logs/{date}`<br/><br/>O `*` personagem não é um valor permitido para o prefixo do caminho. Apenas <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">são permitidos caracteres blob Azure</a> válidos. Não inclua nomes de contentores ou nomes de ficheiros. |
| **Formato de data** (opcional) | Se utilizar a variável de data no caminho, o formato de data em que os ficheiros são organizados. Exemplo: `YYYY/MM/DD` <br/><br/> Quando a entrada blob tem `{date}` ou `{time}` no seu caminho, as pastas são vistas em ordem de tempo ascendente.|
| **Formato de tempo** (opcional) |  Se utilizar a variável de tempo no caminho, o formato de tempo em que os ficheiros são organizados. Atualmente, o único valor suportado é `HH` de horas. |
| **Chave de partição** | Este é um campo opcional que só está disponível se o seu trabalho estiver configurado para utilizar o nível de [compatibilidade](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) 1.2 ou superior. Se a sua entrada for dividida por uma propriedade, pode adicionar o nome desta propriedade aqui. Isto é usado para melhorar o desempenho da sua consulta se inclui uma cláusula PARTITION BY ou GROUP BY neste imóvel. Se este trabalho utilizar o nível de compatibilidade 1.2 ou superior, este campo não é de "PartitionId". |
| **Contagem de divisórias de entrada** | Este campo só está presente quando {partition} está presente no padrão do caminho. O valor desta propriedade é um inteiro >=1. Onde quer que {partition} apareça no pathPattern, será utilizado um número entre 0 e o valor deste campo -1. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro, ou [Outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Certifique-se de que o formato JSON está alinhado com a especificação e não inclui a liderança 0 para números decimais. |
| **Encoding** (Codificação) | Para cSV e JSON, UTF-8 é atualmente o único formato de codificação suportado. |
| **Compressão** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, como Nenhum (padrão), GZip ou Deflate. |

Quando os seus dados provêm de uma fonte de armazenamento Blob, tem acesso aos seguintes campos de metadados na sua consulta Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **Nome Blob** |O nome da bolha de entrada de onde o evento veio. |
| **EventProcessedUtcTime** |A data e hora em que o evento foi processado pela Stream Analytics. |
| **BlobLastModifiedUtcTime** |A data e a hora em que a bolha foi modificada pela última vez. |
| **PartitionId** |O ID de partição de base zero para o adaptador de entrada. |

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
