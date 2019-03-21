---
title: Telemetria dos serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral de telemetria de serviços de multimédia do Azure.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5f33249496c9a4fc8a094e64f15c8d95da69194b
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294880"
---
# <a name="azure-media-services-telemetry"></a>Telemetria de serviços de multimédia do Azure  

Serviços de multimédia do Azure (AMS) permite-lhe aceder aos dados de telemetria/métricas para seus serviços. A versão atual do AMS permite-lhe recolher dados telemétricos para em direto **canal**, **StreamingEndpoint**e ao vivo **arquivo** entidades. 

Telemetria é escrita para uma tabela de armazenamento numa conta de armazenamento do Azure que especificar (normalmente, usaria a conta de armazenamento à conta do AMS). 

O sistema de telemetria não gere a retenção de dados. Pode remover os dados de telemetria antigos ao eliminar as tabelas de armazenamento.

Este tópico descreve como configurar e consumir a telemetria de AMS.

## <a name="configuring-telemetry"></a>Configurar a telemetria

Pode configurar a telemetria sobre uma granularidade de nível de componente. Existem dois níveis de detalhe "Normal" e "Verbose". Atualmente, ambos os níveis de devolvem as mesmas informações. É recomendado que utilize "Normal. 

Os tópicos seguintes mostram como ativar a telemetria:

[Ativar a telemetria com .NET](media-services-dotnet-telemetry.md) 

