---
title: Transmissão em fluxo políticas nos serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo fornece uma explicação sobre o que são políticas de transmissão em fluxo e como elas são usadas pelos serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120214"
---
# <a name="streaming-policies"></a>Políticas de Transmissão em Fluxo

Em serviços de multimédia do Azure v3, [políticas de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingpolicies) permitem-lhe definir os protocolos de transmissão em fluxo e opções de encriptação para sua [localizadores de transmissão em fluxo](streaming-locators-concept.md). Serviços de multimédia v3 fornece que alguns predefinidas políticas de transmissão em fluxo para que pode usá-los diretamente para a versão de avaliação ou de produção. 

Atualmente disponível predefinidas políticas de transmissão em fluxo:<br/>'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'.

Se tiver requisitos especiais (por exemplo, se pretende especificar diferentes protocolos, tem de utilizar um serviço de entrega de chave personalizado ou tem de utilizar um Roteiro claro áudio), pode criar uma política personalizada de transmissão em fluxo. 

 
> [!IMPORTANT]
> * Propriedades de **políticas de transmissão em fluxo** que são de Datetime tipo são sempre em formato UTC.
> * Deve criar um conjunto limitado de políticas para a sua conta de serviço de multimédia e reutilizá-los para os localizadores de transmissão em fluxo sempre que as mesmas opções são necessárias. Para obter mais informações, consulte [Quotas e limitações](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Árvore de decisões

A árvore de decisões seguintes ajudar a escolher uma política predefinida de transmissão em fluxo para o seu cenário.

Clique na imagem para visualizá-lo tamanho completo.  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>Exemplos

### <a name="not-encrypted"></a>Não encriptado

Se quer transmitir o seu ficheiro no-the-limpar (não encriptadas), definir a política predefinida de transmissão em fluxo clara: para "Predefined_ClearStreamingOnly" (no .NET, pode usar a enumeração PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Encriptado 

Se precisar de encriptar o seu conteúdo com encriptação de envelope e cenc, defina a política para 'Predefined_MultiDrmCencStreaming'. Esta política indica que pretende que sejam geradas e definidas no localizador duas chaves de conteúdo (envelope e CENC). Por conseguinte, são aplicadas as encriptação de envelope, do PlayReady e do Widevine (a chave é entregue ao cliente para reprodução, com base nas licenças DRM configuradas).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Se desejar encriptar a sua transmissão em fluxo com CBCS (FairPlay), utilize 'Predefined_MultiDrmStreaming'.

## <a name="filtering-ordering-paging"></a>Paginação de filtragem, ordenação,

Ver [filtragem, ordenação, a paginação de entidades de serviços de multimédia](entities-overview.md).

## <a name="next-steps"></a>Passos Seguintes

* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Utilizar DRM dinâmico licença e de encriptação de serviço de entrega](protect-with-drm.md)
