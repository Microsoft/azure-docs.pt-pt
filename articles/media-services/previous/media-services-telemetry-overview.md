---
title: Telemetria Azure Media Services Telemetria | Microsoft Docs
description: Este artigo fornece uma visão geral da telemetria da Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: b17b5901248056f6000710fa25d2ea1e9df2e2a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009099"
---
# <a name="azure-media-services-telemetry"></a>Telemetria Azure Media Services  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

A Azure Media Services (AMS) permite-lhe aceder a dados de telemetria/métricas para os seus serviços. A versão atual da AMS permite-lhe recolher dados de telemetria para o **Canal** live, **StreamingEndpoint** e entidades live **Archive.** 

A telemetria é escrita para uma tabela de armazenamento numa conta de Armazenamento Azure que especifica (normalmente, utilizaria a conta de armazenamento associada à sua conta AMS). 

O sistema de telemetria não gere a retenção de dados. Pode remover os dados antigos da telemetria eliminando as tabelas de armazenamento.

Este tópico discute como configurar e consumir a telemetria AMS.

## <a name="configuring-telemetry"></a>Configurar a telemetria

Pode configurar a telemetria numa granularidade de nível de componente. Há dois níveis de detalhe "Normal" e "Verbose". Atualmente, ambos os níveis devolvem a mesma informação. Recomenda-se a utilização "Normal. 

Os seguintes tópicos mostram como permitir a telemetria:

[Habilitação de telemetria com .NET](media-services-dotnet-telemetry.md) 