[Ativar a telemetria com REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Consumo de informações de telemetria

Telemetria é escrita para uma tabela de armazenamento do Azure na conta de armazenamento que especificou quando configurou a telemetria para a conta de Media Services. Esta secção descreve as tabelas de armazenamento para as métricas.

Pode consumir dados de telemetria de uma das seguintes formas:

- Ler dados diretamente a partir do armazenamento de tabelas do Azure (por exemplo, utilizando o SDK de armazenamento). Para a descrição das tabelas de armazenamento de telemetria, consulte a **consumir informações telemétricas** na [isso](https://msdn.microsoft.com/library/mt742089.aspx) tópico.

Ou

- Utilizar o suporte do SDK do .NET de serviços de suporte de dados para ler os dados de armazenamento, conforme descrito em [isso](media-services-dotnet-telemetry.md) tópico. 


O esquema de telemetria descrito abaixo é projetado para dar o bom desempenho dentro dos limites de armazenamento de tabelas do Azure:

- Os dados são particionados por conta de ID e ID de serviço para permitir que a telemetria a partir de cada serviço ser consultadas de forma independente.
- Partições contêm a data para dar um limite superior razoável sobre o tamanho da partição.
- Chaves de linha estão em ordem inversa de tempo para permitir que os itens de telemetria mais recentes para ser consultada para um determinado serviço.

Isso deve permitir que muitas das consultas comuns para ser eficiente:

- Baixar o paralela, independente de dados para serviços separados.
- Obter todos os dados para um determinado serviço num intervalo de datas.
- A obter os dados mais recentes para um serviço.

### <a name="telemetry-table-storage-output-schema"></a>Esquema de saída de armazenamento de tabela de telemetria

Dados de telemetria são armazenados no agregado numa tabela, "TelemetryMetrics20160321" onde "20160321" é a data de tabela criada. Sistema de telemetria cria uma tabela separada para cada dia de novo com base 00:00 UTC. A tabela é utilizada para armazenar valores recorrentes, tais como ingestão de velocidade de transmissão dentro de uma determinada janela de tempo, bytes enviados, etc. 

Propriedade|Value|Exemplos/notas
---|---|---
PartitionKey|{account ID}_{entity ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>O ID da conta está incluído na chave de partição para simplificar os fluxos de trabalho em que várias contas de serviços de multimédia estiver a escrever para a mesma conta de armazenamento.
RowKey|{seconds meia-noite} _ {valor aleatório}|01688_00199<br/><br/>A chave de linha começa com o número de segundos a meia-noite para permitir consultas de estilo de n principais dentro de uma partição. Para obter mais informações, veja [este](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artigo. 
Carimbo de data/hora|Data/Hora|Timestamp da tabela do Azure 2016 de auto-09-09T22:43:42.241Z
Type|O tipo da entidade a fornecer os dados de telemetria|Channel/StreamingEndpoint/Archive<br/><br/>Tipo de evento é apenas um valor de cadeia de caracteres.
Name|O nome do evento de telemetria|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|O tempo em que ocorreu o evento de telemetria (UTC)|2016-09-09T22:42:36.924Z<br/><br/>O tempo observado é fornecido pela entidade enviar a telemetria (por exemplo um canal). Pode haver problemas de sincronização entre os componentes, para que este valor é obter uma estimativa de tempo
ServiceID|{serviço ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Propriedades específicas de entidade|Conforme definido pelo evento|StreamName: stream1, velocidade de transmissão 10123,...<br/><br/>As propriedades restantes são definidas para o tipo de evento específico. Conteúdo da tabela do Azure é pares chave-valor.  (ou seja, diferentes linhas da tabela têm conjuntos diferentes de propriedades).

### <a name="entity-specific-schema"></a>Esquema específico de entidade

Existem três tipos de entradas de dados de telemetria de específicas de entidades cada instalados com a frequência seguinte:

- Pontos finais de transmissão em fluxo: Cada 30 segundos
- Canais em direto: Cada minuto
- Arquivo Live: Cada minuto

**Ponto final de transmissão em fluxo**

Propriedade|Value|Exemplos
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Auto timestamp a partir de 2016 de tabela do Azure-09-09T22:43:42.241Z
Type|Type|StreamingEndpoint
Name|Name|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
nome de anfitrião|Nome de anfitrião do ponto final|builddemoserver.origin.mediaservices.windows.net
StatusCode|Estado HTTP de registos|200
ResultCode|Detalhes de código de resultado|S_OK
RequestCount|Pedido de total na agregação|3
BytesSent|Agregados bytes enviados|2987358
ServerLatency|Latência média de servidor (incluindo o armazenamento)|129
E2ELatency|Latência de ponto-a-ponto média|250

**Canais em direto**

Propriedade|Value|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Timestamp da tabela do Azure 2016 de auto-09-09T22:43:42.241Z
Type|Type|Canal
Name|Name|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Tipo de vídeo/áudio/text do controle|vídeo/áudio
TrackName|Nome da faixa|vídeo/audio_1
Velocidade de transmissão|Velocidade de transmissão do Roteiro|785000
CustomAttributes||   
IncomingBitrate|Velocidade de transmissão de entrada real|784548
OverlapCount|Se sobrepõe de ingestão|0
DiscontinuityCount|Descontinuidade Roteiro|0
LastTimestamp|Últimos dados ingeridos timestamp|1800488800
NonincreasingCount|Contagem de fragmentos rejeitados, devido à nerostoucí timestamp|2
UnalignedKeyFrames|Se recebemos fragment(s) (nos níveis de qualidade) onde chave quadros não alinhados |Verdadeiro
UnalignedPresentationTime|Se recebemos fragment(s) (em níveis de qualidade/faixas) em que o tempo de apresentação não está alinhado|Verdadeiro
UnexpectedBitrate|TRUE, se calculado/real velocidade de transmissão de áudio/vídeo controlar > 40.000 bps e IncomingBitrate = = 0 ou IncomingBitrate e actualBitrate diferirem em 50% |Verdadeiro
Bom estado de funcionamento|Se VERDADEIRO, <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> são todos os 0|Verdadeiro<br/><br/>Bom estado de funcionamento é uma função composta, que retorna falsa quando qualquer uma das seguintes condições conter:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>- NonincreasingCount > 0<br/>- UnalignedKeyFrames == True<br/>- UnalignedPresentationTime == True<br/>- UnexpectedBitrate == True

**Arquivo Live**

Propriedade|Value|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Timestamp da tabela do Azure 2016 de auto-09-09T22:43:42.241Z
Type|Type|Arquivo
Name|Name|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Url de programa|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|Nome da faixa|audio_1
TrackType|Tipo de faixa|Áudio/vídeo
Atribut CustomAttribute|Hex a cadeia de caracteres que diferencia entre diferente controlar com o mesmo nome e a velocidade de transmissão (ângulo da câmera de transmissões)|
Velocidade de transmissão|Velocidade de transmissão do Roteiro|785000
Bom estado de funcionamento|TRUE, se FragmentDiscardedCount = = 0 & & ArchiveAcquisitionError = = False|VERDADEIRO (esses dois valores não estão presentes na métrica mas eles estão presentes no evento de origem)<br/><br/>Bom estado de funcionamento é uma função composta, que retorna falsa quando qualquer uma das seguintes condições conter:<br/><br/>-FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>Gerais e respostas

### <a name="how-to-consume-metrics-data"></a>Como consumir dados de métricas?

Dados de métricas são armazenados como uma série de tabelas do Azure na conta de armazenamento do cliente. Estes dados podem ser consumidos usando as ferramentas seguintes:

- SDK DO AMS
- Explorador de armazenamento do Microsoft Azure (dá suporte à exportação para o formato de valores separados por vírgulas e processado no Excel)
- API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Como encontrar o consumo de largura de banda média?

O consumo de largura de banda média é a média de BytesSent ao longo de um período de tempo.

### <a name="how-to-define-streaming-unit-count"></a>Como definir a contagem de unidades de transmissão em fluxo?

A contagem de unidades de transmissão em fluxo pode ser definida como a taxa de transferência de pico de pontos finais de transmissão o serviço dividida pela taxa de transferência de pico de um ponto final de transmissão em fluxo. O débito de utilizável de pico de um ponto final de transmissão em fluxo é de 160 Mbps.
Por exemplo, suponha que a taxa de transferência de pico do serviço de um cliente é 40 MBps (o valor máximo de BytesSent ao longo de um período de tempo). Em seguida, a contagem de unidades de transmissão em fluxo é igual a (40 MBps) * (8 bits/byte) /(160 Mbps) = 2 unidades de transmissão em fluxo.

### <a name="how-to-find-average-requestssecond"></a>Como determinar média de pedidos por segundo?

Para localizar o número médio de pedidos por segundo, compute o número médio de pedidos (RequestCount) ao longo de um período de tempo.

### <a name="how-to-define-channel-health"></a>Como definir o estado de funcionamento do canal?

Estado de funcionamento do canal pode ser definido como uma função booleana de composição, de modo a que for falsa quando qualquer uma das seguintes condições conter:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Como detectar discontinuities?

Para detectar discontinuities, encontrar todas as entradas de dados de canal onde DiscontinuityCount > 0. O carimbo de hora ObservedTime correspondente indica as horas em que ocorreu os discontinuities.

### <a name="how-to-detect-timestamp-overlaps"></a>Como detectar timestamp sobrepõe-se?

Para detectar timestamp sobreposições, encontrar todas as entradas de dados de canal onde OverlapCount > 0. O carimbo de hora ObservedTime correspondente indica as horas em que o carimbo de hora sobrepõe-se ocorreu.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Como encontrar motivos e falhas de pedido de transmissão em fluxo?

Para localizar falhas de pedido de transmissão em fluxo e motivos, encontre todas as entradas de dados do ponto final de transmissão em fluxo em que não é igual a S_OK ResultCode. O campo correspondente de StatusCode indica o motivo da falha do pedido.

### <a name="how-to-consume-data-with-external-tools"></a>Como consumir dados com ferramentas externas?

Dados de telemetria podem ser processados e visualizados com as seguintes ferramentas:

- PowerBI
- Application Insights
- O Azure Monitor (anteriormente conhecido como Shoebox)
- Dashboard em direto do AMS
- Portal do Azure (pendente lançamento)

### <a name="how-to-manage-data-retention"></a>Como gerir a retenção de dados?

O sistema de telemetria não fornece gestão de retenção de dados ou eliminação automática antigos registos. Portanto, precisa gerir e eliminar registos antigos manualmente a partir da tabela de armazenamento. Pode consultar o SDK para saber como fazê-lo de armazenamento.

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
