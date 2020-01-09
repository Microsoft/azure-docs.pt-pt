---
title: Localizadores de streaming nos serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação de quais localizadores de streaming são e como eles são usados pelos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 1cc0132cc17217c858060e107dfcfc090a3ef8a7
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611003"
---
# <a name="streaming-locators"></a>Localizadores de Transmissão em Fluxo

Para fazer vídeos no Recurso de saída disponível para a reprodução em clientes, tem de criar um [Localizador de Transmissões em Fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, criar os URLs das transmissões. Para criar uma URL, você precisa concatenar o nome do host do ponto de extremidade de streaming e o caminho do localizador de streaming. Para um exemplo de .NET, veja [Get a Streaming Locator](stream-files-tutorial-with-api.md#get-a-streaming-locator) (Obter um Localizador de Transmissão em Fluxo).

O processo de criação de um **localizador de streaming** é chamado publicação. Por padrão, o **localizador de streaming** é válido imediatamente depois que você faz as chamadas à API e dura até que ela seja excluída, a menos que você configure as horas de início e término opcionais. 

Ao criar um **localizador de streaming**, você deve especificar um nome de **ativo** e um nome de **política de streaming** . Para obter mais informações, consulte os seguintes tópicos:

* [Recursos](assets-concept.md)
* [Streaming Policies](streaming-policy-concept.md) (Políticas de Transmissão em Fluxo)
* [Content Key Policies](content-key-policy-concept.md) (Políticas de chave de conteúdo)

Você também pode especificar a hora de início e de término no seu localizador de streaming, o que permitirá que o usuário execute o conteúdo entre esses horários (por exemplo, entre 5/1/2019 e 5/5/2019).  

## <a name="considerations"></a>Considerações

* Os **localizadores de streaming** não são atualizáveis. 
* As propriedades dos **localizadores de streaming** que são do tipo DateTime estão sempre no formato UTC.
* Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-las para seus localizadores de streaming sempre que as mesmas opções forem necessárias. Para obter mais informações, consulte [cotas e limitações](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Criar localizadores de streaming  

### <a name="not-encrypted"></a>Não criptografado

Se você quiser transmitir o arquivo no-The-Clear (não criptografado), defina a política Clear streaming predefinida: para ' Predefined_ClearStreamingOnly ' (no .NET, você pode usar a enumeração PredefinedStreamingPolicy. ClearStreamingOnly).

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

### <a name="encrypted"></a>Encriptada 

Se você precisar criptografar o conteúdo com a criptografia CENC, defina sua política como ' Predefined_MultiDrmCencStreaming '. A criptografia Widevine será aplicada a um fluxo de DASH e a um PlayReady para Smooth. A chave será entregue a um cliente de reprodução com base nas licenças DRM configuradas.

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

Se você também quiser criptografar o fluxo do HLS com CBCS (FairPlay), use ' Predefined_MultiDrmStreaming '.

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associar filtros a localizadores de streaming

Consulte [filtros: associar a localizadores de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Entidades do localizador de streaming, Order, Page

Consulte [filtragem, ordenação, paginação de entidades de serviços de mídia](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Listar localizadores de streaming por nome de ativo

Para obter localizadores de streaming com base no nome do ativo associado, use as seguintes operações:

|Linguagem|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="also-see"></a>Veja também

* [Recursos](assets-concept.md)
* [Streaming Policies](streaming-policy-concept.md) (Políticas de Transmissão em Fluxo)
* [Content Key Policies](content-key-policy-concept.md) (Políticas de chave de conteúdo)

## <a name="next-steps"></a>Passos seguintes

[Tutorial: carregar, codificar e transmitir vídeos usando o .NET](stream-files-tutorial-with-api.md)
