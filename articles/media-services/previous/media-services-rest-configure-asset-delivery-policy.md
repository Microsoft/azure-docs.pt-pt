---
title: Configurando políticas de entrega de ativos usando a API REST dos serviços de mídia | Microsoft Docs
description: Este tópico mostra como configurar políticas de entrega de ativos diferentes usando a API REST dos serviços de mídia.
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
ms.openlocfilehash: b1c71a1329b930beea38fe39518914b278f9372d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968413"
---
# <a name="configuring-asset-delivery-policies"></a>Configurando políticas de entrega de ativos
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Se você planeja fornecer ativos criptografados dinamicamente, uma das etapas no fluxo de trabalho de distribuição de conteúdo dos serviços de mídia é configurar políticas de entrega para ativos. A política de entrega de ativos informa aos serviços de mídia como você deseja que seu ativo seja entregue: em qual protocolo de streaming seu ativo deve ser empacotado dinamicamente (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), seja ou não você deseja criptografar dinamicamente seu ativo e como (envelope ou criptografia comum).

Este tópico discute por que e como criar e configurar políticas de entrega de ativos.

> [!NOTE]
> Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
>
> Além disso, para poder usar o empacotamento dinâmico e a criptografia dinâmica, seu ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável.

Você pode aplicar políticas diferentes ao mesmo ativo. Por exemplo, você pode aplicar a criptografia PlayReady à criptografia de envelope Smooth Streaming e AES para MPEG DASH e HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é quando não há qualquer política de entrega de elemento definida. Em seguida, todos os protocolos serão permitidos.

Se você quiser entregar um ativo de armazenamento criptografado, deverá configurar a política de entrega do ativo. Antes que o ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite seu conteúdo usando a política de entrega especificada. Por exemplo, para entregar seu ativo criptografado com a chave de criptografia de envelope criptografia AES (AES), defina o tipo de política como **DynamicEnvelopeEncryption**. Para remover a criptografia de armazenamento e transmitir o ativo em claro, defina o tipo de política como **NoDynamicEncryption**. Seguem exemplos que mostram como configurar esses tipos de política.

Dependendo de como você configura a política de entrega de ativos, você poderá empacotar dinamicamente, criptografar dinamicamente e transmitir os seguintes protocolos de streaming: os fluxos de Smooth Streaming, HLS, MPEG DASH.

A lista a seguir mostra os formatos que você usa para transmitir Smooth, HLS e DASH.

Smooth Streaming:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = M3U8-AAPL)

MPEG DASH

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = MPD-time-CSF)


