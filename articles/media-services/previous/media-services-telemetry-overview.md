---
title: Telemetria dos Serviços de Mídia Azure Microsoft Docs
description: Este artigo fornece uma visão geral da telemetria da Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: e2cbb36158722a47518f575b391340b5e25bd908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895782"
---
# <a name="azure-media-services-telemetry"></a>Telemetria azure Media Services  


> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

A Azure Media Services (AMS) permite-lhe aceder a dados de telemetria/métricas para os seus serviços. A versão atual da AMS permite-lhe recolher dados de telemetria para **o Canal**ao vivo, **streamingEndpoint**e entidades de **Arquivo** ao vivo. 

A telemetria está escrita numa mesa de armazenamento numa conta de Armazenamento Azure que especifica (normalmente, usaria a conta de armazenamento associada à sua conta AMS). 

O sistema de telemetria não gere a retenção de dados. Pode remover os dados antigos da telemetria eliminando as mesas de armazenamento.

Este tópico discute como configurar e consumir a telemetria DA AMS.

## <a name="configuring-telemetry"></a>Configuração da telemetria

Pode configurar a telemetria num nível de granularidade de nível de componente. Existem dois níveis de detalhe "Normal" e "Verbose". Atualmente, ambos os níveis devolvem a mesma informação. Recomenda-se a utilização "Normal. 

Os seguintes tópicos mostram como permitir a telemetria:

[Habilitar a telemetria com .NET](media-services-dotnet-telemetry.md) 

