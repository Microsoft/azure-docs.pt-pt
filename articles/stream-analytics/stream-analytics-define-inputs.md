---
title: Transmitir dados como entrada no Azure Stream Analytics
description: Saiba mais sobre como configurar uma conexão de dados no Azure Stream Analytics. As entradas incluem um fluxo de dados de eventos e também dados de referência.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 8f64b3381f22c31b58604477260b5dae4b84d19a
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72988272"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Transmitir dados como entrada no Stream Analytics

Stream Analytics tem integração de primeira classe com os fluxos de dados do Azure como entradas de três tipos de recursos:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Esses recursos de entrada podem residir na mesma assinatura do Azure que seu trabalho de Stream Analytics ou em uma assinatura diferente.

### <a name="compression"></a>Compressão

Stream Analytics dá suporte à compactação em todas as fontes de entrada de fluxo de dados. Os tipos de compactação com suporte são: nenhuma, GZip e compactação deflate. O suporte para compactação não está disponível para dados de referência. Se o formato de entrada for um dado de Avro compactado, ele será manipulado de forma transparente. Você não precisa especificar o tipo de compactação com a serialização Avro. 

## <a name="create-edit-or-test-inputs"></a>Criar, editar ou testar entradas

Você pode usar o [portal do Azure](stream-analytics-quick-create-portal.md), o [Visual Studio](stream-analytics-quick-create-vs.md)e o [Visual Studio Code](quick-create-vs-code.md) para adicionar e exibir ou editar as entradas existentes em seu trabalho de streaming. Você também pode testar conexões de entrada e [testar consultas](stream-analytics-manage-job.md#test-your-query) de dados de exemplo do portal do Azure, do [Visual Studio](stream-analytics-vs-tools-local-run.md)e do [Visual Studio Code](vscode-local-run.md). Ao escrever uma consulta, você lista a entrada na cláusula FROM. Você pode obter a lista de entradas disponíveis na página **consulta** no Portal. Se você quiser usar várias entradas, poderá `JOIN`-las ou escrever várias consultas de `SELECT`.


## <a name="stream-data-from-event-hubs"></a>Transmitir dados a partir dos Hubs de Eventos

Os hubs de eventos do Azure fornecem ingestãos de eventos de publicação/assinatura altamente escalonáveis. Um hub de eventos pode coletar milhões de eventos por segundo para que você possa processar e analisar grandes quantidades de dados produzidos por seus dispositivos e aplicativos conectados. Juntos, os hubs de eventos e Stream Analytics fornecem uma solução de ponta a ponta para análise em tempo real. Os hubs de eventos permitem alimentar eventos no Azure em tempo real e Stream Analytics trabalhos podem processar esses eventos em tempo real. Por exemplo, você pode enviar cliques da Web, leituras de sensor ou eventos de log online para os hubs de eventos. Em seguida, você pode criar trabalhos de Stream Analytics para usar os hubs de eventos como os fluxos de dados de entrada para filtragem, agregação e correlação em tempo real.

`EventEnqueuedUtcTime` é o carimbo de data/hora da chegada de um evento em um hub de eventos e é o carimbo de data/hora padrão dos eventos provenientes dos hubs de eventos para Stream Analytics. Para processar os dados como um fluxo usando um carimbo de data/hora na carga do evento, você deve usar o [carimbo de data/hora por](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) palavra-chave.

### <a name="event-hubs-consumer-groups"></a>Grupos de consumidores dos hubs de eventos

Você deve configurar cada entrada do hub de eventos Stream Analytics para ter seu próprio grupo de consumidores. Quando um trabalho contém uma auto-associação ou tem várias entradas, algumas entradas podem ser lidas por mais de um downstream de leitor. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite dos hubs de eventos de cinco leitores por grupo de consumidores por partição, é uma prática recomendada designar um grupo de consumidores para cada trabalho de Stream Analytics. Também há um limite de 20 grupos de consumidores para um hub de eventos de camada Standard. Para obter mais informações, consulte [solucionar problemas de Azure Stream Analytics entradas](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Criar uma entrada de hubs de eventos

A tabela a seguir explica cada propriedade na página **nova entrada** no portal do Azure para transmitir dados de entrada de um hub de eventos:

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** |Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Subscrição** | Escolha a assinatura na qual existe o recurso de Hub de eventos. | 
| **Namespace do hub de eventos** | O namespace do hub de eventos é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também cria o namespace. |
| **Nome do hub de eventos** | O nome do hub de eventos a ser usado como entrada. |
| **Nome da política do hub de eventos** | A política de acesso compartilhado que fornece acesso ao Hub de eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. Essa opção é preenchida automaticamente, a menos que você selecione a opção para fornecer as configurações do hub de eventos manualmente.|
| **Grupo de consumidores do hub de eventos** (recomendado) | É altamente recomendável usar um grupo de consumidores distinto para cada trabalho de Stream Analytics. Essa cadeia de caracteres identifica o grupo de consumidores a ser usado para ingerir dados do hub de eventos. Se nenhum grupo de consumidores for especificado, o trabalho de Stream Analytics usará o grupo de consumidores $Default.  |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Verifique se o formato JSON se alinha com a especificação e não inclui 0 à esquerda para números decimais. |
| **Codificação** | Atualmente, UTF-8 é o único formato de codificação com suporte. |
| **Tipo de compactação de evento** | O tipo de compactação usado para ler o fluxo de dados de entrada, como nenhum (padrão), GZip ou deflate. |

Quando seus dados vierem de uma entrada de fluxo do hub de eventos, você terá acesso aos seguintes campos de metadados em sua consulta de Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado pelo Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e a hora em que o evento foi recebido pelos hubs de eventos. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, usando esses campos, você pode escrever uma consulta como o exemplo a seguir:

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Ao usar o Hub de eventos como um ponto de extremidade para rotas do Hub IoT, você pode acessar os metadados do Hub IoT usando a [função GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Transmitir dados do Hub IoT

O Hub IoT do Azure é um ingestão de eventos de publicação/assinatura altamente escalonável, otimizado para cenários de IoT.

O carimbo de data/hora padrão de eventos provenientes de um hub IoT no Stream Analytics é o carimbo de data/hora em que o evento chegou ao Hub IoT, que é `EventEnqueuedUtcTime`. Para processar os dados como um fluxo usando um carimbo de data/hora na carga do evento, você deve usar o [carimbo de data/hora por](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) palavra-chave.

### <a name="iot-hub-consumer-groups"></a>Grupos de consumidores do Hub IOT

Você deve configurar cada entrada de Hub IoT Stream Analytics para ter seu próprio grupo de consumidores. Quando um trabalho contém uma auto-associação ou quando tem várias entradas, algumas entradas podem ser lidas por mais de um downstream de leitor. Essa situação afeta o número de leitores em um único grupo de consumidores. Para evitar exceder o limite do Hub IoT do Azure de cinco leitores por grupo de consumidores por partição, é uma prática recomendada designar um grupo de consumidores para cada trabalho de Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurar um hub IoT como uma entrada de fluxo de dados

A tabela a seguir explica cada propriedade na página **nova entrada** no portal do Azure quando você configura um hub IOT como uma entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada.|
| **Subscrição** | Escolha a assinatura na qual o recurso do Hub IoT existe. | 
| **Hub IoT** | O nome do Hub IoT a ser usado como entrada. |
| **Extremidade** | O ponto de extremidade para o Hub IoT.|
| **Nome da política de acesso compartilhado** | A política de acesso compartilhado que fornece acesso ao Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| **Chave de política de acesso compartilhado** | A chave de acesso compartilhado usada para autorizar o acesso ao Hub IoT.  Essa opção é preenchida automaticamente no, a menos que você selecione a opção para fornecer as configurações do Hub IOT manualmente. |
| **Grupo de consumidores** | É altamente recomendável que você use um grupo de consumidores diferente para cada trabalho de Stream Analytics. O grupo de consumidores é usado para ingerir dados do Hub IoT. Stream Analytics usa o grupo de consumidores de $Default, a menos que você especifique o contrário.  |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Verifique se o formato JSON se alinha com a especificação e não inclui 0 à esquerda para números decimais. |
| **Codificação** | Atualmente, UTF-8 é o único formato de codificação com suporte. |
| **Tipo de compactação de evento** | O tipo de compactação usado para ler o fluxo de dados de entrada, como nenhum (padrão), GZip ou deflate. |


Ao usar dados de fluxo de um hub IoT, você tem acesso aos seguintes campos de metadados em sua consulta de Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** | A data e a hora em que o evento foi processado. |
| **EventEnqueuedUtcTime** | A data e a hora em que o evento foi recebido pelo Hub IoT. |
| **PartitionId** | A ID de partição com base em zero para o adaptador de entrada. |
| **IoTHub. MessageId** | Uma ID que é usada para correlacionar a comunicação bidirecional no Hub IoT. |
| **IoTHub. CorrelationId** | Uma ID que é usada em respostas de mensagens e comentários no Hub IoT. |
| **IoTHub. ConnectionDeviceId** | A ID de autenticação usada para enviar esta mensagem. Esse valor é carimbado em mensagens de entrada pelo Hub IoT. |
| **IoTHub. ConnectionDeviceGenerationId** | A ID de geração do dispositivo autenticado que foi usado para enviar esta mensagem. Esse valor é carimbado em mensagens de entrada pelo Hub IoT. |
| **IoTHub. EnqueuedTime** | A hora em que a mensagem foi recebida pelo Hub IoT. |


## <a name="stream-data-from-blob-storage"></a>Transmitir dados do armazenamento de BLOBs
Para cenários com grandes quantidades de dados não estruturados para armazenar na nuvem, o armazenamento de BLOBs do Azure oferece uma solução econômica e escalonável. Os dados no armazenamento de BLOBs geralmente são considerados dados em repouso; no entanto, os dados de blob podem ser processados como um fluxo de dados por Stream Analytics. 

O processamento de log é um cenário comumente usado para usar entradas de armazenamento de blob com Stream Analytics. Nesse cenário, os arquivos de dados de telemetria foram capturados de um sistema e precisam ser analisados e processados para extrair dados significativos.

O carimbo de data/hora padrão dos eventos de armazenamento de BLOBs em Stream Analytics é o carimbo de data/hora em que o blob foi modificado pela última vez, `BlobLastModifiedUtcTime`que Para processar os dados como um fluxo usando um carimbo de data/hora na carga do evento, você deve usar o [carimbo de data/hora por](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) palavra-chave. Um trabalho Stream Analytics efetua pull dos dados da entrada do armazenamento de BLOBs do Azure a cada segundo se o arquivo de blob estiver disponível. Se o arquivo de blob estiver indisponível, haverá uma retirada exponencial com um atraso de tempo máximo de 90 segundos.

As entradas formatadas em CSV exigem uma linha de cabeçalho para definir campos para o conjunto de dados, e todos os campos de linha de cabeçalho devem ser exclusivos.

> [!NOTE]
> Stream Analytics não dá suporte à adição de conteúdo a um arquivo de blob existente. Stream Analytics exibirá cada arquivo apenas uma vez e quaisquer alterações que ocorram no arquivo depois que o trabalho tiver lido os dados não serão processados. A prática recomendada é carregar todos os dados de um arquivo de blob de uma vez e, em seguida, adicionar mais novos eventos a um novo arquivo de blob diferente.

Carregar um número muito grande de blobs de uma vez pode fazer com que Stream Analytics ignore a leitura de alguns BLOBs em casos raros. É recomendável carregar blobs de pelo menos 2 segundos de distância para o armazenamento de BLOBs. Se essa opção não for viável, você poderá usar os hubs de eventos para transmitir grandes volumes de eventos. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurar o armazenamento de BLOBs como uma entrada de fluxo 

A tabela a seguir explica cada propriedade na página **nova entrada** no portal do Azure quando você configura o armazenamento de BLOBs como uma entrada de fluxo.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que você usa na consulta do trabalho para fazer referência a essa entrada. |
| **Subscrição** | Escolha a assinatura na qual o recurso do Hub IoT existe. | 
| **Conta de armazenamento** | O nome da conta de armazenamento onde os arquivos de blob estão localizados. |
| **Chave da conta de armazenamento** | A chave secreta associada à conta de armazenamento. Essa opção é preenchida automaticamente no, a menos que você selecione a opção para fornecer as configurações de armazenamento de BLOBs manualmente. |
| **Container** | O contêiner para a entrada de BLOB. Os contêineres fornecem um agrupamento lógico para BLOBs armazenados no serviço blob Microsoft Azure. Ao carregar um blob para o serviço de armazenamento de BLOBs do Azure, você deve especificar um contêiner para esse BLOB. Você pode escolher usar o contêiner **existente** ou **criar novo** para ter um novo contêiner criado.|
| **Padrão de caminho** (opcional) | O caminho do arquivo usado para localizar os BLOBs dentro do contêiner especificado. No caminho, você pode especificar uma ou mais instâncias das três variáveis a seguir: `{date}`, `{time}`ou `{partition}`<br/><br/>Exemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2: `cluster1/logs/{date}`<br/><br/>O caractere de `*` não é um valor permitido para o prefixo de caminho. Somente <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de blob do Azure</a> válidos são permitidos. Não incluem nomes de contêiner ou nomes de arquivo. |
| **Formato de data** (opcional) | Se você usar a variável de data no caminho, o formato de data no qual os arquivos são organizados. Exemplo: `YYYY/MM/DD` |
| **Formato de hora** (opcional) |  Se você usar a variável de tempo no caminho, o formato de hora no qual os arquivos são organizados. Atualmente, o único valor com suporte é `HH` por horas. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Verifique se o formato JSON se alinha com a especificação e não inclui 0 à esquerda para números decimais. |
| **Codificação** | Para CSV e JSON, o UTF-8 é o único formato de codificação com suporte no momento. |
| **Çã** | O tipo de compactação usado para ler o fluxo de dados de entrada, como nenhum (padrão), GZip ou deflate. |

Quando seus dados vierem de uma fonte de armazenamento de BLOBs, você terá acesso aos seguintes campos de metadados em sua consulta de Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **BlobName** |O nome do blob de entrada do qual o evento veio. |
| **EventProcessedUtcTime** |A data e a hora em que o evento foi processado pelo Stream Analytics. |
| **BlobLastModifiedUtcTime** |A data e a hora da última modificação do blob. |
| **PartitionId** |A ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, usando esses campos, você pode escrever uma consulta como o exemplo a seguir:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início rápido: criar um trabalho de Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
