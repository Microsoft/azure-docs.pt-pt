---
title: Publicar conteúdo de serviços de multimédia do Azure com REST
description: Saiba como criar um localizador de que é utilizado para compilar um URL de transmissão em fluxo. O código usa a REST API.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: cfa3dd5c39626f209fdcba9df567d506cc768bec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884023"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publicar conteúdo de serviços de multimédia do Azure com REST 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Pode transmitir em fluxo uma MP4 definido através da criação de um localizador de transmissão em fluxo OnDemand e criação de um URL de transmissão em fluxo de velocidade de transmissão adaptável. O [codificação de um recurso de](media-services-rest-encode-asset.md) artigo mostra como a codificar numa conjunto de MP4 de velocidade de transmissão adaptável. Se o seu conteúdo é encriptado, configure a política de entrega de elementos (conforme descrito em [isso](media-services-rest-configure-asset-delivery-policy.md) artigo) antes de criar um localizador. 

Também pode utilizar um OnDemand localizador de transmissão em fluxo para criar URLs que apontam para os ficheiros MP4 que podem ser transferidos progressivamente.  

Este artigo mostra como criar um localizador para publicar o elemento e criar um uniforme, MPEG DASH e URLs de transmissão em fluxo HLS de transmissão em fluxo de OnDemand. Ela também mostra frequente para criar URLs de transferência progressiva.

O [seguintes](#types) secção mostra os tipos de enumeração cujos valores são utilizados nas chamadas REST.   

> [!NOTE]
> Ao aceder a entidades nos serviços de multimédia, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Depois de ligar com êxito à https://media.windows.net, receberá um redirecionamento 301 especificando noutro URI de serviços de multimédia. Tem de certificar as chamadas subseqüentes para o URI de novo.

## <a name="create-an-ondemand-streaming-locator"></a>Criar um OnDemand localizador de transmissão em fluxo
Para criar o OnDemand localizador de transmissão em fluxo e obter os URLs, terá de fazer o seguinte:

1. Se o conteúdo é encriptado, defina uma política de acesso.
2. Crie um localizador de transmissão em fluxo de OnDemand.
3. Se planeja transmitir em fluxo, obtenha o ficheiro de manifesto transmissão em fluxo (. ISM) no recurso. 
   
   Se planeia transferir progressivamente, obter os nomes de ficheiros MP4 no elemento. 
4. Crie URLs para o ficheiro de manifesto ou ficheiros MP4. 
5. Não é possível criar um localizador de transmissão em fluxo com um AccessPolicy que inclui a escrita ou eliminar as permissões.

### <a name="create-an-access-policy"></a>Criar uma política de acesso

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se estiver a utilizar sempre os mesmos dias / permissões, por exemplo, políticas para localizadores que pretendam permanecem em vigor durante muito tempo (políticas de não carregamento) de acesso. Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Pedido:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    Host: media.windows.net
    Content-Length: 68

    {"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 311
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
    Server: Microsoft-IIS/8.5
    request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:52:09 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### <a name="create-an-ondemand-streaming-locator"></a>Criar um OnDemand localizador de transmissão em fluxo
Crie o localizador para o ativo especificado e a política de recurso.

Pedido:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    Host: media.windows.net
    Content-Length: 181

    {"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 637
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
    Server: Microsoft-IIS/8.5
    request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:58:37 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Criar URLs de transmissão em fluxo
Utilize o **caminho** valor retornado após a criação do localizador para criar o uniforme, HLS e MPEG DASH URLs. 

Transmissão em fluxo uniforme: **Caminho** + nome de ficheiro de manifesto + "/ manifesto"

exemplo:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Caminho** + nome de ficheiro de manifesto + "/ manifest(format=m3u8-aapl)"

exemplo:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


TRAÇO: **Caminho** + nome de ficheiro de manifesto + "/ manifest(format=mpd-time-csf)"

exemplo:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Criar os URLs de transferência progressiva
Utilize o **caminho** valor retornado após a criação do localizador para criar o URL de transferência progressiva.   

URL: **Caminho** + nome do elemento ficheiro mp4

exemplo:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>Tipos de enumeração
    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Descrição geral de REST API de operações de serviços de multimédia](media-services-rest-how-to-use.md)

[Configurar a política de entrega de elemento](media-services-rest-configure-asset-delivery-policy.md)

