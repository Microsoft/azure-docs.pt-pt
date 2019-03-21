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
ms.author: juliako;cenkdin
ms.openlocfilehash: fc72bad81896d786bfe040632b7f6a61f775b6f1
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294754"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Criar filtros com suporte de dados do Azure REST API dos serviços 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A partir da versão 2.17, serviços de multimédia permite-lhe definir os filtros para os seus ativos. Estes filtros são regras de lado do servidor que permitem que os clientes podem optar por fazer coisas como: reprodução apenas uma secção de um vídeo (em vez de reproduzir o vídeo inteiro), ou especifique apenas um subconjunto de representações de áudio e vídeos que o dispositivo do seu cliente pode manipular (em vez de todas as representações que estão associadas a com elemento). Esta filtragem de seus ativos é arquivado por meio **dinâmica manifestar**s que são criados mediante solicitação do seu cliente para transmitir um vídeo com base nos filtros especificados.

Para obter mais informações relacionadas com filtros e de manifesto dinâmico, consulte [descrição geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

Este artigo mostra como utilizar as APIs REST para criar, atualizar e eliminar filtros. 

## <a name="types-used-to-create-filters"></a>Tipos utilizados para criar filtros
Os seguintes tipos são utilizados durante a criação de filtros:  

* [Filtro](https://docs.microsoft.com/rest/api/media/operations/filter)
* [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect e FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Ao aceder a entidades nos serviços de multimédia, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Criar filtros
### <a name="create-global-filters"></a>Criar filtros globais
Para criar um filtro global, utilize os seguintes pedidos HTTP:  

#### <a name="http-request"></a>Pedido de HTTP
Cabeçalhos de pedido

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




#### <a name="http-response"></a>Resposta de HTTP
    HTTP/1.1 201 Created 

### <a name="create-local-assetfilters"></a>Criar local AssetFilters
Para criar um AssetFilter local, utilize os seguintes pedidos HTTP:  

#### <a name="http-request"></a>Pedido de HTTP
Cabeçalhos de pedido

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

#### <a name="http-response"></a>Resposta de HTTP
    HTTP/1.1 201 Created 
    . . . 

## <a name="list-filters"></a>Lista de filtros
### <a name="get-all-global-filters-in-the-ams-account"></a>Obtenha tudo global **filtro**s na conta do AMS
Para listar os filtros, utilize o seguinte HTTP de pedidos: 

#### <a name="http-request"></a>Pedido de HTTP
    GET https://media.windows.net/API/Filters HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

### <a name="get-assetfilters-associated-with-an-asset"></a>Obtenha **AssetFilter**s associados com um recurso
#### <a name="http-request"></a>Pedido de HTTP
    GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
    Host: media.windows.net 

### <a name="get-an-assetfilter-based-on-its-id"></a>Obter um **AssetFilter** com base no respetivo Id
#### <a name="http-request"></a>Pedido de HTTP
    GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    x-ms-client-request-id: 00000000


## <a name="update-filters"></a>Filtros de atualização
Utilize intercalação, PUT ou PATCH para atualizar um filtro com novos valores de propriedade.  Para obter mais informações sobre estas operações, consulte [PATCH, PUT, MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

Se atualizar um filtro, pode demorar até dois minutos para o ponto final de transmissão em fluxo atualizar as regras. Se o conteúdo tiver sido servido com este filtro (e armazenado em cache em proxies e CDN caches), a atualizar este filtro pode resultar em falhas de player. Limpe a cache depois de atualizar o filtro. Se esta opção não for possível, considere utilizar um filtro de diferente.  

### <a name="update-global-filters"></a>Filtros de atualização global
Para atualizar um filtro global, utilize os seguintes pedidos HTTP: 

#### <a name="http-request"></a>Pedido de HTTP
Cabeçalhos de pedido: 

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

Corpo do pedido: 

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

### <a name="update-local-assetfilters"></a>Atualizar local AssetFilters
Para atualizar um filtro local, utilize os seguintes pedidos HTTP: 

#### <a name="http-request"></a>Pedido de HTTP
Cabeçalhos de pedido: 

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

Corpo do pedido: 

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


## <a name="delete-filters"></a>Eliminar filtros
### <a name="delete-global-filters"></a>Eliminar filtros globais
Para eliminar um filtro global, utilize os seguintes pedidos HTTP:

#### <a name="http-request"></a>Pedido de HTTP
    DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
    DataServiceVersion:3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN>  
    x-ms-version: 2.17 
    Host: media.windows.net 


### <a name="delete-local-assetfilters"></a>Eliminar local AssetFilters
Para eliminar um AssetFilter local, utilize os seguintes pedidos HTTP:

#### <a name="http-request"></a>Pedido de HTTP
    DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
    DataServiceVersion: 3.0 
    MaxDataServiceVersion: 3.0 
    Accept: application/json 
    Accept-Charset: UTF-8 
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17 
    Host: media.windows.net 

## <a name="build-streaming-urls-that-use-filters"></a>Criar a URLs que utilizam filtros de transmissão em fluxo
Para obter informações sobre como publicar e entregar os seus recursos, consulte [entrega de conteúdos para os clientes descrição-geral](media-services-deliver-content-overview.md).

Os exemplos seguintes mostram como adicionar filtros a suas URLs de transmissão em fluxo.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Transmissão em fluxo uniforme**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)

    
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Descrição geral de manifestos dinâmicos](media-services-dynamic-manifest-overview.md)

