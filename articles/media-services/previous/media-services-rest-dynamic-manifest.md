---
title: REST API dos serviços de criar filtros com o suporte de dados do Azure | Documentos da Microsoft
description: Este tópico descreve como criar filtros para que o cliente possa utilizar secções específicas do fluxo de um fluxo. Serviços de multimédia cria os manifestos dinâmicos para alcançar este seletiva de transmissão em fluxo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewr: cenkdin
ms.openlocfilehash: 29fef3bec90819b252b43491c08e7a5bc2b3d454
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69014906"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Criando filtros com API REST de Serviços de Mídia do Azure 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A partir da versão 2,17, os serviços de mídia permitem que você defina filtros para seus ativos. Esses filtros são regras do lado do servidor que permitem que seus clientes escolham tarefas como: reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro) ou especificar apenas um subconjunto de representações de áudio e vídeo que o dispositivo do seu cliente pode manipular (em vez de todas as representações associadas ao ativo). Essa filtragem de seus ativos é arquivada pormeio de manifestos dinâmicos que são criados na solicitação do cliente para transmitir um vídeo com base nos filtros especificados.

Para obter informações mais detalhadas relacionadas aos filtros e ao manifesto dinâmico, consulte [visão geral](media-services-dynamic-manifest-overview.md)de manifestos dinâmicos.

Este artigo mostra como usar as APIs REST para criar, atualizar e excluir filtros. 

## <a name="types-used-to-create-filters"></a>Tipos usados para criar filtros
Os seguintes tipos são usados ao criar filtros:  

* [Sem](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect e FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos em suas solicitações HTTP. Para obter mais informações, consulte [configuração para desenvolvimento da API REST dos serviços de mídia](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como se conectar à API do AMS, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Criar filtros
### <a name="create-global-filters"></a>Criar filtros globais
Para criar um filtro global, use as seguintes solicitações HTTP:  

#### <a name="http-request"></a>Solicitação HTTP
Cabeçalhos de solicitação

    POST https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host:media.windows.net 

Corpo do pedido 

    {  
       "Name":"GlobalFilter",
       "PresentationTimeRange":{  
          "StartTimestamp":"0",
          "EndTimestamp":"9223372036854775807",
          "PresentationWindowDuration":"12000000000",
          "LiveBackoffDuration":"0",
          "Timescale":"10000000"
       },
       "Tracks":[  
          {  
             "PropertyConditions":
                  [  
                {  
                   "Property":"Type",
                   "Value":"audio",
                   "Operator":"Equal"
                },
                {  
                   "Property":"Bitrate",
                   "Value":"0-2147483647",
                   "Operator":"Equal"
                }
             ]
          }
       ]
    }




#### <a name="http-response"></a>Resposta HTTP
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>Criar AssetFilters local
Para criar um AssetFilter local, use as seguintes solicitações HTTP:  

#### <a name="http-request"></a>Solicitação HTTP
Cabeçalhos de solicitação

    POST https://media.windows.net/API/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net  

Corpo do pedido 

    {   
       "Name":"AssetFilter", 
       "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
       "PresentationTimeRange":{   
          "StartTimestamp":"0", 
          "EndTimestamp":"9223372036854775807", 
          "PresentationWindowDuration":"12000000000", 
          "LiveBackoffDuration":"0", 
          "Timescale":"10000000" 
       }, 
       "Tracks":[   
          {   
             "PropertyConditions": 
                  [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

#### <a name="http-response"></a>Resposta HTTP
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>Listar filtros
### <a name="get-all-global-filters-in-the-ams-account"></a>Obter todos os **filtros**globais na conta do AMS
Para listar filtros, use as seguintes solicitações HTTP: 

#### <a name="http-request"></a>Solicitação HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Obter **AssetFilter**s associados a um ativo
#### <a name="http-request"></a>Solicitação HTTP
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Obter um **AssetFilter** com base em sua ID
#### <a name="http-request"></a>Solicitação HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Atualizar filtros
Use PATCH, PUT ou MERGE para atualizar um filtro com novos valores de propriedade.  Para obter mais informações sobre essas operações, consulte [patch, PUT e Merge](https://msdn.microsoft.com/library/dd541276.aspx).

Se você atualizar um filtro, pode levar até dois minutos para que o ponto de extremidade de streaming atualize as regras. Se o conteúdo foi servido usando esse filtro (e armazenado em cache em proxies e caches CDN), a atualização desse filtro pode resultar em falhas do Player. Limpe o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente.  

### <a name="update-global-filters"></a>Atualizar filtros globais
Para atualizar um filtro global, use as seguintes solicitações HTTP: 

#### <a name="http-request"></a>Solicitação HTTP
Cabeçalhos de solicitação: 

    MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 
    Content-Length: 384

Corpo do Pedido: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 

### <a name="update-local-assetfilters"></a>Atualizar AssetFilters local
Para atualizar um filtro local, use as seguintes solicitações HTTP: 

#### <a name="http-request"></a>Solicitação HTTP
Cabeçalhos de solicitação: 

    MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Content-Type: application/json 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

Corpo do Pedido: 

    { 
       "Tracks":[   
          {   
             "PropertyConditions": 
             [   
                {   
                   "Property":"Type", 
                   "Value":"audio", 
                   "Operator":"Equal" 
                }, 
                {   
                   "Property":"Bitrate", 
                   "Value":"0-2147483647", 
                   "Operator":"Equal" 
                } 
             ] 
          } 
       ] 
    } 


## <a name="delete-filters"></a>Excluir filtros
### <a name="delete-global-filters"></a>Excluir filtros globais
Para excluir um filtro global, use as seguintes solicitações HTTP:

#### <a name="http-request"></a>Solicitação HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.17 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Excluir AssetFilters local
Para excluir um AssetFilter local, use as seguintes solicitações HTTP:

#### <a name="http-request"></a>Solicitação HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Criar URLs de streaming que usam filtros
Para obter informações sobre como publicar e entregar seus ativos, consulte [visão geral sobre como fornecer conteúdo aos clientes](media-services-deliver-content-overview.md).

Os exemplos a seguir mostram como adicionar filtros às suas URLs de streaming.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) v4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) v3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Visão geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md)

