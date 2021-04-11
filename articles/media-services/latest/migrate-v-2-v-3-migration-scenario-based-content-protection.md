---
title: Orientação de migração de proteção de conteúdos
description: Este artigo dá ao seu cenário de proteção de conteúdos orientação baseada em cenário que o ajudará na sua migração da Azure Media Services v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 74f15fc302a8499e41a1413dd8915e6442d4bbe7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064499"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Orientação de migração baseada em cenários de proteção de conteúdos

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo fornece-lhe detalhes e orientações sobre a migração de casos de proteção de conteúdos da API v2 para o novo Azure Media Services v3 API.

## <a name="protect-content-in-v3-api"></a>Proteger o conteúdo em V3 API

Utilize o suporte para [funcionalidades multi-chave](architecture-design-multi-drm-system.md) na nova API v3.

Consulte conceitos de proteção de conteúdos, tutoriais e como guiar abaixo para etapas específicas.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>Visibilidade dos ativos v2, StreamingLocators e propriedades na API v3 para cenários de proteção de conteúdos

Durante a migração para a V3 API, você vai descobrir que você precisa aceder a algumas propriedades ou chaves de conteúdo a partir dos seus V2 Ativos. Uma diferença fundamental é que a API v2 usaria o **AssetId** como chave de identificação primária e a nova API v3 utiliza o nome de Gestão de Recursos Azure da entidade como identificador primário.  A propriedade v2 **Asset.Name** não é normalmente usada como um identificador único, por isso, ao migrar para v3, verá que os seus nomes de Ativos V2 aparecem agora no campo **Ativo.Descrição.**

Por exemplo, se já tinha um Ativo v2 com o ID de **"nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8",** então encontrará ao listar os antigos ativos v2 através da API v3, então você vai encontrar ao listar os antigos ativos v2 através da API v3, então você vai encontrar ao enumerar os antigos ativos v2 através da API v3, então você vai encontrar ao listar os antigos ativos v2 através da API v3, então você vai encontrar ao listar os antigos ativos v2 através da API v3, então você vai encontrar ao enumerar os antigos ativos v2 através da API v3, então você vai encontrar ao enumerar os antigos ativos v2 através da API v3, então você vai encontrar ao listar os antigos ativos v2 através da API v3, então você vai encontrar ao enumerar os antigos ativos v2 através da API v3, então você vai encontrar ao enumerar os antigo o nome passará a ser a parte GUID no final (neste caso, **"8cb39104-122c-496e-9ac5-7f9e2c2547b8".**

Pode consultar os **StreamingLocators associados** aos Ativos criados na API v2 utilizando o novo método v3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) na entidade Desíprocos.  Consulte também a versão SDK do cliente .NET do [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Os resultados do método **ListStreamingLocators** fornecer-lhe-ão o **Nome** e **StreamingLocatorId** do localizador juntamente com o **Nome StreamingPolicyName**.

Para encontrar as **Chave de Conteúdo utilizadas** nos seus **StreamingLocators** para proteção de conteúdos, pode ligar para o método [StreamingLocator.ListContentKeysAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true)  

Quaisquer **Ativos** que tenham sido criados e publicados utilizando a API v2 terão uma [Política chave de conteúdo](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) e uma Chave de Conteúdo definida neles na API v3, em vez de utilizarem uma política de chave de conteúdo padrão na Política de [Streaming.](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept)

Para obter mais informações sobre a proteção de conteúdos na API v3, consulte o artigo [Proteja o seu conteúdo com encriptação dinâmica dos Media Services.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>Como listar os seus V2 Ativos e definições de proteção de conteúdos utilizando a API v3

Na API v2, utilizaria comumente **Ativos,** **StreamingLocators** e **ContentKeys** para proteger o seu conteúdo de streaming.
Ao migrar para a V3 API, os seus ativos API v2, StreamingLocators e ContentKeys são todos expostos automaticamente na API v3 e todos os dados sobre eles estão disponíveis para você aceder.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>Posso atualizar as propriedades v2 utilizando a API v3?

Não, não é possível atualizar quaisquer propriedades em entidades V2 através da V3 API que foram criadas utilizando StreamingLocators, StreamingPolicies, Políticas chave de conteúdo e Chaves de Conteúdo em v2.
Se precisar de atualizar, alterar ou alterar os conteúdos armazenados em entidades V2, terá de o atualizar através da API v2 ou criar novas entidades da API v3 para os migrar para a frente.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Como posso alterar o ContentKeyPolicy usado para um Ativo V2 que é publicado e manter a mesma chave de conteúdo?

Nesta situação, deve primeiro não publicar (remover todos os localizadores de streaming) no Ativo através do v2 SDK (eliminar o localizador, desvincular a Política de Autorização de Chave de Conteúdo, desvincular a Política de Entrega de Ativos, desvincular a Chave de Conteúdo, eliminar a Chave de Conteúdo) e, em seguida, criar um novo **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** em v3 utilizando uma V3 [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) e [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept).

É necessário especificar o identificador de chave de conteúdo específico e o valor-chave necessário quando estiver a criar o **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)**.

Note que é possível eliminar o localizador V2 utilizando a API v3, mas isso não removerá a chave de conteúdo ou a política de chave de conteúdo utilizada se forem criadas na API v2.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>Utilizando AMSE v2 e AMSE v3 lado a lado

Ao migrar o seu conteúdo de V2 para V3, é aconselhável instalar a [ferramenta V2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) juntamente com a ferramenta [V3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) para ajudar a comparar os dados que mostram lado a lado para um Ativo que é criado e publicado através de APIs v2. As propriedades devem ser visíveis, mas em locais ligeiramente diferentes agora.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Conceitos de proteção de conteúdos, tutoriais e como guiar

### <a name="concepts"></a>Conceitos

- [Proteja o seu conteúdo com encriptação dinâmica dos Media Services](drm-content-protection-concept.md)
- [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](architecture-design-multi-drm-system.md)
- [Serviços de Mídia v3 com modelo de licença PlayReady](drm-playready-license-template-concept.md)
- [Serviços de Mídia v3 com visão geral do modelo de licença widevine](drm-widevine-license-template-concept.md)
- [Requisitos de licença e configuração do Apple FairPlay](drm-fairplay-license-overview.md)
- [Políticas de streaming](streaming-policy-concept.md)
- [Políticas-chave de conteúdo](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Tutoriais

[Quickstart: Use o portal para encriptar conteúdo](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Guias de procedimentos

- [Obter uma chave de assinatura da política existente](drm-get-content-key-policy-dotnet-how-to.md)
- [Streaming Offline FairPlay para iOS com Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Streaming offline Widevine para Android com Media Services v3](drm-offline-widevine-for-android.md)
- [Streaming Offline PlayReady para Windows 10 com Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Amostras

Também pode [comparar o código V2 e V3 nas amostras de código](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Ferramentas

- [v3 Azure Media Services Explorer ferramenta](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Ferramenta Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
