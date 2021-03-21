---
title: Configurar políticas de entrega de ativos utilizando serviços de mídia REST API | Microsoft Docs
description: Este tópico mostra como configurar diferentes políticas de entrega de ativos usando a API dos Serviços de Mídia.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 65a7b9479dae1826f478ca5e038ef5feb1caefb6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017329"
---
# <a name="configuring-asset-delivery-policies"></a>Configurar políticas de entrega de ativos

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Se planeia entregar ativos encriptados dinamicamente, um dos passos no fluxo de trabalho de entrega de conteúdos dos Media Services está a configurar políticas de entrega de ativos. A política de entrega de ativos diz aos Media Services como pretende que o seu ativo seja entregue: em que protocolo de streaming o seu ativo será embalado dinamicamente (por exemplo, MPEG DASH, HLS, Smooth Streaming, ou tudo), quer queira ou não encriptar dinamicamente o seu ativo e como (envelope ou encriptação comum).

Este tópico discute porquê e como criar e configurar políticas de entrega de ativos.

> [!NOTE]
> Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
>
> Além disso, para poder utilizar embalagens dinâmicas e encriptação dinâmica, o seu ativo deve conter um conjunto de mp4s de bitrate adaptativo ou ficheiros de streaming suaves adaptáveis.

Pode aplicar diferentes políticas ao mesmo ativo. Por exemplo, pode aplicar encriptação PlayReady a encriptação Smooth Streaming e AES Envelope a MPEG DASH e HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é quando não há qualquer política de entrega de elemento definida. Em seguida, todos os protocolos serão permitidos.

Se quiser entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo. Antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação de armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada. Por exemplo, para entregar o seu ativo encriptado com a chave de encriptação padrão de encriptação avançada (AES), desafie o tipo de política para **DynamicEnvelopeEncryption**. Para remover a encriptação de armazenamento e transmitir o ativo de forma clara, desafie o tipo de política para **NoDynamicEncrycryption**. Seguem-se exemplos que mostram como configurar estes tipos de políticas.

Dependendo da configuração da política de entrega de ativos, seria capaz de embalar, encriptar dinamicamente e transmitir os seguintes protocolos de streaming: Smooth Streaming, HLS, MPEG DASH streams.

A lista a seguir mostra os formatos que utiliza para transmitir Smooth, HLS, DASH.

