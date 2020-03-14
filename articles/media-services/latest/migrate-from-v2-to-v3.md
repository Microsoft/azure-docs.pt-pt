---
title: Migrar dos Serviços de Mídia Azure v2 para v3  Microsoft Docs
description: Este artigo descreve as alterações introduzidas no Azure Media Services v3 e mostra diferenças entre duas versões. O artigo também fornece orientação de migração para a mudança dos Serviços de Media v2 para v3.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: serviços de multimédia do azure, transmissão, difusão, em direto, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 5083dc79b146598142ac27eb6ac7ef9ed436f37d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251560"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Orientação de migração para a mudança dos Serviços de Media v2 para v3

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` no leitor de feed RSS.

Este artigo fornece a orientação de migração dos Serviços de Media v2 para v3.

Se tiver um serviço de vídeo desenvolvido hoje em cima do [legado Media Services v2 APIs,](../previous/media-services-overview.md)deve rever as seguintes diretrizes e considerações antes de migrar para as APIs v3. Existem muitos benefícios e novas funcionalidades na API v3 que melhoram a experiência e as capacidades do desenvolvedor dos Media Services. No entanto, tal como foi chamado na secção [Questões Conhecidas](#known-issues) deste artigo, existem também algumas limitações devido a alterações entre as versões API. Esta página será mantida à medida que a equipa de Media Services faz melhorias contínuas nas APIs v3 e aborda as lacunas entre as versões. 

## <a name="prerequisites"></a>Pré-requisitos

* Rever [Os Serviços de Media v2 vs. v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Benefícios dos Serviços de Media v3
  
### <a name="api-is-more-approachable"></a>API é mais acessível

*  A v3 baseia-se numa superfície da API unificada que expõe uma funcionalidade incorporada de gestão e de operações no Azure Resource Manager. Os modelos do Gestor de Recursos Azure podem ser usados para criar e implementar Transforms, Streaming Endpoints, Eventos Ao Vivo e muito mais.
* Documento de [especificação OpenAPI (anteriormente chamado Swagger).](https://aka.ms/ams-v3-rest-sdk)
    Expõe o esquema para todos os componentes do serviço, incluindo codificação baseada em ficheiros.
* SDKs disponíveis para [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js,](/javascript/api/overview/azure/mediaservices/management) [Python,](https://aka.ms/ams-v3-python-ref) [Java,](https://aka.ms/ams-v3-java-ref) [Go](https://aka.ms/ams-v3-go-ref)e Ruby.
* [Integração Azure CLI](https://aka.ms/ams-v3-cli-ref) para suporte simples de scripts.

### <a name="new-features"></a>Novos recursos

* Para o processamento de trabalho baseado em ficheiros, pode utilizar um URL HTTP(S) como entrada.<br/>Não precisa de ter conteúdos já armazenados no Azure, nem precisa de criar Ativos.
* Introduz o conceito de [Transforms](transforms-jobs-concept.md) para processamento de trabalho baseado em ficheiros. Um Transform pode ser usado para construir configurações reutilizáveis, para criar modelos de gestor de recursos Azure, e isolar configurações de processamento entre vários clientes ou inquilinos.
* Um Ativo pode ter [vários localizadores](streaming-locators-concept.md) de streaming cada um com diferentes configurações de [Embalagem Dinâmica](dynamic-packaging-overview.md) e Encriptação Dinâmica.
* [A proteção de conteúdos](content-key-policy-concept.md) suporta funcionalidades multi-teclas.
* Você pode transmitir Eventos ao vivo que têm até 24 horas de duração quando usar os Serviços de Media para transcodificar um único feed de contribuição bitrate para um fluxo de saída que tem várias bitrates.
* Novo suporte de streaming ao vivo de Low Latency em Eventos Ao Vivo. Para mais informações, consulte [a latência.](live-event-latency.md)
* A Pré-visualização de eventos ao vivo suporta [a Embalagem Dinâmica](dynamic-packaging-overview.md) e a Encriptação Dinâmica. Isto permite a proteção de conteúdos na pré-visualização, bem como na embalagem DASH e HLS.
* A Saída Ao Vivo é mais simples de usar do que a entidade do Programa nas APIs v2. 
* Melhor suporte de RTMP (maior estabilidade e mais suporte codificador de fonte).
* RTMPS segura ingerir.<br/>Quando se cria um Evento Ao Vivo, obtém-se 4 URLs ingerir. Os 4 URLs ingerir são quase idênticos, têm o mesmo token de streaming (AppId), apenas a parte número da porta é diferente. Dois dos URLs são primários e reserva para RTMPS.   
* Tem um controlo de acesso baseado em papéis (RBAC) sobre as suas entidades. 

## <a name="known-issues"></a>Problemas conhecidos

*  Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

    * gerir os Media Services v3 [Eventos Ao Vivo,](live-events-outputs-concept.md) 
    * vista (não gerir) [v3 Ativos,](assets-concept.md) 
    * [obtenha informações sobre o acesso a APIs.](access-api-portal.md) 

    Para todas as outras tarefas de gestão (por exemplo, [Transforms and Jobs](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados.
* Você precisa fornecer Media Reserved Units (MRUs) na sua conta para controlar a conmoeda e desempenho dos seus Empregos, particularmente aqueles que envolvam Análise de Vídeo ou Áudio. Para obter mais informações, veja [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Dimensionar o Processamento de Multimédia). Pode gerir as MrUs utilizando o [CLI 2.0 para media services v3,](media-reserved-units-cli-how-to.md)utilizando o [portal Azure,](../previous/media-services-portal-scale-media-processing.md)ou utilizando as [APIs v2](../previous/media-services-dotnet-encoding-units.md). Você precisa fornecer MRUs, quer esteja usando Media Services v2 ou v3 APIs.
* As entidades de Serviços de Media criadas com a V3 API não podem ser geridas pela API v2.  
* Nem todas as entidades da API V2 aparecem automaticamente na API V3.  Seguem-se exemplos de entidades nas duas versões que são incompatíveis:  
    * Os empregos e tarefas criados na v2 não aparecem na v3, uma vez que não estão associados a uma Transform. A recomendação é mudar para v3 Transforms and Jobs. Haverá um período de tempo relativamente curto para monitorizar os postos de trabalho v2 de bordo durante a transição.
    * Canais e Programas criados com v2 (que são mapeados para Eventos Ao Vivo e Saídas ao Vivo em v3) não podem continuar a ser geridos com v3. A recomendação é mudar para v3 Live Events e Live Outputs em uma paragem conveniente do Canal.<br/>Atualmente, não é possível migrar continuamente a correr canais.  

> [!NOTE]
> Esta página será mantida à medida que a equipa de Media Services faz melhorias contínuas nas APIs v3 e aborda as lacunas entre as versões.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Codificar um ficheiro remoto com base em URL e transmitir o vídeo - .NET](stream-files-dotnet-quickstart.md)
