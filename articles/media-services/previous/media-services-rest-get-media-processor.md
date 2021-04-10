---
title: Como obter uma instância de processador de mídia usando REST | Microsoft Docs
description: Saiba como criar um componente de processador de mídia para codificar, converter formato, encriptar ou desencriptar conteúdos de mídia para a Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7eb4647ae5ba40688cbf39cbfe8449f59275e7e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014320"
---
# <a name="how-to-get-a-media-processor-instance"></a>Como obter uma instância de processador de mídia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)


## <a name="overview"></a>Descrição Geral

Os processadores de mídia são um componente que lida com uma tarefa específica de processamento de vídeo ou áudio, como codificação, conversão de formato, encriptação ou desencriptação de conteúdos de mídia. Todas as tarefas submetidas aos Serviços de Media requerem que um processador de mídia possa codificar, encriptar ou converter o conteúdo de vídeo ou áudio.

## <a name="azure-media-processors"></a>Processadores de mídia Azure

O seguinte tópico fornece listas de processadores de mídia:

* [Processadores de multimédia de codificação](scenarios-and-availability.md)
* [Processadores de multimédia de análise](scenarios-and-availability.md)

>[!NOTE]
>Ao aceder a entidades nos Serviços de Media, deve definir campos e valores específicos de cabeçalho nos seus pedidos HTTP. Para obter mais informações, consulte [Configuração para Serviços de Mídia REST Desenvolvimento da API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [Aceda à API dos Serviços de Media Azure com autenticação AD Azure](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Obtenha um processador de mídia

A chamada REST seguinte mostra como obter um processador de mídia instância pelo nome (neste caso, **Media Encoder Standard**). 

Pedido:

```console
GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <token>
x-ms-version: 2.19
Host: media.windows.net
```

Resposta:

```console
. . .

{  
   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
Agora que sabe como obter uma instância de processador de mídia, vá ao artigo [Como Codificar um Artigo de Ativo](media-services-rest-get-started.md) que demonstre como usar o Media Encoder Standard para codificar um ativo.