Streaming suave:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Para obter instruções sobre como publicar um elemento e compilar um URL de transmissão em fluxo, consulte [Compilar um URL de transmissão em fluxo](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Considerações
* Não é possível excluir um AssetDeliveryPolicy associado a um ativo enquanto existe um localizador OnDemand (streaming) para esse ativo. A recomendação é retirar a política do ativo antes de eliminar a política.
* Um localizador de streaming não pode ser criado num ativo encriptado de armazenamento quando não é definida nenhuma política de entrega de ativos.  Se o Ativo não estiver encriptado, o sistema permitirá criar um localizador e transmitir o ativo de forma clara sem uma política de entrega de ativos.
* Pode ter várias políticas de entrega de ativos associadas a um único ativo, mas só pode especificar uma forma de lidar com um determinado AssetDeliveryProtocol.  Ou seja, se tentar ligar duas políticas de entrega que especificam o protocolo AssetDeliveryProtocol.SmoothStreaming que resultará num erro porque o sistema não sabe qual pretende que se aplique quando um cliente faz um pedido de Streaming Suave.
* Se tiver um ativo com um localizador de streaming existente, não pode ligar uma nova política ao ativo, desvincular uma política existente do ativo ou atualizar uma política de entrega associada ao ativo.  Primeiro tem de remover o localizador de streaming, ajustar as políticas e, em seguida, recriar o localizador de streaming.  Pode utilizar o mesmo localizadorId quando recriar o localizador de streaming, mas deve garantir que não causará problemas aos clientes, uma vez que o conteúdo pode ser cached pela origem ou por um CDN a jusante.

> [!NOTE]
> 
> Ao aceder a entidades nos Serviços de Media, deve definir campos e valores específicos de cabeçalho nos seus pedidos HTTP. Para obter mais informações, consulte [Configuração para Serviços de Mídia REST Desenvolvimento da API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [Aceda à API dos Serviços de Media Azure com autenticação AD Azure](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Política clara de entrega de ativos
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Criar política de entrega de ativos
O seguinte pedido HTTP cria uma política de entrega de ativos que especifica para não aplicar encriptação dinâmica e para entregar o fluxo em qualquer um dos seguintes protocolos: PROTOCOLOS MPEG DASH, HLS e Smooth Streaming. 

Para obter informações sobre os valores que pode especificar ao criar uma AssetDeliveryPolicy, consulte os Tipos utilizados ao definir a secção [AssetDeliveryPolicy.](#types)   

Pedido:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
Host: media.windows.net

{"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null}
```

Resposta:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 363
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 06:21:27 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
"Name":"Clear Policy",
"AssetDeliveryProtocol":7,
"AssetDeliveryPolicyType":2,
"AssetDeliveryConfiguration":null,
"Created":"2015-02-08T06:21:27.6908329Z",
"LastModified":"2015-02-08T06:21:27.6908329Z"}
```

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Ligue o ativo com a política de entrega de ativos
O seguinte pedido HTTP liga o ativo especificado à política de entrega de ativos.

Pedido:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
Host: media.windows.net

{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}
```

Resposta:

```output
HTTP/1.1 204 No Content
```


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Crie a chave de conteúdo do tipo EnvelopeEncrypation e ligue-a ao ativo
Ao especificar a política de entrega dinâmica Deencriptação DynamicEnvelopeEncryption, tem de se certificar de que liga o seu ativo a uma chave de conteúdo do tipo EnvelopeEncrypation. Para obter mais informações, consulte: [Criar uma chave de conteúdo).](media-services-rest-create-contentkey.md)

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Obtenha URL de entrega
Obtenha o URL de entrega para o método de entrega especificado da chave de conteúdo criada no passo anterior. Um cliente utiliza o URL devolvido para solicitar uma chave AES ou uma licença PlayReady para reproduzir o conteúdo protegido.

Especifique o tipo de URL para entrar no corpo do pedido HTTP. Se estiver a proteger o seu conteúdo com o PlayReady, solicite um URL de aquisição de licença de Media Services PlayReady, utilizando 1 para o keyDeliveryType: {"keyDeliveryType":1}. Se estiver a proteger o seu conteúdo com a encriptação do envelope, solicite um URL de aquisição chave especificando 2 para keyDeliveryType: {"keyDeliveryType":2}.

Pedido:

```console
POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
Content-Type: application/json
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
Host: media.windows.net
Content-Length: 21

{"keyDeliveryType":2}
```

Resposta:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 198
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
request-id: d26f65d2-fe65-4136-8fcf-31545be68377
x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 08 Feb 2015 21:42:30 GMT

{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}
```


### <a name="create-asset-delivery-policy"></a>Criar política de entrega de ativos
O seguinte pedido HTTP cria o **AssetDeliveryPolicy** que está configurado para aplicar encriptação dinâmica do envelope **(DynamicEnvelopeEncryption)** ao protocolo **HLS** (neste exemplo, outros protocolos serão bloqueados do streaming). 

Para obter informações sobre os valores que pode especificar ao criar uma AssetDeliveryPolicy, consulte os Tipos utilizados ao definir a secção [AssetDeliveryPolicy.](#types)   

Pedido:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}
```


Resposta:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 460
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 09 Feb 2015 05:24:38 GMT

{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}
```


### <a name="link-asset-with-asset-delivery-policy"></a>Ligue o ativo com a política de entrega de ativos
Consulte [o ativo Link com a política de entrega de ativos](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Crie a chave de conteúdo do tipo CommonEncrypation e ligue-a ao ativo
Ao especificar a política de entrega dinâmicaCommonEncryption, tem de se certificar de que liga o seu ativo a uma chave de conteúdo do tipo CommonEncrypation. Para obter mais informações, consulte: [Criar uma chave de conteúdo).](media-services-rest-create-contentkey.md)

### <a name="get-delivery-url"></a>Obtenha URL de entrega
Obtenha o URL de entrega para o método de entrega PlayReady da chave de conteúdo criada no passo anterior. Um cliente utiliza o URL devolvido para solicitar uma licença PlayReady para reproduzir o conteúdo protegido. Para mais informações, consulte [o URL de entrega.](#get_delivery_url)

### <a name="create-asset-delivery-policy"></a>Criar política de entrega de ativos
O seguinte pedido HTTP cria o **AssetDeliveryPolicy** que está configurado para aplicar encriptação comum dinâmica **(DynamicCommonEncryption)** ao protocolo **de Streaming Suave** (neste exemplo, outros protocolos serão bloqueados do streaming). 

Para obter informações sobre os valores que pode especificar ao criar uma AssetDeliveryPolicy, consulte os Tipos utilizados ao definir a secção [AssetDeliveryPolicy.](#types)   

Pedido:

```console
POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
Host: media.windows.net

{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}
```


Se pretender proteger o seu conteúdo utilizando o Widevine DRM, atualize os valores de Configuração assetDeliveryconfiguration para utilizar o WidevineLicenseAcquisitionUrl (que tem o valor de 7) e especifique o URL de um serviço de entrega de licenças. Pode utilizar os seguintes parceiros AMS para ajudá-lo a entregar licenças widevine: [Axinom,](https://www.axinom.com) [EZDRM,](https://ezdrm.com/) [castLabs](https://castlabs.com/company/partners/azure/).

Por exemplo: 

```console
{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}
```

> [!NOTE]
> Ao encriptar com Widevine, só seria capaz de entregar usando o DASH. Certifique-se de especificar o DASH (2) no protocolo de entrega do ativo.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Ligue o ativo com a política de entrega de ativos
Consulte [o ativo Link com a política de entrega de ativos](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Tipos utilizados na definição da AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

O enum que se segue descreve valores que pode definir para o protocolo de entrega de ativos.

```csharp
[Flags]
public enum AssetDeliveryProtocol
{
    /// <summary>
    /// No protocols.
    /// </summary>
    None = 0x0,

    /// <summary>
    /// Smooth streaming protocol.
    /// </summary>
    SmoothStreaming = 0x1,

    /// <summary>
    /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
    /// </summary>
    Dash = 0x2,

    /// <summary>
    /// Apple HTTP Live Streaming protocol.
    /// </summary>
    HLS = 0x4,

    ProgressiveDownload = 0x10, 

    /// <summary>
    /// Include all protocols.
    /// </summary>
    All = 0xFFFF
}
```

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

O enum que se segue descreve valores que pode definir para o tipo de política de entrega de ativos.  

```csharp
public enum AssetDeliveryPolicyType
{
    /// <summary>
    /// Delivery Policy Type not set.  An invalid value.
    /// </summary>
    None,

    /// <summary>
    /// The Asset should not be delivered via this AssetDeliveryProtocol. 
    /// </summary>
    Blocked, 

    /// <summary>
    /// Do not apply dynamic encryption to the asset.
    /// </summary>
    /// 
    NoDynamicEncryption,  

    /// <summary>
    /// Apply Dynamic Envelope encryption.
    /// </summary>
    DynamicEnvelopeEncryption,

    /// <summary>
    /// Apply Dynamic Common encryption.
    /// </summary>
    DynamicCommonEncryption
    }
```

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

O enum que se segue descreve valores que pode utilizar para configurar o método de entrega da chave de conteúdo para o cliente.
    
```csharp
public enum ContentKeyDeliveryType
{
    /// <summary>
    /// None.
    ///
    </summary>
    None = 0,

    /// <summary>
    /// Use PlayReady License acquisition protocol
    ///
    </summary>
    PlayReadyLicense = 1,

    /// <summary>
    /// Use MPEG Baseline HTTP key protocol.
    ///
    </summary>
    BaselineHttp = 2,

    /// <summary>
    /// Use Widevine License acquisition protocol
    ///
    </summary>
    Widevine = 3

}
```


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

O enum seguinte descreve valores que pode definir para configurar chaves usadas para obter uma configuração específica para uma política de entrega de ativos.

```csharp
public enum AssetDeliveryPolicyConfigurationKey
{
    /// <summary>
    /// No policies.
    /// </summary>
    None,

    /// <summary>
    /// Exact Envelope key URL.
    /// </summary>
    EnvelopeKeyAcquisitionUrl,

    /// <summary>
    /// Base key url that will have KID=<Guid> appended for Envelope.
    /// </summary>
    EnvelopeBaseKeyAcquisitionUrl,

    /// <summary>
    /// The initialization vector to use for envelope encryption in Base64 format.
    /// </summary>
    EnvelopeEncryptionIVAsBase64,

    /// <summary>
    /// The PlayReady License Acquisition Url to use for common encryption.
    /// </summary>
    PlayReadyLicenseAcquisitionUrl,

    /// <summary>
    /// The PlayReady Custom Attributes to add to the PlayReady Content Header
    /// </summary>
    PlayReadyCustomAttributes,

    /// <summary>
    /// The initialization vector to use for envelope encryption.
    /// </summary>
    EnvelopeEncryptionIV,

    /// <summary>
    /// Widevine DRM acquisition url
    /// </summary>
    WidevineLicenseAcquisitionUrl
}
```

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