[Habilitar a telemetria com REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Informação de telemetria consumista

A telemetria é escrita para uma Tabela de Armazenamento Azure na conta de armazenamento que especificou quando configurava a telemetria para a conta dos Serviços de Comunicação Social. Esta secção descreve as tabelas de armazenamento para as métricas.

Pode consumir dados de telemetria de uma das seguintes formas:

- Leia os dados diretamente do Azure Table Storage (por exemplo, utilizando o SDK de armazenamento). Para a descrição das tabelas de armazenamento de telemetria, consulte as **informações de telemetria consumista** [neste](/previous-versions/azure/mt742089(v=azure.100)) tópico.

Ou

- Utilize o suporte nos Serviços de Comunicação Social .NET SDK para ler dados de armazenamento, conforme descrito [neste](media-services-dotnet-telemetry.md) tópico. 


O esquema de telemetria descrito abaixo destina-se a dar um bom desempenho dentro dos limites do Armazenamento da Mesa Azure:

- Os dados são divididos pelo ID da conta e pelo ID do serviço para permitir que a telemetria de cada serviço seja consultada de forma independente.
- As partições contêm a data para dar um limite superior razoável no tamanho da partição.
- As teclas de linha estão em ordem inversa para permitir que os itens de telemetria mais recentes sejam consultados para um determinado serviço.

Isto deve permitir que muitas das consultas comuns sejam eficientes:

- Descarregamento paralelo e independente de dados para serviços separados.
- Recuperar todos os dados para um determinado serviço num intervalo de datas.
- Recuperar os dados mais recentes para um serviço.

### <a name="telemetry-table-storage-output-schema"></a>Esquema de saída de armazenamento de mesa de telemetria

Os dados da telemetria são armazenados em conjunto numa tabela, "TelemetriaMétrica20160321" onde "20160321" é a data da tabela criada. O sistema de telemetria cria uma tabela separada para cada novo dia baseado em 00:00 UTC. A tabela é usada para armazenar valores recorrentes, tais como ingestão de bitrate dentro de uma determinada janela de tempo, bytes enviados, etc. 

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|{iD da conta}_{iD da entidade}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>O ID da conta está incluído na chave de partição para simplificar os fluxos de trabalho onde várias contas de Serviços de Mídia estão escrevendo para a mesma conta de armazenamento.
RowKey|{segundos para meia-noite}_{valor aleatório}|01688_00199<br/><br/>A tecla de linha começa com o número de segundos à meia-noite para permitir consultas de estilo top n dentro de uma partição. Para mais informações, consulte [este](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artigo. 
CarimboDeDataEHora|Data/Hora|Marca de tempo automática da tabela Azure 2016-09-09T22:43:42.241Z
Tipo|O tipo de entidade que fornece dados de telemetria|Canal/StreamingEndpoint/Arquivo<br/><br/>O tipo de evento é apenas um valor de corda.
Name|O nome do evento de telemetria|ChannelHeartbeat/StreamingEndpointRequestLog
Tempo observado|A hora em que ocorreu o evento de telemetria (UTC)|2016-09-09T22:42:36.924Z<br/><br/>O tempo observado é fornecido pela entidade que envia a telemetria (por exemplo, um canal). Pode haver problemas de sincronização de tempo entre componentes, pelo que este valor é aproximado
ServiceID|{iD serviço}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Propriedades específicas da entidade|Conforme definido pelo evento|StreamName: stream1, Bitrate 10123, ...<br/><br/>As propriedades restantes são definidas para o tipo de evento dado. O conteúdo da tabela Azure é par de valores-chave.  (isto é, diferentes linhas na tabela têm diferentes conjuntos de propriedades).

### <a name="entity-specific-schema"></a>Esquema específico de entidade

Existem três tipos de entradas telemétricas específicas de entidades cada uma empurrada com a seguinte frequência:

- Pontos finais de streaming: A cada 30 segundos
- Canais ao vivo: A cada minuto
- Arquivo ao vivo: A cada minuto

**Ponto Final de Transmissão em Fluxo**

Propriedade|Valor|Exemplos
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
CarimboDeDataEHora|CarimboDeDataEHora|Marca de tempo automático da Tabela Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|StreamingEndpoint
Name|Name|StreamingEndpointRequestLog
Tempo observado|Tempo observado|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Nome do Anfitrião|Nome de anfitrião do ponto final|builddemoserver.origin.mediaservices.windows.net
Código de Estado|Registos HTTP|200
Resultados Código|Detalhe do código de resultados|S_OK
PedidoCount|Pedido total na agregação|3
BytesSent|Bytes agregados enviados|2987358
ServerLatency|Latência média do servidor (incluindo armazenamento)|129
E2ELatency|Latência média de ponta a ponta|250

**Canal ao vivo**

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
CarimboDeDataEHora|CarimboDeDataEHora|Marca de tempo automática da tabela Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|Canal
Name|Name|ChannelHeartbeat
Tempo observado|Tempo observado|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Tipo de vídeo/áudio/texto de faixa|vídeo/áudio
Nome de track|Nome da pista|vídeo/audio_1
Bitrate|Bitrate de pista|785000
CustomAttributes||   
IncomingBitrate|Bitrate de entrada real|784548
Sobreposição|Sobreposição na ingestão|0
Contagem descontinuidade|Descontinuidade para pista|0
Última Hora da Noite|Última data ingerida estamp de tempo|1800488800
NonincreasingCount|Contagem de fragmentos descartados devido ao tempotames não crescente|2
Quadros Desalinhados|Se recebemos fragmentos (s) (em níveis de qualidade) onde quadros-chave não alinhados |Verdadeiro
Apresentação Não Assinada|Se recebemos fragmentos (através de níveis/faixas de qualidade) onde o tempo de apresentação não está alinhado|Verdadeiro
Bitrato inesperado|É verdade, se calculado/bitrate real para a faixa de áudio/vídeo > 40.000 bps e IncomingBitrate == 0 OU IncomingBitrate e actualBitrate diferem em 50% |Verdadeiro
Bom estado de funcionamento|Verdade, se <br/>sobreposiçãoCount, <br/>DescontinuidadeCount, <br/>NonIncreasingCount, <br/>QuadrosKey desalinhados, <br/>Apresentação Não Assinada, <br/>Bitrato inesperado<br/> são todos 0|Verdadeiro<br/><br/>Saudável é uma função composta que retorna falsa quando qualquer uma das seguintes condições mantém:<br/><br/>- Sobreposição > 0<br/>- Descontinuidade > 0<br/>- NonincreasingCount > 0<br/>- DesalinhadosKeyFrames == Verdadeiro<br/>- DesalinhadoPresentação Hora da Apresentação == Verdade<br/>- InesperadoBitrate == Verdadeiro

**Arquivo ao vivo**

Propriedade|Valor|Exemplos/notas
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
CarimboDeDataEHora|CarimboDeDataEHora|Marca de tempo automática da tabela Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|Arquivo
Name|Name|ArchiveHeartbeat
Tempo observado|Tempo observado|2016-09-09T22:42:36.924Z
ServiceID|ID de Serviço|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestoName|Url do programa|activo-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
Nome de track|Nome da pista|audio_1
TrackType|Tipo da pista|Áudio/vídeo
CustomAttribute|Cadeia hex que diferencia entre faixa diferente com o mesmo nome e bitrate (ângulo multi-câmara)|
Bitrate|Bitrate de pista|785000
Bom estado de funcionamento|Verdade, se FragmentDiscardedCount == 0 && ArchiveAcquisitionError == Falso|Verdade (estes dois valores não estão presentes na métrica, mas estão presentes no evento fonte)<br/><br/>Saudável é uma função composta que retorna falsa quando qualquer uma das seguintes condições mantém:<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == Verdadeiro

## <a name="general-qa"></a>General Q&A

### <a name="how-to-consume-metrics-data"></a>Como consumir dados de métricas?

Os dados das métricas são armazenados como uma série de Tabelas Azure na conta de armazenamento do cliente. Estes dados podem ser consumidos utilizando as seguintes ferramentas:

- AMS SDK
- Microsoft Azure Storage Explorer (suporta exportação para formato de valor separado por vírgula e processado no Excel)
- API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Como encontrar um consumo médio de largura de banda?

O consumo médio de largura de banda é a média de BytesSent ao longo de um período de tempo.

### <a name="how-to-define-streaming-unit-count"></a>Como definir a contagem de unidades de streaming?

A contagem de unidades de streaming pode ser definida como o pico de produção dos pontos finais de streaming do serviço divididos pela produção máxima de um ponto final de streaming. O pico utilizável de um ponto final de streaming é de 160 Mbps.
Por exemplo, suponha que o pico de produção do serviço de um cliente seja de 40 MBps (o valor máximo de BytesSent ao longo de um período de tempo). Em seguida, a contagem da unidade de streaming é igual a (40 MBps)*(8 bits/byte)/(160 Mbps) = 2 unidades de streaming.

### <a name="how-to-find-average-requestssecond"></a>Como encontrar pedidos médios/segundo?

Para encontrar o número médio de pedidos/segundo, calcule o número médio de pedidos (RequestCount) ao longo de um período de tempo.

### <a name="how-to-define-channel-health"></a>Como definir a saúde do canal?

A saúde do canal pode ser definida como uma função booleana composta de modo a que seja falsa quando qualquer uma das seguintes condições se mantém:

- Sobreposição > 0
- Descontinuidade > 0
- NonincreasingCount > 0
- Quadros Desalinhados deKey == Verdadeiro 
- Apresentação Não Atando Tempo == Verdadeiro 
- InesperadoBitrate == Verdadeiro


### <a name="how-to-detect-discontinuities"></a>Como detetar descontinuidades?

Para detetar descontinuidades, encontre todas as entradas de dados do Canal onde o Descontinuidade > 0. O respetivo aviso de tempo de Tempo observado indica os tempos em que ocorreram as descontinuidades.

### <a name="how-to-detect-timestamp-overlaps"></a>Como detetar sobreposições de tempo?

Para detetar sobreposições de tempo, encontre todas as entradas de dados do Canal onde o Sobreposição > 0. O respetivo aviso de tempo do Tempo de Observação indica os tempos em que o seu tempo de se sobreposição ocorreu.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Como encontrar falhas e razões de pedido de streaming?

Para encontrar falhas e razões de pedido de streaming, encontre todas as entradas de dados do Streaming Endpoint onde o ResultadoCode não seja igual a S_OK. O campo statusCode correspondente indica o motivo da falha do pedido.

### <a name="how-to-consume-data-with-external-tools"></a>Como consumir dados com ferramentas externas?

Os dados telemétricos podem ser processados e visualizados com as seguintes ferramentas:

- Power BI
- Application Insights
- Azure Monitor (antiga Shoebox)
- Painel AMS Live
- Portal Azure (versão pendente)

### <a name="how-to-manage-data-retention"></a>Como gerir a retenção de dados?

O sistema de telemetria não fornece gestão de retenção de dados ou eliminação automática de registos antigos. Assim, é necessário gerir e apagar registos antigos manualmente da mesa de armazenamento. Pode consultar o SDK de armazenamento para saber como fazê-lo.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
