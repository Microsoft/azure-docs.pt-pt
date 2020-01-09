---
title: Dados de Stream como entrada para o Azure Stream Analytics
description: Saiba mais sobre como configurar uma ligação de dados no Azure Stream Analytics. Entradas incluem um fluxo de dados de eventos e também os dados de referência.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 72568be0cf87770e8878f95de4a9c82842b470df
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646851"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Dados de Stream como entrada para o Stream Analytics

Stream Analytics tem integração de classe empresarial com fluxos de dados do Azure como entradas dentre três tipos de recursos:

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estes recursos de entrada podem residir na mesma subscrição do Azure que a tarefa de Stream Analytics ou numa subscrição diferente.

### <a name="compression"></a>Compressão

Stream Analytics suporta a compressão em origens de entrada todos os dados stream. Os tipos de compactação com suporte são: nenhuma, GZip e compactação deflate. Suporte para compressão não está disponível para os dados de referência. Se o formato de entrada é dados Avro são compactados, ele é tratado de forma transparente. Não tem de especificar o tipo de compactação com a serialização do Avro. 

## <a name="create-edit-or-test-inputs"></a>Criar, editar ou entradas de teste

Você pode usar o [portal do Azure](stream-analytics-quick-create-portal.md), o [Visual Studio](stream-analytics-quick-create-vs.md)e o [Visual Studio Code](quick-create-vs-code.md) para adicionar e exibir ou editar as entradas existentes em seu trabalho de streaming. Você também pode testar conexões de entrada e [testar consultas](stream-analytics-manage-job.md#test-your-query) de dados de exemplo do portal do Azure, do [Visual Studio](stream-analytics-vs-tools-local-run.md)e do [Visual Studio Code](visual-studio-code-local-run.md). Ao escrever uma consulta, você lista a entrada na cláusula FROM. Pode obter a lista de entradas de disponíveis dos **consulta** página no portal. Se pretender utilizar várias entradas, pode `JOIN` -los ou escrever várias `SELECT` consultas.


## <a name="stream-data-from-event-hubs"></a>Transmitir dados a partir dos Hubs de Eventos

Os Hubs de eventos do Azure fornece altamente dimensionável ingestors de eventos de publicação-subscrição. Um hub de eventos pode coletar milhões de eventos por segundo para que você possa processar e analisar grandes quantidades de dados produzidos por seus dispositivos e aplicativos conectados. Juntos, os Hubs de eventos e Stream Analytics proporcionam uma solução de ponto-a-ponto para análise em tempo real. Os Hubs de eventos permite-lhe o feed de eventos para o Azure em tempo real, e as tarefas do Stream Analytics podem processar esses eventos em tempo real. Por exemplo, pode enviar cliques da web, as leituras dos sensores ou online de registo de eventos para Hubs de eventos. Em seguida, pode criar tarefas do Stream Analytics para utilizar os Hubs de eventos como os fluxos de dados de entrada para em tempo real filtrar, Agregar e correlação.

`EventEnqueuedUtcTime` é o carimbo de hora da chegada de um evento num hub de eventos e o carimbo de hora padrão de eventos provenientes dos Hubs de eventos para o Stream Analytics. Para processar os dados como um fluxo usando um timestamp, no caso de payload, tem de utilizar o [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) palavra-chave.

### <a name="event-hubs-consumer-groups"></a>Grupos de consumidores dos hubs de eventos

Deve configurar cada hub de eventos do Stream Analytics para ter seu próprio grupo de consumidores de entrada. Quando uma tarefa contém uma associação automática ou tem várias entradas, algumas entradas pode ser lidos por mais do que um leitor de downstream. Esta situação afeta o número de leitores num grupo de consumidor. Para evitar exceder o limite de Hubs de eventos de cinco de leitores por grupo de consumidor por partição, é recomendável designar um grupo de consumidores para cada tarefa do Stream Analytics. Também há um limite de 20 grupos de consumidores para um hub de eventos de camada Standard. Para obter mais informações, consulte [entradas de resolver problemas relacionados com o Azure Stream Analytics](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Criar uma entrada de hubs de eventos

A tabela seguinte explica cada propriedade no **nova entrada** página no portal do Azure para entrada de dados de fluxo de um hub de eventos:

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** |Um nome amigável que utiliza na consulta da tarefa para fazer referência a esta entrada. |
| **Subscrição** | Escolha a subscrição na qual o recurso de hub de eventos existe. | 
| **Espaço de nomes de Hub de eventos** | O espaço de nomes do Hub de eventos é um contentor para um conjunto de entidades de mensagens. Quando cria um novo hub de eventos, também é criar o espaço de nomes. |
| **Nome do Hub de eventos** | O nome do hub de eventos para utilizar como entrada. |
| **Nome de política do Hub de eventos** | A política de acesso partilhado que fornece acesso ao Hub de eventos. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. Esta opção é preenchida automaticamente, a menos que selecionar a opção para fornecer as definições do Hub de eventos manualmente.|
| **Grupo de consumidores do Hub de eventos** (recomendado) | É altamente recomendado a utilizar um grupo de consumidores distintos para cada tarefa do Stream Analytics. Esta cadeia identifica o grupo de consumidor a utilizar para ingestão de dados do hub de eventos. Se não for especificado nenhum grupo de consumidores, a tarefa do Stream Analytics utiliza o grupo de consumidores $Default.  |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o 0 na frente para números decimais. |
| **Codificação** | UTF-8 atualmente é o único formato de codificação suportado. |
| **Tipo de compressão de evento** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, tais como None (predefinição), GZip e Deflate. |

Quando seus dados é proveniente de uma entrada de fluxo do Hub de eventos, tem acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** |A data e hora em que o evento foi processado pelo Stream Analytics. |
| **EventEnqueuedUtcTime** |A data e hora em que o evento foi recebido dos Hubs de eventos. |
| **PartitionId** |O ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, utilizar estes campos, pode escrever uma consulta semelhante ao seguinte exemplo:

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

## <a name="stream-data-from-iot-hub"></a>Dados de Stream do IoT Hub

O Hub IoT do Azure é um ingestão de eventos de publicação/assinatura altamente escalonável, otimizado para cenários de IoT.

O carimbo de hora padrão de eventos provenientes de um IoT Hub no Stream Analytics é o carimbo de hora a que o evento chegou no IoT Hub, que é `EventEnqueuedUtcTime`. Para processar os dados como um fluxo usando um timestamp, no caso de payload, tem de utilizar o [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) palavra-chave.

### <a name="iot-hub-consumer-groups"></a>Grupos de consumidores do Hub IOT

Deve configurar cada IoT Hub do Stream Analytics para ter seu próprio grupo de consumidores de entrada. Quando uma tarefa contém uma associação automática, ou quando tem várias entradas, alguma entrada pode ser lidos por mais do que um leitor de downstream. Esta situação afeta o número de leitores num grupo de consumidor. Para evitar exceder o limite do IoT Hub do Azure de cinco de leitores por grupo de consumidor por partição, é recomendável designar um grupo de consumidores para cada tarefa do Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurar um IoT Hub como um fluxo de dados de entrada

A tabela seguinte explica cada propriedade no **nova entrada** página no portal do Azure quando configurar um IoT Hub como um fluxo de entrada.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que utiliza na consulta da tarefa para fazer referência a esta entrada.|
| **Subscrição** | Escolha a subscrição na qual existe o recurso do IoT Hub. | 
| **Hub IoT** | O nome do IoT Hub para utilizar como entrada. |
| **Endpoint** | O ponto final do IoT Hub.|
| **Nome da política de acesso partilhado** | A política de acesso partilhado que fornece acesso ao IoT Hub. Cada política de acesso partilhado tem um nome, as permissões que definir e chaves de acesso. |
| **Chave de política de acesso partilhado** | A chave de acesso partilhado utilizada para autorizar o acesso ao IoT Hub.  Esta opção é preenchida automaticamente no, a menos que selecionar a opção para fornecer o Iot Hub as definições manualmente. |
| **Grupo de consumidores** | É altamente recomendado que utilize um grupo de consumidores diferentes para cada tarefa do Stream Analytics. O grupo de consumidores é utilizado para ingerir dados a partir do IoT Hub. Stream Analytics utiliza o grupo de consumidores $Default, a menos que especifique de outra forma.  |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o 0 na frente para números decimais. |
| **Codificação** | UTF-8 atualmente é o único formato de codificação suportado. |
| **Tipo de compressão de evento** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, tais como None (predefinição), GZip e Deflate. |


Quando usa a transmitir dados a partir de um IoT Hub, tem acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **EventProcessedUtcTime** | A data e hora em que o evento foi processado. |
| **EventEnqueuedUtcTime** | A data e hora em que o evento foi recebido pelo IoT Hub. |
| **PartitionId** | O ID de partição com base em zero para o adaptador de entrada. |
| **IoTHub.MessageId** | Um ID que é utilizado para correlacionar a comunicação bidirecional no IoT Hub. |
| **IoTHub.CorrelationId** | Um ID que é utilizado em respostas de mensagem e comentários no IoT Hub. |
| **IoTHub.ConnectionDeviceId** | O ID de autenticação utilizado para enviar a mensagem. Este valor está marcado em mensagens de servicebound pelo IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | O ID de geração do dispositivo autenticado que foi utilizado para enviar a mensagem. Este valor está marcado em mensagens de servicebound pelo IoT Hub. |
| **IoTHub.EnqueuedTime** | A hora quando a mensagem foi recebida pelo IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Dados de Stream de armazenamento de BLOBs
Para cenários com grandes quantidades de dados não estruturados para armazenar na cloud, o armazenamento de Blobs do Azure oferece uma solução económica e dimensionável. Normalmente, os dados no armazenamento de BLOBs são considerados o dados em repouso; No entanto, os dados de BLOBs podem ser processados como um fluxo de dados pelo Stream Analytics. 

Processamento do registo é um cenário usado para usar entradas de armazenamento de Blobs com o Stream Analytics. Neste cenário, os arquivos de dados de telemetria tenham sido capturados a partir de um sistema e tem de ser analisado e processado para extrair dados significativos.

O carimbo de hora padrão de eventos de armazenamento de BLOBs no Stream Analytics é o período de tempo que o blob foi modificado pela última vez, que é `BlobLastModifiedUtcTime`. Se um blob for carregado em uma conta de armazenamento em 13:00 e o trabalho de Azure Stream Analytics for iniciado usando a opção *agora* às 13:01, o BLOB não será selecionado, pois sua hora modificada fica fora do período de execução do trabalho.

Se um blob for carregado em um contêiner de conta de armazenamento em 13:00 e o trabalho de Azure Stream Analytics for iniciado usando a *hora personalizada* em 13:00 ou anterior, o blob será coletado à medida que sua hora modificada estiver dentro do período de execução do trabalho.

Se um trabalho de Azure Stream Analytics for iniciado usando o *agora* às 13:00 e um blob for carregado no contêiner da conta de armazenamento em 13:01, Azure Stream Analytics selecionará o blob.

Para processar os dados como um fluxo usando um timestamp, no caso de payload, tem de utilizar o [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) palavra-chave. Uma tarefa do Stream Analytics extrai dados da entrada de armazenamento de Blobs do Azure cada segundo se o arquivo de Blobs está disponível. Se o ficheiro blob não estiver disponível, há um término exponencial com um atraso de tempo máximo de 90 segundos.

As entradas formatadas em CSV exigem uma linha de cabeçalho para definir campos para o conjunto de dados, e todos os campos de linha de cabeçalho devem ser exclusivos.

> [!NOTE]
> Stream Analytics não suporta a adição de conteúdo a um arquivo de blob existente. Stream Analytics irão ver apenas uma vez cada arquivo e quaisquer alterações que ocorrem no ficheiro depois da tarefa tem de ler os dados não são processadas. É melhor prática carregar todos os dados para um ficheiro de BLOBs de uma só vez e, em seguida, adicionar outros eventos mais recentes para um arquivo de blob diferente, novo.

Carregar um número muito grande de blobs de uma vez pode fazer com que Stream Analytics ignore a leitura de alguns BLOBs em casos raros. É recomendável carregar blobs de pelo menos 2 segundos de distância para o armazenamento de BLOBs. Se essa opção não for viável, você poderá usar os hubs de eventos para transmitir grandes volumes de eventos. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurar o armazenamento de BLOBs como um fluxo de entrada 

A tabela seguinte explica cada propriedade no **nova entrada** página no portal do Azure quando configurar o armazenamento de BLOBs como um fluxo de entrada.

| Propriedade | Descrição |
| --- | --- |
| **Alias de entrada** | Um nome amigável que utiliza na consulta da tarefa para fazer referência a esta entrada. |
| **Subscrição** | Escolha a subscrição na qual existe o recurso do IoT Hub. | 
| **Conta de armazenamento** | O nome da conta de armazenamento onde estão localizados os ficheiros de blob. |
| **Chave da conta de armazenamento** | A chave secreta associada à conta de armazenamento. Esta opção é preenchida automaticamente no, a menos que selecionar a opção para fornecer as definições de armazenamento de BLOBs manualmente. |
| **Contentor** | O contentor para o blob de entrada. Os contentores oferecem um agrupamento lógico para blobs armazenados no serviço de Blobs do Microsoft Azure. Ao carregar um blob para o serviço de armazenamento de Blobs do Azure, tem de especificar um contentor para esse blob. Pode escolher entre **utilizar existente** contentor ou **criar nova** ter um novo contentor criado.|
| **Padrão do caminho** (opcional) | O caminho de ficheiro utilizado para localizar os blobs no contentor especificado. Se você quiser ler BLOBs da raiz do contêiner, não defina um padrão de caminho. No caminho, pode especificar uma ou mais instâncias das seguintes três variáveis: `{date}`, `{time}`, ou `{partition}`<br/><br/>Exemplo 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemplo 2: `cluster1/logs/{date}`<br/><br/>O `*` caráter não é um valor permitido para o prefixo do caminho. Só é válida <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caracteres de Blobs do Azure</a> são permitidos. Não inclua nomes de contêiner ou nomes de arquivo. |
| **Formato de data** (opcional) | Se usar a variável de data no caminho, o formato de data em que os ficheiros estão organizados. Exemplo: `YYYY/MM/DD` |
| **Formato de hora** (opcional) |  Se usar a variável de tempo no caminho, o formato de hora em que os ficheiros estão organizados. Atualmente o único valor suportado é `HH` durante horas. |
| **Formato de serialização de eventos** | O formato de serialização (JSON, CSV, Avro ou [outro (Protobuf, XML, proprietário...)](custom-deserializer.md)do fluxo de dados de entrada.  Certifique-se de que o formato JSON se alinha com a especificação e não inclui o 0 na frente para números decimais. |
| **Codificação** | Para CSV e JSON, UTF-8 é atualmente o único formato de codificação suportado. |
| **Compressão** | O tipo de compressão utilizado para ler o fluxo de dados de entrada, tais como None (predefinição), GZip e Deflate. |

Quando seus dados é proveniente de uma origem de armazenamento de BLOBs, tem acesso para os seguintes campos de metadados na sua consulta do Stream Analytics:

| Propriedade | Descrição |
| --- | --- |
| **BlobName** |O nome do blob de entrada que vieram com o evento. |
| **EventProcessedUtcTime** |A data e hora em que o evento foi processado pelo Stream Analytics. |
| **BlobLastModifiedUtcTime** |A data e hora em que o blob foi modificado pela última vez. |
| **PartitionId** |O ID de partição com base em zero para o adaptador de entrada. |

Por exemplo, utilizar estes campos, pode escrever uma consulta semelhante ao seguinte exemplo:

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
