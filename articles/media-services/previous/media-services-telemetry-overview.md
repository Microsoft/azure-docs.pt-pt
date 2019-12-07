---
title: Telemetria dos serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral da telemetria de Serviços de Mídia do Microsoft Azure.
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
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895782"
---
# <a name="azure-media-services-telemetry"></a>Telemetria dos serviços de mídia do Azure  


> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

O AMS (serviços de mídia do Azure) permite que você acesse dados de telemetria/métricas para seus serviços. A versão atual do AMS permite que você colete dados de telemetria para entidades de Live **Channel**, **StreamingEndpoint**e de **arquivamento** ao vivo. 

A telemetria é gravada em uma tabela de armazenamento em uma conta de armazenamento do Azure que você especifica (normalmente, você usaria a conta de armazenamento associada à sua conta AMS). 

O sistema de telemetria não gerencia a retenção de dados. Você pode remover os dados de telemetria antigos excluindo as tabelas de armazenamento.

Este tópico discute como configurar e consumir a telemetria do AMS.

## <a name="configuring-telemetry"></a>Configurando telemetria

Você pode configurar a telemetria em uma granularidade de nível de componente. Há dois níveis de detalhes "normal" e "detalhado". Atualmente, os dois níveis retornam as mesmas informações. É recomendável usar "normal. 

Os tópicos a seguir mostram como habilitar a telemetria:

[Habilitando a telemetria com o .NET](media-services-dotnet-telemetry.md) 