[Habilitar a telemetria com REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Consumir informação sobre telemetria

A telemetria está escrita numa Tabela de Armazenamento Azure na conta de armazenamento que especificou quando configurava a telemetria para a conta de Media Services. Esta secção descreve as tabelas de armazenamento para as métricas.

Pode consumir dados de telemetria de uma das seguintes formas:

- Leia os dados diretamente do Armazenamento de Mesa Azure (por exemplo, utilizando o SDK de Armazenamento). Para obter a descrição das tabelas de armazenamento de telemetria, consulte a informação de **telemetria consumista** [neste](https://msdn.microsoft.com/library/mt742089.aspx) tópico.

Ou

- Utilize o suporte nos Media Services .NET SDK para ler dados de armazenamento, conforme descrito [neste](media-services-dotnet-telemetry.md) tópico. 


O esquema de telemetria descrito abaixo foi concebido para dar um bom desempenho dentro dos limites do Armazenamento de Mesa Azure:

- Os dados são divididos por ID de conta e id de serviço para permitir que a telemetria de cada serviço seja consultada de forma independente.
- As divisórias contêm a data para dar um limite superior razoável ao tamanho da divisória.
- As teclas de fila estão em marcha atrás para permitir que os mais recentes artigos de telemetria sejam consultados por um determinado serviço.

Isto deve permitir que muitas das consultas comuns sejam eficientes:

- Transferência paralela e independente de dados para serviços separados.
- Recuperando todos os dados para um determinado serviço numa gama de datas.
- Recuperando os dados mais recentes para um serviço.

### <a name="telemetry-table-storage-output-schema"></a>Esquema de saída de armazenamento de mesa de telemetria

Os dados da telemetria são armazenados em conjunto numa tabela, "TelemettryMetrics20160321" onde "20160321" é data da tabela criada. O sistema de telemetria cria uma tabela separada para cada novo dia baseado às 00:00 UTC. A tabela é utilizada para armazenar valores recorrentes, como a ingestão de bitrate dentro de uma dada janela de tempo, bytes enviados, etc. 

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|{ID da conta ID}_{entidade ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>O ID da conta está incluído na chave de partição para simplificar fluxos de trabalho onde várias contas de Serviços de Media estão escrevendo para a mesma conta de armazenamento.
RowKey|{segundos para a meia-noite}_{valor aleatório}|01688_00199<br/><br/>A tecla da linha começa com o número de segundos à meia-noite para permitir consultas de estilo top n dentro de uma divisória. Para mais informações, consulte [este](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artigo. 
Carimbo de data/hora|Date/Time|Carimbo de tempo automático da mesa Azure 2016-09-09T22:43:42.241Z
Tipo|O tipo de entidade que fornece dados de telemetria|Canal/StreamingEndpoint/Archive<br/><br/>O tipo de evento é apenas um valor de corda.
Nome|O nome do evento de telemetria|ChannelHeartbeat/StreamingEndpointRequestLog
Tempo observado|O tempo em que ocorreu o evento de telemetria (UTC)|2016-09-09T22:42:36.924Z<br/><br/>O tempo observado é fornecido pela entidade que envia a telemetria (por exemplo, um canal). Pode haver problemas de sincronização do tempo entre componentes para que este valor seja aproximado
ServiceID|{ID de serviço}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Propriedades específicas da entidade|Conforme definido pelo evento|StreamName: stream1, Bitrate 10123, ...<br/><br/>As propriedades restantes são definidas para o tipo de evento dado. O conteúdo da tabela Azure é um par de valor chave.  (isto é, diferentes linhas na tabela têm diferentes conjuntos de propriedades).

### <a name="entity-specific-schema"></a>Esquema específico da entidade

Existem três tipos de entradas de dados telemétricos específicas da entidade cada uma empurrada com a seguinte frequência:

- Pontos finais de streaming: A cada 30 segundos
- Canais ao vivo: A cada minuto
- Arquivo ao vivo: A cada minuto

**Ponto Final de Transmissão em Fluxo**

Propriedade|Valor|Exemplos
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Carimbo de tempo automático da Mesa Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|StreamingEndpoint
Nome|Nome|StreamingEndpointRequestLog
Tempo observado|Tempo observado|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
NomedeAnfitrião|Nome de anfitrião do ponto final|builddemoserver.origin.mediaservices.windows.net
Código de Estado|Estado de Registos HTTP|200
Código de Resultados|Detalhe do código de resultados|S_OK
Contagem de Pedidos|Pedido total na agregação|3
BytesSent|Bytes agregados enviados|2987358
ServidorLatency|Latência média do servidor (incluindo armazenamento)|129
E2ELatency|Latência média de ponta a ponta|250

**Canal ao vivo**

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Carimbo de tempo automático da mesa Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|Canal
Nome|Nome|CanalHeartbeat
Tempo observado|Tempo observado|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Tipo de vídeo/áudio/texto de faixa|vídeo/áudio
TrackName|Nome da pista|vídeo/audio_1
Bitrate|Bitrate de pista|785000
CustomAtributos||   
IncomingBitrate|Bitrate de entrada real|784548
SobreposiçãoCount|Sobreposição na ingéeca|0
DescontinuidadeCount|Descontinuidade para pista|0
Sequete do Tempo Último|Última marca de tempo de dados ingerida|1800488800
Contagem não crescente|Contagem de fragmentos descartados devido a carimbo temporal não crescente|2
KeyFrames não alinhados|Quer tivéssemos recebido fragmentos (em níveis de qualidade) onde os quadros-chave não estavam alinhados |Verdadeiro
Tempo de apresentação não alinhado|Se recebemos fragmentos (em níveis/faixas de qualidade) onde o tempo de apresentação não está alinhado|Verdadeiro
Bitrate Inesperado|Verdade, se calculado/real bitrate para a faixa de áudio/vídeo > 40.000 bps e IncomingBitrate == 0 OR IncomingBitrate e realBitrate diferem em 50% |Verdadeiro
Bom estado de funcionamento|Verdade, se <br/>sobreposiçãoCount, <br/>DescontinuidadeCount, <br/>Não Aumentar a Contagem, <br/>KeyFrames desalinhados, <br/>Tempo de apresentação não alinhado, <br/>Bitrate Inesperado<br/> são todos 0|Verdadeiro<br/><br/>Saudável é uma função composta que retorna falsa quando qualquer uma das seguintes condições se mantém:<br/><br/>- SobreposiçãoCount > 0<br/>- DescontinuidadeCount > 0<br/>- Contagem não crescente > 0<br/>- KeyFrames não alinhados == Verdadeiro<br/>- Apresentação não alinhadaTempo == Verdadeiro<br/>- InesperadoBitrate == Verdadeiro

**Arquivo ao vivo**

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Carimbo de tempo automático da mesa Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|Arquivo
Nome|Nome|ArchiveHeartbeat
Tempo observado|Tempo observado|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Manifesto Nome|Url do programa|activo-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism.ism
TrackName|Nome da pista|audio_1
TrackType|Tipo de pista|Áudio/vídeo
Atribuir personalizado|Cadeia hexaque que diferencia entre diferentes faixas com o mesmo nome e bitrate (ângulo multi câmara)|
Bitrate|Bitrate de pista|785000
Bom estado de funcionamento|Verdade, se FragmentDiscarddCount == 0 && ArchiveAcquisitionError == Falso|Verdade (estes dois valores não estão presentes na métrica, mas estão presentes no evento de origem)<br/><br/>Saudável é uma função composta que retorna falsa quando qualquer uma das seguintes condições se mantém:<br/><br/>- Contagem fragmentada descartada > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>General Q&A

### <a name="how-to-consume-metrics-data"></a>Como consumir dados métricos?

Os dados das métricas são armazenados como uma série de Tabelas Azure na conta de armazenamento do cliente. Estes dados podem ser consumidos utilizando as seguintes ferramentas:

- AMS SDK
- Microsoft Azure Storage Explorer (suporta exportação para formato de valor separado de vírem e processado no Excel)
- API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Como encontrar um consumo médio de largura de banda?

O consumo médio de largura de banda é a média de BytesSent ao longo de um período de tempo.

### <a name="how-to-define-streaming-unit-count"></a>Como definir a contagem de unidades de streaming?

A contagem de unidades de streaming pode ser definida como a potência máxima dos pontos finais de streaming do serviço divididos pela potência máxima de um ponto final de streaming. A entrada máxima utilizável de um ponto final de streaming é de 160 Mbps.
Por exemplo, suponha que a entrada máxima do serviço de um cliente seja de 40 MBps (o valor máximo de BytesSent ao longo de um período de tempo). Em seguida, a contagem da unidade de streaming é igual a (40 MBps)*(8 bits/byte)/(160 Mbps) = 2 unidades de streaming.

### <a name="how-to-find-average-requestssecond"></a>Como encontrar pedidos médios/segundo?

Para encontrar o número médio de pedidos/segundo, calcule o número médio de pedidos (RequestCount) ao longo de um período de tempo.

### <a name="how-to-define-channel-health"></a>Como definir a saúde do canal?

A saúde do canal pode ser definida como uma função booleancomposta compósita de tal forma que é falsa quando qualquer uma das seguintes condições se mantém:

- SobreposiçãoCount > 0
- DescontinuidadeCount > 0
- Contagem não crescente > 0
- KeyFrames não alinhados == Verdadeiro 
- Apresentação não alinhadaTempo == Verdadeiro 
- Bitrate Inesperado == Verdadeiro


### <a name="how-to-detect-discontinuities"></a>Como detetar descontinuidades?

Para detetar descontinuidades, encontre todas as entradas de dados do Canal onde o Conde de Siscontinuidade > 0. O carimbo de tempo observado correspondente indica os tempos em que ocorreram as descontinuidades.

### <a name="how-to-detect-timestamp-overlaps"></a>Como detetar sobreposições de carimbos de tempo?

Para detetar sobreposições de carimbos de tempo, encontre todas as entradas de dados do Canal onde o Sobreposição Count > 0. O carimbo de tempo observado correspondente indica os tempos em que ocorreu a marca de tempo.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Como encontrar falhas e razões de pedido de streaming?

Para encontrar falhas e razões de pedido de streaming, encontre todas as entradas de dados do Streaming Endpoint onde o ResultCode não seja igual a S_OK. O campo StatusCode correspondente indica o motivo da falha do pedido.

### <a name="how-to-consume-data-with-external-tools"></a>Como consumir dados com ferramentas externas?

Os dados telemétricos podem ser processados e visualizados com as seguintes ferramentas:

- Power BI
- Application Insights
- Monitor Azure (antiga Caixa de Sapatos)
- Painel de instrumentos ao vivo da AMS
- Portal Azure (lançamento pendente)

### <a name="how-to-manage-data-retention"></a>Como gerir a retenção de dados?

O sistema de telemetria não fornece gestão de retenção de dados ou eliminação automática de registos antigos. Assim, é necessário gerir e apagar registos antigos manualmente da mesa de armazenamento. Pode consultar o SDK de armazenamento para saber como fazê-lo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