Para obter instruções sobre como publicar um elemento e compilar um URL de transmissão em fluxo, consulte [Compilar um URL de transmissão em fluxo](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Considerações
* Não é possível excluir um AssetDeliveryPolicy associado a um ativo enquanto existir um localizador OnDemand (streaming) para esse ativo. A recomendação é remover a política do ativo antes de excluir a política.
* Um localizador de streaming não pode ser criado em um ativo de armazenamento criptografado quando nenhuma política de entrega de ativo é definida.  Se o ativo não for um armazenamento criptografado, o sistema permitirá que você crie um localizador e transmita o ativo em claro sem uma política de entrega de ativos.
* Você pode ter várias políticas de entrega de ativos associadas a um único ativo, mas só pode especificar uma maneira de lidar com um determinado AssetDeliveryProtocol.  Significando que se você tentar vincular duas políticas de entrega que especificam o protocolo AssetDeliveryProtocol. SmoothStreaming, que resultará em um erro, pois o sistema não sabe qual deles você deseja aplicar quando um cliente fizer uma solicitação de Smooth Streaming.
* Se você tiver um ativo com um localizador de streaming existente, não poderá vincular uma nova política ao ativo, desvincular uma política existente do ativo ou atualizar uma política de entrega associada ao ativo.  Primeiro, você precisa remover o localizador de streaming, ajustar as políticas e, em seguida, recriar o localizador de streaming.  Você pode usar o mesmo locatorid ao recriar o localizador de streaming, mas deve garantir que não causará problemas para clientes, já que o conteúdo pode ser armazenado em cache pela origem ou por uma CDN downstream.

> [!NOTE]
> 
> Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos em suas solicitações HTTP. Para obter mais informações, consulte [configuração para desenvolvimento da API REST dos serviços de mídia](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ligue-se aos Serviços Multimédia

Para obter informações sobre como se conectar à API do AMS, consulte [acessar a API dos serviços de mídia do Azure com a autenticação do Azure ad](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Limpar política de entrega de ativos
### <a id="create_asset_delivery_policy"></a>Criar política de entrega de ativos
A solicitação HTTP a seguir cria uma política de entrega de ativos que especifica para não aplicar a criptografia dinâmica e para entregar o fluxo em qualquer um dos seguintes protocolos: MPEG DASH, HLS e protocolos Smooth Streaming. 

Para obter informações sobre quais valores você pode especificar ao criar um AssetDeliveryPolicy, consulte os [tipos usados ao definir](#types) a seção AssetDeliveryPolicy.   

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

### <a id="link_asset_with_asset_delivery_policy"></a>Ativo de link com política de entrega de ativos
A solicitação HTTP a seguir vincula o ativo especificado à política de entrega de ativos para.

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


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicEnvelopeEncryption
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Criar a chave de conteúdo do tipo EnvelopeEncryption e vinculá-la ao ativo
Ao especificar a política de entrega DynamicEnvelopeEncryption, você precisa se certificar de vincular seu ativo a uma chave de conteúdo do tipo EnvelopeEncryption. Para obter mais informações, consulte: [criando uma chave de conteúdo](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Obter URL de entrega
Obtenha a URL de entrega para o método de entrega especificado da chave de conteúdo criada na etapa anterior. Um cliente usa a URL retornada para solicitar uma chave AES ou uma licença do PlayReady a fim de reproduzir o conteúdo protegido.

Especifique o tipo da URL a ser obtido no corpo da solicitação HTTP. Se você estiver protegendo seu conteúdo com o PlayReady, solicite uma URL de aquisição de licença do PlayReady dos serviços de mídia usando 1 para o keydeliverytype: {"keydeliverytype": 1}. Se você estiver protegendo seu conteúdo com a criptografia de envelope, solicite uma URL de aquisição de chave especificando 2 para keydeliverytype: {"keydeliverytype": 2}.

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


### <a name="create-asset-delivery-policy"></a>Criar política de entrega de ativos
A solicitação HTTP a seguir cria o **AssetDeliveryPolicy** que está configurado para aplicar a criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) ao protocolo **HLS** (neste exemplo, outros protocolos serão impedidos de streaming). 

Para obter informações sobre quais valores você pode especificar ao criar um AssetDeliveryPolicy, consulte os [tipos usados ao definir](#types) a seção AssetDeliveryPolicy.   

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


### <a name="link-asset-with-asset-delivery-policy"></a>Ativo de link com política de entrega de ativos
Consulte o [ativo de link com a política de entrega de ativos](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicCommonEncryption
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Criar a chave de conteúdo do tipo CommonEncryption e vinculá-la ao ativo
Ao especificar a política de entrega DynamicCommonEncryption, você precisa se certificar de vincular seu ativo a uma chave de conteúdo do tipo CommonEncryption. Para obter mais informações, consulte: [criando uma chave de conteúdo](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Obter URL de entrega
Obtenha a URL de entrega para o método de entrega PlayReady da chave de conteúdo criada na etapa anterior. Um cliente usa a URL retornada para solicitar uma licença do PlayReady a fim de reproduzir o conteúdo protegido. Para obter mais informações, consulte [obter URL de entrega](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Criar política de entrega de ativos
A solicitação HTTP a seguir cria o **AssetDeliveryPolicy** que está configurado para aplicar a**DynamicCommonEncryption**(criptografia comum dinâmica) ao protocolo de **Smooth streaming** (neste exemplo, outros protocolos serão impedidos de streaming). 

Para obter informações sobre quais valores você pode especificar ao criar um AssetDeliveryPolicy, consulte os [tipos usados ao definir](#types) a seção AssetDeliveryPolicy.   

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


Se você quiser proteger seu conteúdo usando o Widevine DRM, atualize os valores de AssetDeliveryConfiguration para usar WidevineLicenseAcquisitionUrl (que tem o valor de 7) e especifique a URL de um serviço de entrega de licença. Você pode usar os seguintes parceiros do AMS para ajudá-lo a fornecer licenças Widevine: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [ezdrm e](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Por exemplo: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> Ao criptografar com Widevine, você só poderá entregar usando DASH. Certifique-se de especificar DASH (2) no protocolo de entrega de ativos.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Ativo de link com política de entrega de ativos
Consulte o [ativo de link com a política de entrega de ativos](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Tipos usados ao definir AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

A enumeração a seguir descreve os valores que você pode definir para o protocolo de entrega de ativos.

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

A enumeração a seguir descreve os valores que você pode definir para o tipo de política de entrega de ativos.  

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

A enumeração a seguir descreve os valores que você pode usar para configurar o método de entrega da chave de conteúdo para o cliente.
    
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

A enumeração a seguir descreve os valores que você pode definir para configurar as chaves usadas para obter uma configuração específica para uma política de entrega de ativos.

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

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

