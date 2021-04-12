---
title: Publique o conteúdo da Azure Media Services usando REST
description: Aprenda a criar um localizador que seja usado para construir um URL de streaming. O código utiliza REST API.
author: IngridAtMicrosoft
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
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: b8733d499b2396160a73906f16a69291cf0b9d71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015425"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publique o conteúdo da Azure Media Services usando REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Pode transmitir um conjunto de MP4 bitrate adaptativo, criando um localizador de streaming OnDemand e construindo um URL de streaming. A [codificação de um](media-services-rest-encode-asset.md) artigo de ativo mostra como codificar num conjunto de MP4 bitrate adaptativo. Se o seu conteúdo estiver encriptado, configuure a política de entrega de ativos (conforme descrito [neste](media-services-rest-configure-asset-delivery-policy.md) artigo) antes de criar um localizador. 

Também pode utilizar um localizador de streaming OnDemand para construir URLs que apontam para ficheiros MP4 que podem ser descarregados progressivamente.  

Este artigo mostra como criar um localizador de streaming OnDemand para publicar o seu ativo e construir um URLs de streaming Smooth, MPEG e HLS. Também mostra como construir URLs de descarregamento progressivo.

A secção [seguinte](#types) mostra os tipos de enum cujos valores são utilizados nas chamadas REST.   

> [!NOTE]
> Ao aceder a entidades nos Serviços de Media, deve definir campos e valores específicos de cabeçalho nos seus pedidos HTTP. Para obter mais informações, consulte [Configuração para Serviços de Mídia REST Desenvolvimento da API](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [Aceda à API dos Serviços de Media Azure com autenticação AD Azure](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>Depois de se ligar com https://media.windows.net sucesso, receberá um redirecionamento 301 especificando outro URI dos Media Services. Tens de fazer chamadas subsequentes para o novo URI.

## <a name="create-an-ondemand-streaming-locator"></a>Criar um localizador de streaming OnDemand
Para criar o localizador de streaming OnDemand e obter URLs, tem de fazer o seguinte:

1. Se o conteúdo estiver encriptado, defina uma política de acesso.
2. Crie um localizador de streaming OnDemand.
3. Se pretender transmitir, obtenha o ficheiro manifesto de streaming (.ism) no ativo. 
   
   Se pretender descarregar progressivamente, obtenha os nomes dos ficheiros MP4 no ativo. 
4. Construa URLs para o ficheiro manifesto ou ficheiros MP4. 
5. Não é possível criar um localizador de streaming utilizando uma AccessPolicy que inclua permissões de escrita ou de eliminação.

### <a name="create-an-access-policy"></a>Criar uma política de acesso

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se estiver sempre a utilizar as mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que se destinam a permanecer no local por muito tempo (políticas de não carregamento). Para mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

Pedido:

```console
POST https://media.windows.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
Host: media.windows.net
Content-Length: 68

{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

Resposta:

```console
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
```

### <a name="create-an-ondemand-streaming-locator"></a>Criar um localizador de streaming OnDemand
Crie o localizador para a política de ativos e ativos especificados.

Pedido:

```console
POST https://media.windows.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
Host: media.windows.net
Content-Length: 181

{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}
```
Resposta:

```console
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
```

### <a name="build-streaming-urls"></a>Construir URLs de streaming
Utilize o valor **Path** devolvido após a criação do localizador para construir os URLs Smooth, HLS e MPEG DASH. 

Streaming Suave: **Caminho** + nome manifesto do ficheiro + "/manifesto"

exemplo:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **Path** + manifesto nome de ficheiro + "/manifest (formato=m3u8-aapl)"

exemplo:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


DASH: **Caminho** + nome manifesto do ficheiro + "/manifesto (formato=mpd-time-csf)"

exemplo:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>Construa URLs de descarregamento progressivo
Utilize o valor **Path** devolvido após a criação do localizador para construir o URL de descarregamento progressivo.   

URL: **Path** + nome mp4 do ficheiro de ativos

exemplo:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Tipos de Enum

```console
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
```

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ver também
[Operações de Serviços de Mídia REST Visão geral da API](media-services-rest-how-to-use.md)

[Configure a política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md)

