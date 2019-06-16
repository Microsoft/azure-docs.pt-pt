---
title: Transmissão em fluxo localizadores nos serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo fornece uma explicação sobre o que são os localizadores de transmissão em fluxo e como elas são usadas pelos serviços de multimédia do Azure.
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
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299172"
---
# <a name="streaming-locators"></a>Localizadores de Transmissão em Fluxo

Para fazer vídeos no Recurso de saída disponível para a reprodução em clientes, tem de criar um [Localizador de Transmissões em Fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, criar os URLs das transmissões. Para compilar um URL, terá de concatenar o nome de anfitrião do ponto final de transmissão em fluxo e o caminho de localizador de transmissão em fluxo. Para um exemplo de .NET, veja [Get a Streaming Locator](stream-files-tutorial-with-api.md#get-a-streaming-locator) (Obter um Localizador de Transmissão em Fluxo).

O processo de criação de um **localizador de transmissão em fluxo** é chamado de publicação. Por predefinição, o **localizador de transmissão em fluxo** é válido, imediatamente após fazer as chamadas à API e dura até serem eliminada, a menos que configure o início opcional e de horas de fim. 

Ao criar um **localizador de transmissão em fluxo**, tem de especificar um **Asset** nome e um **política de transmissão em fluxo** nome. Para obter mais informações, consulte os seguintes tópicos:

* [Ativos](assets-concept.md)
* [Streaming Policies](streaming-policy-concept.md) (Políticas de Transmissão em Fluxo)
* [Content Key Policies](content-key-policy-concept.md) (Políticas de Chaves de Conteúdos)

Também pode especificar a hora de início e de fim de sua localizador de transmissão em fluxo, que só permitirá que o seu utilizador reproduzir o conteúdo entre essas horas (por exemplo, entre 5/1/2019 para 5/5/2019).  

## <a name="considerations"></a>Considerações

* **Os localizadores de transmissão em fluxo** não são atualizáveis. 
* Propriedades de **localizadores de transmissão em fluxo** que são de Datetime tipo são sempre em formato UTC.
* Deve criar um conjunto limitado de políticas para a sua conta de serviço de multimédia e reutilizá-los para os localizadores de transmissão em fluxo sempre que as mesmas opções são necessárias. Para obter mais informações, consulte [Quotas e limitações](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Criar localizadores de transmissão em fluxo  

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

### <a name="encrypted"></a>Encriptados 

Se precisar de encriptar o seu conteúdo com a encriptação de CENC, defina a política para 'Predefined_MultiDrmCencStreaming'. A encriptação do Widevine será aplicada a um fluxo DASH e PlayReady para uniforme. A chave será entregue a um cliente de reprodução com base em licenças DRM configuradas.

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

Se desejar encriptar a sua transmissão em fluxo HLS com CBCS (FairPlay), utilize 'Predefined_MultiDrmStreaming'.

## <a name="associate-filters-with-streaming-locators"></a>Associar filtros localizadores de transmissão em fluxo

Ver [filtros: associar às localizadores de transmissão em fluxo](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrar, ordem, entidades de localizador de transmissão em fluxo de página

Ver [filtragem, ordenação, a paginação de entidades de serviços de multimédia](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Lista os localizadores de transmissão em fluxo por nome de recurso

Para obter os localizadores de transmissão em fluxo de mensagens em fila com base no nome do recurso associado, utilize as seguintes operações:

|Idioma|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>Consulte também

* [Ativos](assets-concept.md)
* [Streaming Policies](streaming-policy-concept.md) (Políticas de Transmissão em Fluxo)
* [Content Key Policies](content-key-policy-concept.md) (Políticas de Chaves de Conteúdos)

## <a name="next-steps"></a>Passos Seguintes

[Tutorial: Upload, encode, and stream videos using .NET](stream-files-tutorial-with-api.md) (Tutorial: carregar, codificar e transmitir vídeos em fluxo com .NET)
