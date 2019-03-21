---
title: Configurar políticas de entrega de elemento com a API de REST de serviços de multimédia | Documentos da Microsoft
description: Este tópico mostra como configurar políticas de entrega de elemento diferente com a API de REST de serviços de multimédia.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 5e4e565b0b5272de19458617a9c4bd3509907cce
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295730"
---
# <a name="configuring-asset-delivery-policies"></a>Configurar políticas de entrega de ativo
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Se planear distribuir ativos encriptados de forma dinâmica, um dos passos no fluxo de trabalho de entrega de conteúdos de serviços de suporte de dados está a configurar políticas de entrega para ativos. A política de entrega de elementos informa os serviços de multimédia que pretende para o seu elemento seja entregue: em qual protocolo de transmissão em fluxo deve seu elemento ser dinamicamente empacotado (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), se pretende encriptar dinamicamente ou não o elemento e como (envelope ou encriptação comum).

Este tópico descreve por que e como criar e configurar políticas de entrega de ativo.

> [!NOTE]
> Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
>
> Além disso, para poder utilizar o empacotamento dinâmico e a encriptação dinâmica seu elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável.

Pode aplicar políticas diferentes para o mesmo elemento. Por exemplo, pode aplicar encriptação PlayReady para encriptação de transmissão em fluxo uniforme e AES Envelope para MPEG DASH e HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é quando não há qualquer política de entrega de elemento definida. Em seguida, todos os protocolos serão permitidos.

Se quiser fornecer um recurso de criptografado de armazenamento, é necessário configurar a política de entrega de elementos. Antes do seu elemento pode ser transmitido em fluxo, o servidor de transmissão em fluxo remove a encriptação de armazenamento e transmite os seus conteúdos através da política de entrega especificado. Por exemplo, para fornecer o seu elemento encriptado com a chave de encriptação de envelope Advanced Encryption Standard (AES), defina o tipo de política como **DynamicEnvelopeEncryption**. Para remover a encriptação de armazenamento e transmitir em fluxo o elemento transparente, defina o tipo de política como **NoDynamicEncryption**. Siga os exemplos que mostram como configurar esses tipos de política.

Dependendo de como configurar a política de entrega de elemento deve ser capaz de dinamicamente o pacote, dinamicamente encriptar e transmitir os seguintes protocolos de transmissão em fluxo: Smooth Streaming, HLS, transmissões em fluxo MPEG DASH.

A lista seguinte mostra os formatos que utilize em fluxo uniforme, HLS, TRAÇO.

