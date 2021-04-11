---
title: Orientação de migração de proteção de conteúdos
description: Este artigo dá ao seu cenário de proteção de conteúdos orientação baseada em cenário que o ajudará na sua migração da Azure Media Services v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490952"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Orientação de migração baseada em cenários de proteção de conteúdos

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo fornece-lhe detalhes e orientações sobre a migração de casos de proteção de conteúdos da API v2 para o novo Azure Media Services v3 API.

## <a name="protect-content-in-v3-api"></a>Proteger o conteúdo em V3 API

Utilize o suporte para [funcionalidades multi-chave](architecture-design-multi-drm-system.md) na nova API v3.

Consulte conceitos de proteção de conteúdos, tutoriais e como guiar no final deste artigo para etapas específicas.

> [!NOTE]
> O resto deste artigo discute como pode migrar a sua proteção de conteúdo V2 para v3 com .NET.  Se precisar de instruções ou de um código de amostra para um idioma ou método diferente, por favor, crie um problema GitHub para esta página.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>v3 visibilidade de v2 Ativos, StreamingLocators e propriedades

Na API `Assets` v2, `StreamingLocators` e foram `ContentKeys` usados para proteger o seu conteúdo de streaming. Ao migrar para a API v3, o seu V2 `Assets` API, e todos expostos automaticamente na `StreamingLocators` `ContentKeys` API v3 e todos os dados sobre os mesmos estão disponíveis para aceder.

No entanto, não é possível *atualizar* quaisquer propriedades em entidades V2 através da V3 API que foram criadas em V2.

Se necessitar de atualizar, alterar ou alterar os conteúdos armazenados em entidades V2, atualize-os com a API v2 ou crie novas entidades da API v3 para os migrar.

## <a name="asset-identifier-differences"></a>Diferenças de identificador de ativos

Para migrar, terá de aceder a propriedades ou chaves de conteúdo a partir dos seus Ativos V2.  É importante entender que a API v2 usa a `AssetId` chave de identificação primária, mas a nova V3 API usa o nome de Gestão de *Recursos Azure* da entidade como identificador principal.  (A propriedade v2 `Asset.Name` não é usada como um identificador único.) Com a V3 API, o seu nome v2 Asset aparece agora como `Asset.Description` o .

Por exemplo, se já tinha um Ativo V2 com o ID `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` de, o identificador encontra-se agora no final do GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` . Verá isto ao listar os seus ativos V2 através da API v3.

Quaisquer Ativos que tenham sido criados e publicados utilizando a API v2 terão tanto `ContentKeyPolicy` a a e a a na `ContentKey` API v3 em vez de uma política de chave de conteúdo padrão na `StreamingPolicy` .

Para obter mais informações, consulte a documentação [da política-chave](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) de conteúdo e a documentação da [Política de Streaming.](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept)

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Utilize ferramentas Azure Media Services Explorer (AMSE) v2 e AMSE v3 lado a lado

Utilize a [ferramenta v2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) juntamente com a ferramenta [V3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) para comparar os dados lado a lado com um Ativo criado e publicado através de APIs v2. As propriedades devem ser visíveis, mas em locais diferentes.

## <a name="use-the-net-content-protection-migration-sample"></a>Utilize a amostra de migração de proteção de conteúdo .NET

Pode encontrar uma amostra de código para comparar as diferenças nos identificadores de Ativos utilizando o [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) sob a Proteção de Conteúdos nas amostras de código dos Serviços de Comunicação.

## <a name="list-the-streaming-locators"></a>Listar os localizadores de streaming

Pode consultar os `StreamingLocators` Ativos associados aos Ativos criados na API v2 utilizando o novo método v3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) na entidade Desíprocora.  Consulte também a versão SDK do cliente .NET do [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Os resultados do `ListStreamingLocators` método fornecer-lhe-ão `Name` o e do `StreamingLocatorId` localizador juntamente com o `StreamingPolicyName` .

## <a name="find-the-content-keys"></a>Encontre as chaves de conteúdo

Para encontrar o `ContentKeys` usado com o seu , pode ligar para o método `StreamingLocators` [StreamingLocator.ListContentKeysAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true)  

Para obter mais informações sobre a proteção de conteúdos na API v3, consulte o artigo [Proteja o seu conteúdo com encriptação dinâmica dos Media Services.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>Alterar o v2 ContentKeyPolicy mantendo o mesmo ContentKey

Em primeiro lugar, deve não publicar (remover todos os localizadores de streaming) no Ativo através do V2 SDK. Eis como:

1. Apague o localizador.
1. Desvincula-se. `ContentKeyAuthorizationPolicy`
1. Desvincula-se. `AssetDeliveryPolicy`
1. Desvincula-se. `ContentKey`
1. Elimine o `ContentKey` .
1. Crie um novo `StreamingLocator` em v3 utilizando um V3 `StreamingPolicy` `ContentKeyPolicy` e, especificando o identificador de chave de conteúdo específico e o valor chave necessário.

> [!NOTE]
> É possível eliminar o localizador V2 utilizando a API v3, mas isso não removerá a chave de conteúdo ou a política de chave de conteúdo se forem criados na API v2.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Conceitos de proteção de conteúdos, tutoriais e como guiar

### <a name="concepts"></a>Conceitos

- [Proteja o seu conteúdo com encriptação dinâmica dos Media Services](drm-content-protection-concept.md)
- [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](architecture-design-multi-drm-system.md)
- [Serviços de Mídia v3 com modelo de licença PlayReady](drm-playready-license-template-concept.md)
- [Serviços de Mídia v3 com visão geral do modelo de licença widevine](drm-widevine-license-template-concept.md)
- [Requisitos de licença e configuração do Apple FairPlay](drm-fairplay-license-overview.md)
- [Políticas de streaming](stream-streaming-policy-concept.md)
- [Políticas-chave de conteúdo](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Tutoriais

[Quickstart: Use o portal para encriptar conteúdo](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Guias de procedimentos

- [Obter uma chave de assinatura da política existente](drm-get-content-key-policy-dotnet-how-to.md)
- [Streaming Offline FairPlay para iOS com Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Streaming offline Widevine para Android com Media Services v3](drm-offline-widevine-for-android.md)
- [Streaming Offline PlayReady para Windows 10 com Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Amostras

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- Também pode [comparar o código V2 e V3 nas amostras de código](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Ferramentas

- [v3 Azure Media Services Explorer ferramenta](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 Ferramenta Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
