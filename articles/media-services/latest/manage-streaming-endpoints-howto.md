---
title: Gerir pontos finais de streaming com o Azure Media Services v3
description: Este artigo demonstra como gerir os pontos finais de streaming com o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461049"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Gerir pontos finais de streaming com Media Services v3

Quando a sua conta De serviços de media é criada, um [Ponto final de streaming](streaming-endpoint-concept.md) **padrão** é adicionado à sua conta no estado **Deter.** Para começar a transmitir o seu conteúdo e tirar partido de [embalagens dinâmicas](dynamic-packaging-overview.md) e [encriptação dinâmica,](content-protection-overview.md)o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **De Execução.**

Este artigo mostra-lhe como executar o comando [inicial](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) no seu ponto final de streaming usando diferentes tecnologias. 
 
> [!NOTE]
> Só é cobrado quando o seu Streaming Endpoint está em estado de funcionamento.
    
## <a name="prerequisites"></a>Pré-requisitos

Revisão: 

* [Conceitos de Serviços de Media](concepts-overview.md)
* [Conceito de Endpoint de streaming](streaming-endpoint-concept.md)
* [Empacotamento dinâmico](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Utilizar REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Para obter mais informações, consulte: 

* O início de uma documentação de referência [de StreamingEndpoint.](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* Começar um ponto final de streaming é uma operação assíncrona. 

    Para obter informações sobre como monitorizar as operações de longo prazo, consulte [operações de longo prazo](media-services-apis-overview.md).
* Esta [coleção de Carteiro](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) contém exemplos de múltiplas operações REST, incluindo sobre como iniciar um ponto final de streaming.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure 
 
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
1. Vá à sua conta azure media services.
1. No painel esquerdo, selecione **Pontos Finais de Streaming**.
1. Selecione o ponto final de streaming que pretende iniciar e, em seguida, selecione **Iniciar**.

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Para mais informações, consulte [o arranque do streaming az ams](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Utilizar SDKs

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Consulte a amostra completa do [código Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

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

Consulte a amostra completa do [código .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Passos seguintes

* [Media Services v3 OpenAPI Especificação (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operações de Streaming Endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)
