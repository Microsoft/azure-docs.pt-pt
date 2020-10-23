---
title: Migrar do Azure Media Services v2 para v3 Microsoft Docs
description: Este artigo descreve alterações que foram introduzidas no Azure Media Services v3 e mostra diferenças entre duas versões. O artigo também fornece orientações de migração para a mudança dos Serviços de Comunicação Social v2 para v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: serviços de multimédia do azure, transmissão, difusão, em direto, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0cdf4699f5dc00087845ee6ca0d24ad6493c320b
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92426861"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Orientação de migração para passar dos Serviços de Comunicação v2 para v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` no seu leitor de feed RSS.

Este artigo fornece a orientação de migração dos Serviços de Comunicação Social v2 a v3.

Se tem um serviço de vídeo desenvolvido hoje em cima do [legado Media Services v2 APIs,](../previous/media-services-overview.md)deve rever as seguintes diretrizes e considerações antes de migrar para as APIs v3. Existem muitos benefícios e novas funcionalidades na V3 API que melhoram a experiência e capacidades do desenvolvedor dos Media Services. No entanto, como é salientada na secção [Questões Conhecidas](#known-issues) deste artigo, existem também algumas limitações devido a alterações entre as versões API. Esta página será mantida à medida que a equipa de Media Services faz melhorias contínuas nas APIs v3 e aborda as lacunas entre as versões. 

## <a name="prerequisites"></a>Pré-requisitos

* Review [Media Services v2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Benefícios dos Serviços de Mídia v3
  
### <a name="api-is-more-approachable"></a>API é mais acessível

*  A v3 baseia-se numa superfície da API unificada que expõe uma funcionalidade incorporada de gestão e de operações no Azure Resource Manager. Os modelos do Azure Resource Manager podem ser usados para criar e implementar Transformações, Streaming Endpoints, Eventos Ao Vivo e muito mais.
* [Documento de Especificação OpenAPI (anteriormente chamado Swagger).](https://aka.ms/ams-v3-rest-sdk)
    Expõe o esquema para todos os componentes de serviço, incluindo codificação baseada em ficheiros.
* SDKs disponíveis para [.NET](/dotnet/api/overview/azure/mediaservices/management), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python,](/python/api/overview/azure/mediaservices/management) [Java,](/java/api/overview/azure/mediaservices/management) [Go](https://aka.ms/ams-v3-go-ref)e Ruby.
* [Integração do Azure CLI](/cli/azure/ams) para suporte simples de scripts.

### <a name="new-features"></a>Novas funcionalidades

* Para o processamento de trabalho baseado em ficheiros, pode utilizar um URL HTTP(S) como entrada.<br/>Não precisa de ter conteúdos já armazenados no Azure, nem precisa de criar Ativos.
* Introduz o conceito de [Transforms](transforms-jobs-concept.md) para processamento de trabalho baseado em ficheiros. Um Transform pode ser usado para construir configurações reutilizáveis, para criar modelos de gestor de recursos Azure e isolar configurações de processamento entre vários clientes ou inquilinos.
* Um Ativo pode ter [vários localizadores de streaming](streaming-locators-concept.md) cada um com diferentes configurações [de Embalagem Dinâmica](dynamic-packaging-overview.md) e Encriptação Dinâmica.
* [A proteção de conteúdos](content-key-policy-concept.md) suporta funcionalidades multi-chave.
* Você pode transmitir Live Events que têm até 24 horas de duração ao usar os Media Services para transcoding um único feed de contribuição bitrate para um fluxo de saída que tem vários bitrates.
* Novo suporte ao vivo de Baixa Latência em Eventos Ao Vivo. Para mais informações, consulte [a latência.](live-event-latency.md)
* A pré-visualização do evento ao vivo suporta [embalagens dinâmicas](dynamic-packaging-overview.md) e encriptação dinâmica. Isto permite a proteção de conteúdos na pré-visualização, bem como na embalagem DASH e HLS.
* A Saída Ao Vivo é mais simples de usar do que a entidade programina nas APIs v2. 
* Melhor suporte de RTMP (maior estabilidade e mais suporte ao codificação de fontes).
* RTMPS ingerindo seguro.<br/>Quando crias um Evento Ao Vivo, recebes 4 URLs ingeridos. Os 4 URLs ingering são quase idênticos, têm o mesmo token de streaming (AppId), apenas a parte do número da porta é diferente. Dois dos URLs são primários e de reserva para RTMPS.   
* Tem o controlo de acesso baseado em funções (Azure RBAC) sobre as suas entidades. 

## <a name="known-issues"></a>Problemas conhecidos

*  Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

    * gerir os Serviços de Comunicação Social v3 [Live Events,](live-events-outputs-concept.md) 
    * ver (não gerir) v3 [Ativos,](assets-concept.md) 
    * [obter informações sobre o acesso a APIs](./access-api-howto.md). 

    Para todas as outras tarefas de gestão (por exemplo, [Transformações e Empregos](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API](/rest/api/media/), [CLI](/cli/azure/ams)ou um dos [SDKs suportados.](media-services-apis-overview.md#sdks)
* Precisa de atear unidades reservadas de mídia (MRUs) na sua conta para controlar a concordância e desempenho dos seus Empregos, particularmente os que envolvem Vídeo ou Análise Áudio. Para obter mais informações, veja [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Dimensionar o Processamento de Multimédia). Pode gerir as MRUs utilizando o [CLI 2.0 para Serviços de Mídia v3,](media-reserved-units-cli-how-to.md)utilizando o [portal Azure,](../previous/media-services-portal-scale-media-processing.md)ou utilizando as [APIs v2](../previous/media-services-dotnet-encoding-units.md). Você precisa de providenciar MRUs, quer esteja a usar Os Serviços de Mídia v2 ou v3 APIs.
* As entidades de Serviços de Comunicação Social criadas com a V3 API não podem ser geridas pela V2 API.  
* Nem todas as entidades da API V2 aparecem automaticamente na API V3.  Seguem-se exemplos de entidades nas duas versões incompatíveis:  
    * Empregos e Tarefas criadas em V2 não aparecem na V3 uma vez que não estão associadas a uma Transformação. A recomendação é mudar para v3 Transforms and Jobs. Haverá um período de tempo relativamente curto para a necessidade de monitorizar o voo v2 Jobs durante a transição.
    * Canais e Programas criados com v2 (que são mapeados para Eventos Ao Vivo e Saídas Ao Vivo em V3) não podem continuar a ser geridos com v3. A recomendação é mudar para v3 Live Events e Live Outputs numa paragem conveniente do Canal.<br/>Atualmente, não é possível migrar continuamente canais de funcionamento.  

> [!NOTE]
> Esta página será mantida à medida que a equipa de Media Services faz melhorias contínuas nas APIs v3 e aborda as lacunas entre as versões.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Codificar um ficheiro remoto com base no URL e transmitir o vídeo - .NET](stream-files-dotnet-quickstart.md)