[Habilitando telemetria com REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Consumindo informações de telemetria

A telemetria é gravada em uma tabela de armazenamento do Azure na conta de armazenamento que você especificou quando configurou a telemetria para a conta dos serviços de mídia. Esta seção descreve as tabelas de armazenamento para as métricas.

Você pode consumir dados de telemetria de uma das seguintes maneiras:

- Ler dados diretamente do armazenamento de tabelas do Azure (por exemplo, usando o SDK de armazenamento). Para obter a descrição das tabelas de armazenamento de telemetria, consulte o **consumo de informações de telemetria** [neste tópico.](https://msdn.microsoft.com/library/mt742089.aspx)

Ou

- Use o suporte no SDK do .NET dos serviços de mídia para ler dados de armazenamento, conforme descrito [neste tópico.](media-services-dotnet-telemetry.md) 


O esquema de telemetria descrito abaixo foi projetado para fornecer um bom desempenho dentro dos limites do armazenamento de tabelas do Azure:

- Os dados são particionados por ID de conta e ID de serviço para permitir que a telemetria de cada serviço seja consultada de forma independente.
- As partições contêm a data para fornecer um limite superior razoável no tamanho da partição.
- As chaves de linha estão em ordem de tempo inversa para permitir que os itens de telemetria mais recentes sejam consultados para um determinado serviço.

Isso deve permitir que muitas das consultas comuns sejam eficientes:

- Download paralelo e independente de dados para serviços separados.
- Recuperando todos os dados de um determinado serviço em um intervalo de datas.
- Recuperando os dados mais recentes de um serviço.

### <a name="telemetry-table-storage-output-schema"></a>Esquema de saída de armazenamento de tabela de telemetria

Os dados de telemetria são armazenados em agregação em uma tabela, "TelemetryMetrics20160321", onde "20160321" é a data da tabela criada. O sistema de telemetria cria uma tabela separada para cada novo dia com base em 00:00 UTC. A tabela é usada para armazenar valores recorrentes, como a taxa de bits de ingestão em um determinado período de tempo, bytes enviados, etc. 

Propriedade|Valor|Exemplos/observações
---|---|---
PartitionKey|{ID da conta} _ {ID da entidade}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>A ID da conta é incluída na chave de partição para simplificar os fluxos de trabalho em que várias contas de serviços de mídia estão gravando na mesma conta de armazenamento.
RowKey|{segundos à meia-noite} _ {valor aleatório}|01688_00199<br/><br/>A chave de linha começa com o número de segundos à meia-noite para permitir as primeiras consultas de estilo n em uma partição. Para obter mais informações, veja [este](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artigo. 
Carimbo de data/hora|Data/hora|Carimbo de data/hora automático da tabela do Azure 2016-09-09T22:43:42.241 Z
Tipo|O tipo da entidade que fornece dados de telemetria|Canal/StreamingEndpoint/arquivo morto<br/><br/>O tipo de evento é apenas um valor de cadeia de caracteres.
Nome|O nome do evento de telemetria|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|A hora em que o evento de telemetria ocorreu (UTC)|2016-09-09T22:42:36.924 Z<br/><br/>O tempo observado é fornecido pela entidade enviando a telemetria (por exemplo, um canal). Pode haver problemas de sincronização de horário entre os componentes para que esse valor seja aproximado
ServiceID|{ID do serviço}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Propriedades específicas da entidade|Conforme definido pelo evento|StreamName: STREAM1, taxa de bits 10123,...<br/><br/>As propriedades restantes são definidas para o tipo de evento fornecido. O conteúdo da tabela do Azure é par chave-valor.  (ou seja, linhas diferentes na tabela têm conjuntos diferentes de propriedades).

### <a name="entity-specific-schema"></a>Esquema específico da entidade

Há três tipos de entradas de dados de telemétricas específicas de entidade que são empurradas com a seguinte frequência:

- Pontos de extremidade de streaming: a cada 30 segundos
- Canais ao vivo: a cada minuto
- Arquivamento dinâmico: a cada minuto

**Ponto Final de Transmissão em Fluxo**

Propriedade|Valor|Exemplos
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Carimbo de data/hora automático da tabela do Azure 2016-09-09T22:43:42.241 Z
Tipo|Tipo|StreamingEndpoint
Nome|Nome|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|ID de serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
nome de anfitrião|Nome do host do ponto de extremidade|builddemoserver.origin.mediaservices.windows.net
StatusCode|Registra o status HTTP|200
ResultCode|Detalhe do código de resultado|S_OK
RequestCount|Total de solicitações na agregação|3
BytesSent|Bytes agregados enviados|2987358
ServerLatency|Latência média do servidor (incluindo armazenamento)|129
E2ELatency|Latência média de ponta a ponta|250

**Canal ao vivo**

Propriedade|Valor|Exemplos/observações
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Carimbo de data/hora automático da tabela do Azure 2016-09-09T22:43:42.241 Z
Tipo|Tipo|Canal
Nome|Nome|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|ID de serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Tipo de faixa de vídeo/áudio/texto|vídeo/áudio
TrackName|Nome da faixa|vídeo/audio_1
Velocidade de transmissão|Rastrear taxa de bits|785000
CustomAttributes||   
IncomingBitrate|Taxa de bits de entrada real|784548
OverlapCount|Sobreposição na ingestão|0
DiscontinuityCount|Descontinuidade para acompanhamento|0
LastTimestamp|Último carimbo de data/hora de dados ingeridos|1800488800
NonincreasingCount|Contagem de fragmentos descartados devido ao carimbo de data/hora sem aumento|2
UnalignedKeyFrames|Se recebemos fragmentos (nos níveis de qualidade) em que os quadros-chave não estão alinhados |Verdadeiro
UnalignedPresentationTime|Se recebemos fragmentos (em faixas/níveis de qualidade) em que o tempo de apresentação não está alinhado|Verdadeiro
UnexpectedBitrate|True, se a taxa de bits calculada/real para a faixa de áudio/vídeo > 40.000 bps e IncomingBitrate = = 0 ou IncomingBitrate e actualBitrate for diferente de 50% |Verdadeiro
Bom estado de funcionamento|True, se <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> são todos 0|Verdadeiro<br/><br/>Íntegro é uma função composta que retorna falso quando qualquer uma das seguintes condições mantém:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames = = true<br/>-UnalignedPresentationTime = = true<br/>-UnexpectedBitrate = = true

**Arquivamento dinâmico**

Propriedade|Valor|Exemplos/observações
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Carimbo de data/hora|Carimbo de data/hora|Carimbo de data/hora automático da tabela do Azure 2016-09-09T22:43:42.241 Z
Tipo|Tipo|Arquivo
Nome|Nome|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|ID de serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|URL do programa|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|Nome da faixa|audio_1
TrackType|Tipo da faixa|Áudio/vídeo
CustomAttribute|Cadeia de caracteres hexadecimal que diferencia entre diferentes faixas com o mesmo nome e taxa de bits (ângulo de várias câmeras)|
Velocidade de transmissão|Rastrear taxa de bits|785000
Bom estado de funcionamento|True, se FragmentDiscardedCount = = 0 & & ArchiveAcquisitionError = = false|True (esses dois valores não estão presentes na métrica, mas estão presentes no evento de origem)<br/><br/>Íntegro é uma função composta que retorna falso quando qualquer uma das seguintes condições mantém:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError = = true

## <a name="general-qa"></a>P geral & um

### <a name="how-to-consume-metrics-data"></a>Como consumir dados de métricas?

Os dados de métricas são armazenados como uma série de tabelas do Azure na conta de armazenamento do cliente. Esses dados podem ser consumidos usando as seguintes ferramentas:

- SDK DO AMS
- Gerenciador de Armazenamento do Microsoft Azure (dá suporte à exportação para o formato de valor separado por vírgula e processada no Excel)
- API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Como encontrar o consumo médio de largura de banda?

O consumo médio de largura de banda é a média de BytesSent em um período de tempo.

### <a name="how-to-define-streaming-unit-count"></a>Como definir a contagem de unidades de streaming?

A contagem de unidades de streaming pode ser definida como a taxa de transferência de pico dos pontos de extremidade de streaming do serviço divididos pela taxa de transferência de pico de um ponto de extremidades de streaming. O pico de taxa de transferência utilizável de um ponto de extremidade de streaming é de 160 Mbps.
Por exemplo, suponha que a taxa de transferência de pico do serviço de um cliente seja 40 MBps (o valor máximo de BytesSent em um período de tempo). Em seguida, a contagem de unidades de streaming é igual a (40 MBps) * (8 bits/byte)/(160 Mbps) = 2 unidades de streaming.

### <a name="how-to-find-average-requestssecond"></a>Como encontrar média de solicitações/segundo?

Para localizar o número médio de solicitações/segundo, calcule o número médio de solicitações (RequestCount) em um período de tempo.

### <a name="how-to-define-channel-health"></a>Como definir a integridade do canal?

A integridade do canal pode ser definida como uma função booliana composta, de modo que ela seja falsa quando qualquer uma das seguintes condições mantiver:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate = = true


### <a name="how-to-detect-discontinuities"></a>Como detectar o descontinuidades?

Para detectar o descontinuidades, localize todas as entradas de dados de canal em que DiscontinuityCount > 0. O carimbo de data/hora Observad correspondente indica as horas em que o descontinuidades ocorreu.

### <a name="how-to-detect-timestamp-overlaps"></a>Como detectar sobreposições de carimbo de data/hora?

Para detectar sobreposições de carimbo de data/hora, localize todas as entradas de dados de canal em que OverlapCount > 0. O carimbo de data/hora Observadotime indica as horas em que as sobreposições de carimbo de data/hora ocorreram.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Como encontrar falhas de solicitação de streaming e motivos?

Para encontrar falhas de solicitação de streaming e motivos, localize todas as entradas de dados de ponto de extremidade de streaming em que ResultCode não é igual a S_OK. O campo StatusCode correspondente indica o motivo da falha de solicitação.

### <a name="how-to-consume-data-with-external-tools"></a>Como consumir dados com ferramentas externas?

Os dados de telemétricas podem ser processados e visualizados com as seguintes ferramentas:

- PowerBI
- Estatísticas das Aplicações
- Azure Monitor (anteriormente sapatos)
- Painel dinâmico do AMS
- Portal do Azure (versão pendente)

### <a name="how-to-manage-data-retention"></a>Como gerenciar a retenção de dados?

O sistema de telemetria não fornece gerenciamento de retenção de dados ou exclusão automática de registros antigos. Portanto, você precisa gerenciar e excluir registros antigos manualmente da tabela de armazenamento. Você pode consultar o SDK de armazenamento para saber como fazê-lo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