Transmissão em fluxo uniforme:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Para obter instruções sobre como publicar um elemento e compilar um URL de transmissão em fluxo, consulte [Compilar um URL de transmissão em fluxo](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Considerações
* Não é possível eliminar um AssetDeliveryPolicy associado a um recurso enquanto um localizador OnDemand (transmissão em fluxo) existe para esse recurso. A recomendação é remove a política de recurso antes de eliminar a política.
* Não é possível criar um localizador de transmissão em fluxo num recurso armazenamento encriptado quando não está definida nenhuma política de entrega de elementos.  Se o elemento não estiver armazenamento encriptado, o sistema lhe permitirá criar um localizador e transmitir em fluxo o elemento de forma sem uma política de entrega de elementos.
* Pode ter várias diretivas de entrega de elemento associadas a um recurso único, mas só pode especificar uma forma de lidar com um determinado AssetDeliveryProtocol.  Ou seja, se tentar ligar duas políticas de entrega que especificar o protocolo de AssetDeliveryProtocol.SmoothStreaming resultará num erro porque o sistema não sabe que aquele que pretende que se aplicam quando um cliente faz um pedido de transmissão em fluxo uniforme.
* Se tiver um recurso com um localizador de transmissão em fluxo existente, não é possível ligar uma nova política para o elemento, desassociar uma política existente do elemento ou atualizar uma política de entrega associada com o elemento.  Primeiro tem de remover o localizador de transmissão em fluxo, ajustar as políticas e, em seguida, voltar a criar o localizador de transmissão em fluxo.  Pode utilizar o mesmo locatorId quando recriar o localizador de transmissão em fluxo, mas deve garantir que não causará problemas para os clientes, uma vez que o conteúdo pode ser colocado em cache a origem ou de uma CDN downstream.

> [!NOTE]
> 
> Ao aceder a entidades nos serviços de multimédia, tem de definir campos de cabeçalho específicas e os valores nos seus pedidos HTTP. Para obter mais informações, consulte [programa de configuração para o desenvolvimento de API de REST do Media Services](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligar aos Media Services

Para obter informações sobre como ligar à AMS API, consulte [aceder a API de serviços de multimédia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Política de entrega de elemento clara
### <a id="create_asset_delivery_policy"></a>Criar política de entrega de elemento
O pedido HTTP seguinte cria uma política de entrega de elemento que especifica para não aplicar a encriptação dinâmica e para disponibilizar o fluxo em qualquer um dos seguintes protocolos:  Protocolos de MPEG DASH, HLS e Smooth Streaming. 

Para informações sobre quais os valores que pode especificar durante a criação de um AssetDeliveryPolicy, consulte a [tipos utilizados para definir a AssetDeliveryPolicy](#types) secção.   

Pedido:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Resposta:

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

### <a id="link_asset_with_asset_delivery_policy"></a>Recurso de ligação com a política de entrega de elemento
O pedido HTTP seguinte liga o elemento especificado para a política de entrega de elementos para.

Pedido:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Resposta:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de elementos de DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Criar chave de conteúdo do tipo EnvelopeEncryption e ligá-lo para o elemento
Ao especificar a política de entrega de DynamicEnvelopeEncryption, terá de certificar-se de que ligar o seu elemento a uma chave de conteúdo do tipo EnvelopeEncryption. Para obter mais informações, consulte: [Criar uma chave de conteúdo](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Obter URL de entrega
Obter o URL de entrega para o método de entrega especificado da chave de conteúdo criado no passo anterior. Um cliente utiliza a URL retornada para solicitar uma chave AES ou um PlayReady de licença por ordem de reproduzir o conteúdo protegido.

Especifique o tipo do URL para se obter no corpo da solicitação HTTP. Se estiver a proteger seu conteúdo com o PlayReady, solicitar um URL de aquisição de licença de PlayReady de serviços de multimédia, através de 1 para o keyDeliveryType: {"keyDeliveryType": 1}. Se estiver a proteger seu conteúdo com a encriptação de envelope, solicitar um URL de aquisição de chave, especificando 2 para keyDeliveryType: {"keyDeliveryType": 2}.

Pedido:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Resposta:

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


### <a name="create-asset-delivery-policy"></a>Criar política de entrega de elemento
O pedido HTTP seguinte cria a **AssetDeliveryPolicy** que está configurada para aplicar a criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) para o **HLS** protocolo (neste exemplo, outros protocolos serão bloqueados da transmissão em fluxo). 

Para informações sobre quais os valores que pode especificar durante a criação de um AssetDeliveryPolicy, consulte a [tipos utilizados para definir a AssetDeliveryPolicy](#types) secção.   

Pedido:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Resposta:

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


### <a name="link-asset-with-asset-delivery-policy"></a>Recurso de ligação com a política de entrega de elemento
Consulte [recurso de ligação com a política de entrega de elemento](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de elementos de DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Criar chave de conteúdo do tipo CommonEncryption e ligá-lo para o elemento
Ao especificar a política de entrega de DynamicCommonEncryption, terá de certificar-se de que ligar o seu elemento a uma chave de conteúdo do tipo CommonEncryption. Para obter mais informações, consulte: [Criar uma chave de conteúdo](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Obter URL de entrega
Obter o URL de entrega para o método de entrega de PlayReady da chave de conteúdo criado no passo anterior. Um cliente utiliza a URL retornada para solicitar uma licença do PlayReady em ordem de reproduzir o conteúdo protegido. Para obter mais informações, consulte [obter URL de entrega](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Criar política de entrega de elemento
O pedido HTTP seguinte cria a **AssetDeliveryPolicy** que está configurada para aplicar encriptação comum dinâmica (**DynamicCommonEncryption**) para o **Smooth Streaming**protocol (neste exemplo, outros protocolos serão bloqueados da transmissão em fluxo). 

Para informações sobre quais os valores que pode especificar durante a criação de um AssetDeliveryPolicy, consulte a [tipos utilizados para definir a AssetDeliveryPolicy](#types) secção.   

Pedido:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Se pretender proteger os seus conteúdos com Widevine DRM, atualize os valores de AssetDeliveryConfiguration para utilizar WidevineLicenseAcquisitionUrl (que tem o valor de 7) e especifique o URL de um serviço de entrega de licença. Pode utilizar os seguintes parceiros de AMS para ajudar a fornecer licenças do Widevine: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Por exemplo: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Ao encriptar com Widevine, só seria capaz de fornecer com DASH. Certifique-se especificar o TRAÇO (2) o protocolo de entrega de elemento.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Recurso de ligação com a política de entrega de elemento
Consulte [recurso de ligação com a política de entrega de elemento](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Tipos de utilizadas para definir a AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

A enumeração seguinte descreve os valores que pode definir para o protocolo de entrega de elemento.

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

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

A enumeração seguinte descreve os valores que pode definir para o tipo de política de entrega de ativo.  

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

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

A enumeração seguinte descreve os valores que pode utilizar para configurar o método de entrega da chave de conteúdo para o cliente.
    
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


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

A enumeração seguinte descreve os valores que pode definir para configurar as chaves utilizadas para obter uma configuração específica para uma política de entrega de elementos.

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

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

