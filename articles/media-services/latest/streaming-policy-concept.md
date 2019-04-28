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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230901"
---
# <a name="streaming-policies"></a>Políticas de Transmissão em Fluxo

Em serviços de multimédia do Azure v3, [políticas de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingpolicies) permitem-lhe definir os protocolos de transmissão em fluxo e opções de encriptação para sua [localizadores de transmissão em fluxo](streaming-locators-concept.md). Pode utilizar uma das políticas predefinidas de transmissão em fluxo ou criado uma política personalizada. As políticas de transmissão em fluxo predefinido atualmente disponíveis são: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'.

> [!IMPORTANT]
> * Propriedades de **políticas de transmissão em fluxo** que são de Datetime tipo são sempre em formato UTC.
> * Deve criar um conjunto limitado de políticas para a sua conta de serviço de multimédia e reutilizá-los para os localizadores de transmissão em fluxo sempre que as mesmas opções são necessárias. 

## <a name="examples"></a>Exemplos

### <a name="not-encrypted"></a>Não encriptado

Se quer transmitir o seu ficheiro no-the-limpar (não encriptadas), definir a política predefinida de transmissão em fluxo clara: para "Predefined_ClearStreamingOnly" (no .NET, pode usar PredefinedStreamingPolicy.ClearStreamingOnly).

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

### <a name="encrypted"></a>Encriptados 

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
