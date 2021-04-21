---
title: Gerir pontos finais de transmissão em fluxo
description: Este artigo demonstra como gerir os pontos finais de streaming com a Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2b442edc537ec64b12df215a18ab017ee47becff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791738"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Gerir pontos finais de streaming com os Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Quando a sua conta de Serviços de Media é criada, um [Ponto Final de Streaming](stream-streaming-endpoint-concept.md) **predefinido** é adicionado à sua conta no estado **Stop.** Para começar a transmitir o seu conteúdo e tirar partido da [embalagem dinâmica](encode-dynamic-packaging-concept.md) e da [encriptação dinâmica,](drm-content-protection-concept.md)o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **de Funcionamento.**

Este artigo mostra-lhe como executar o comando [inicial](/rest/api/media/streamingendpoints/start) no seu ponto final de streaming usando diferentes tecnologias. 
 
> [!NOTE]
> Só é cobrado quando o seu Streaming Endpoint está em funcionamento.
    
## <a name="prerequisites"></a>Pré-requisitos

Comentário: 

* [Conceitos de Serviços de Mídia](concepts-overview.md)
* [Conceito de endpoint de streaming](stream-streaming-endpoint-concept.md)
* [Empacotamento dinâmico](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>Utilizar REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Para obter mais informações, consulte: 

* Inicie uma documentação de referência [streamingEndpoint.](/rest/api/media/streamingendpoints/start)
* Iniciar um ponto final de streaming é uma operação assíncronea. 

    Para obter informações sobre como monitorizar as operações de longo prazo, consulte [operações de longo prazo.](media-services-apis-overview.md)
* Esta [coleção de Carteiro](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) contém exemplos de múltiplas operações REST, incluindo sobre como iniciar um ponto final de streaming.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure 
 
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Vá à sua conta da Azure Media Services.
1. No painel esquerdo, selecione  **Streaming Endpoints**.
1. Selecione o ponto final de streaming que pretende iniciar e, em seguida, selecione **Start**.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Para obter mais informações, consulte [o início do ponto de streaming az ams](/cli/azure/ams/streaming-endpoint#az_ams_streaming_endpointstart).

## <a name="use-sdks"></a>Utilizar SDKs

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Consulte a amostra completa do [código Java.](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Consulte a amostra completa do [código .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Passos seguintes

* [Serviços de Mídia v3 Especificação OpenAPI (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operações de streaming endpoint](/rest/api/media/streamingendpoints)
