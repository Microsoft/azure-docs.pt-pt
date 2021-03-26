---
title: Alterações na terminologia e na entidade dos Serviços de Comunicação Social v3
description: Este artigo descreve as diferenças de terminologia entre a Azure Media Services v2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5c1fbaf9be4cb128f0e4390a8c97c6f0b9330ce2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559881"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Terminologia e alterações de entidades entre os Serviços de Mídia V2 e V3

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-2.svg)

Este artigo descreve as diferenças de terminologia entre a Azure Media Services v2 a v3.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Reveja as convenções de nomeação que são aplicadas aos recursos dos Media Services V3. Também [reveja o nome de bolhas](assets-concept.md#naming)

## <a name="terminology-changes"></a>Alterações de terminologia

- Um *localizador* é agora chamado de *localizador de streaming.*
- Um *Canal* é agora chamado de *Evento Ao Vivo.*
- Um *programa* é agora chamado de *Saída Ao Vivo.*
- Uma *Tarefa* é agora chamada de *JobOutput*, que faz parte de um Trabalho.

## <a name="entity-changes"></a>Alterações de entidades
| **Entidade V2**<!-- row --> | **Entidade V3** | **Orientação** | **Acessível a V3** | **Atualizado por V3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  A entidade `AccessPolicies` não existe na V3. | No | No |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Yes | Yes |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Yes | No |
| `AssetFile`<!-- row --> | <!-- empty --> |A entidade `AssetFiles` não existe na V3. Embora os ficheiros (blobs de armazenamento) que carrega ainda sejam considerados ficheiros.<br/><br/> Utilize as APIs de Armazenamento Azure para enumerar as bolhas num recipiente. Há duas maneiras de aplicar uma transformação nos ficheiros com um trabalho:<br/><br/>Ficheiros já enviados para armazenamento: O URI incluiria o ID do ativo para que os trabalhos fossem feitos em ativos dentro de uma conta de armazenamento.<br/><br/>Ficheiros a serem carregados durante o processo de transformação e trabalho: O ativo é criado no armazenamento, um URL SAS é devolvido, os ficheiros são carregados para armazenamento e, em seguida, a transformação é aplicada aos ficheiros. | No | No |
| `Channel`<!-- row --> | `LiveEvent` | Os Eventos Ao Vivo substituem os Canais da V2 API. Eles carregam a maioria das funcionalidades, e têm mais novidades como transcrições ao vivo, modo stand-by e suporte para ingestão DE RTMPS. <br/><br/>Ver [evento ao vivo em site baseado em streaming ao vivo](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | No | No |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` já não é uma entidade, é agora uma propriedade de um localizador de streaming.<br/><br/>  No v3, os dados-chave do conteúdo estão associados ao `StreamingLocator` (para encriptação de saída) ou ao próprio Ativo (para encriptação de armazenamento lateral do cliente). | Yes | No |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Yes | No |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` estão incluídos no `ContentKeyPolicy` . | Yes | No |
| `IngestManifest`<!-- row --> | <!-- empty --> | A entidade `IngestManifests` não existe na V3. O upload de ficheiros na V3 envolve a API de armazenamento Azure. Os ativos são criados primeiro e depois os ficheiros são enviados para o recipiente de armazenamento associado. Há muitas maneiras de obter dados em um recipiente de armazenamento Azure que pode ser usado em vez disso. `JobInputHttp` também fornece uma maneira de baixar uma entrada de trabalho de um dado url, se desejar. | No | No |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Há muitas maneiras de obter dados em um recipiente de armazenamento Azure que pode ser usado em vez disso. `JobInputHttp` também fornece uma maneira de baixar uma entrada de trabalho de um dado url, se desejar. | No | No |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Há muitas maneiras de obter dados em um recipiente de armazenamento Azure que pode ser usado em vez disso. `JobInputHttp` também fornece uma maneira de baixar uma entrada de trabalho de um dado url, se desejar. | No | No |
| `Job`<!-- row --> | `Job` | Crie um `Transform` antes de criar um `Job` . | No | No |
| `JobTemplate`<!-- row --> | `Transform` | Use um `Transform` em vez disso. Uma transformação é uma entidade separada de um trabalho e é reutilizável. | No | No |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Yes | No |
| `MediaProcessor`<!-- row --> | <!-- empty --> | Em vez de procurar o `MediaProcessor` uso pelo nome, use a predefinição desejada ao definir uma transformação. A predefinição utilizada determinará o processador de mídia utilizado pelo sistema de trabalho. Consulte tópicos de codificação em [codificação baseada](migrate-v-2-v-3-migration-scenario-based-encoding.md)em cenários . <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | No | NA (apenas em V2) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | As notificações na V3 são tratadas através da Grelha de Eventos Azure. A `NotificationEndpoint` inscrição de assinatura da Grade de Eventos é substituída pelo registo de subscrição da Grade de Eventos que também engloba a configuração para os tipos de notificações a receber (que em v2 foi tratado pelo `JobNotificationSubscription` trabalho, o `TaskNotificationSubscription` da Tarefa e da Telemetria). `ComponentMonitoringSetting` A telemetria v2 foi dividida entre a Azure Event Grid e o Azure Monitor para se encaixar nos melhoramentos do ecossistema Azure maior. | No | No |
| `Program`<!-- row --> | `LiveOutput` | As Saídas Ao Vivo substituem agora os Programas na API v3.  | No | No |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | O streaming endpoints permanece principalmente o mesmo. São usados para embalagens dinâmicas, encriptação e entrega de conteúdo HLS e DASH para streaming ao vivo e a pedido, quer diretamente de origem, quer através do CDN. As novas funcionalidades incluem suporte para uma melhor integração e gráfico do Azure Monitor. |  Yes | Yes |
| `Task`<!-- row --> | `JobOutput` | Substituída `JobOutput` por (que já não é uma entidade separada na API).  Consulte tópicos de codificação em [codificação baseada](migrate-v-2-v-3-migration-scenario-based-encoding.md)em cenários . | No | No |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Substituída `TransformOutput` por (que já não é uma entidade separada na API). Consulte tópicos de codificação em [codificação baseada](migrate-v-2-v-3-migration-scenario-based-encoding.md)em cenários . | No | No |
| `Inputs`<!-- row --> | `Inputs` | As entradas e saídas estão agora ao nível do Job. Ver tópicos de codificação em [codificação baseada](migrate-v-2-v-3-migration-scenario-based-encoding.md) em cenário | No | No |
| `Outputs`<!-- row --> | `Outputs` | As entradas e saídas estão agora ao nível do Job.  Em V3, o formato de metadados mudou de XML para JSON.  As Saídas Ao Vivo começam na criação e param quando apagadas. Ver tópicos de codificação em [codificação baseada](migrate-v-2-v-3-migration-scenario-based-encoding.md) em cenário | No | No |


| **Outras alterações** | **V2**  | **V3** |
|---|---|---|
| **Armazenamento** <!--new row --> |||
| Armazenamento <!--new row --> | | Os V3 SDKs estão agora dissociados do Storage SDK, o que lhe dá mais controlo sobre a versão do Storage SDK que pretende utilizar e evita problemas de versão.                      |
| **Encoding** (Codificação) <!--new row --> |||
| Taxas de codificação <!--new row --> | bit rates medidos em kbps ex: 128 (kbps)| bits por segundo ex: 128000 (bits/segundo)|
| Codificação DRM FairPlay <!--new row --> | Nos Serviços de Mídia V2, o vetor de inicialização (IV) pode ser especificado. | Nos Serviços de Comunicação Social V3, o FairPlay IV não pode ser especificado.|
| Codificação premium <!--new row --> | Codificador premium e Indexante Legado| O [Premium Encoder](../previous/media-services-encode-asset.md) e os [processadores de análise de mídia](../previous/legacy-components.md) legacy (Azure Media Services Indexer 2 Preview, Face Redator, etc.) não estão acessíveis via V3. Adicionámos suporte para mapeamento de canais de áudio ao codificar Standard.  Consulte [o Áudio nos Serviços de Comunicação Encoding Swagger documentação](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  | Ver tópicos de codificação em [codificação baseada](migrate-v-2-v-3-migration-scenario-based-encoding.md) em cenário |
| **Transformações e tarefas** <!--new row -->|||
| Processamento baseado em trabalho HTTPS <!--new row --> |<!-- empty -->| Para o processamento de job baseado em ficheiros, pode utilizar um URL HTTPS como entrada. Não precisa de ter conteúdos já armazenados no Azure, nem precisa de criar Ativos. |
| Modelos ARM para empregos <!--new row --> | Os modelos ARM não existiam em V2. | Uma transformação pode ser usada para construir configurações reutilizáveis, para criar modelos de Gestor de Recursos Azure e isolar configurações de processamento entre vários clientes ou inquilinos. |
| **Eventos ao vivo** <!--new row --> |||
| Ponto final de transmissão em fluxo <!--new row --> | Um ponto final de streaming representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação do leitor cliente, ou a uma Rede de Entrega de Conteúdos (CDN) para posterior distribuição. | O streaming endpoints permanece principalmente o mesmo. São usados para embalagens dinâmicas, encriptação e entrega de conteúdo HLS e DASH para streaming ao vivo e a pedido, quer diretamente de origem, quer através do CDN.  As novas funcionalidades incluem suporte para uma melhor integração e gráfico do Azure Monitor. |
| Canais de eventos ao vivo <!--new row --> | Os canais são responsáveis pelo processamento de conteúdos de streaming ao vivo. Um Canal fornece um ponto final de entrada (ingest URL) que fornece a um transcodificador vivo. O canal recebe streams de entrada ao vivo a partir do transcodificador ao vivo e disponibiliza-o para streaming através de um ou mais pontos finais de streaming. Os canais também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para visualizar e validar o seu fluxo antes de ser processado e entregue.| Os Eventos Ao Vivo substituem os Canais da V2 API. Eles carregam a maioria das funcionalidades, e têm mais novidades como transcrições ao vivo, modo stand-by e suporte para ingestão DE RTMPS. |
| Programas de eventos ao vivo <!--new row --> | Um programa permite-lhe controlar a publicação e o armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação channel and Program é semelhante aos meios tradicionais onde um canal tem um fluxo constante de conteúdo e um programa é telescópio para algum evento cronometrado nesse canal. Pode especificar o número de horas que pretende manter o conteúdo gravado para o programa definindo a `ArchiveWindowLength` propriedade. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.| As Saídas Ao Vivo substituem agora os Programas na API v3. |
| Comprimento do evento ao vivo <!--new row --> |<!-- empty -->| Pode transmitir Live Events 24/7 ao utilizar os Media Services para transcoding de um único feed de contribuição bitrate para um fluxo de saída que tem vários bitrates.|
| Latência do evento ao vivo <!--new row --> |<!-- empty -->| Novo apoio ao vivo de baixa latência em eventos ao vivo. |
| Pré-visualização do evento ao vivo <!--new row --> |<!-- empty -->| A pré-visualização do evento ao vivo suporta embalagens dinâmicas e encriptação dinâmica. Isto permite a proteção de conteúdos na pré-visualização, bem como na embalagem DASH e HLS. |
| Evento ao vivo RTMPS <!--new row --> |<!-- empty-->| Melhor suporte rtmps com maior estabilidade e mais suporte de codificadores de origem. |
| Evento ao vivo RTMPS segura ingerir <!--new row --> | | Quando se cria um evento ao vivo, obtém-se 4 URLs ingeridos. Os 4 URLs ingeridos são quase idênticos, têm o mesmo token de `AppId` streaming, só que a parte do número da porta é diferente. Dois dos URLs são primários e de reserva para RTMPS.| 
| Transcrição de evento ao vivo <!--new row --> |<!-- empty--> | O Azure Media Service fornece vídeo, áudio e texto em diferentes protocolos. Quando publica o seu live stream utilizando MPEG-DASH ou HLS/CMAF, em seguida, juntamente com vídeo e áudio, o nosso serviço entrega o texto transcrito em TTML compatível com IMSC1.1.|
| Modo de standby de evento ao vivo <!--new row --> | Não havia modo de espera para o V2. | O modo stand-by é uma nova funcionalidade v3 que ajuda a gerir piscinas quentes de Eventos Ao Vivo. Os clientes podem agora iniciar um Live Event em modo stand-by a um custo mais baixo antes de transitar para o estado de execução. Isto melhora os tempos de início do canal e reduz os custos de operação de piscinas quentes para arranques mais rápidos. |
| Faturação de evento ao vivo <!--new row --> | <!-- empty-->| A faturação de eventos ao vivo é baseada em medidores do Live Channel. |
| Saídas ao vivo <!--new row --> | Os programas tiveram de ser iniciados após a criação. | As Saídas Ao Vivo começam na criação e param quando apagadas. |
