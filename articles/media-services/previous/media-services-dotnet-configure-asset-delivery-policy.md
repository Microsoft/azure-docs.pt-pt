---
title: Configurar políticas de entrega de elemento com o .NET SDK | Documentos da Microsoft
description: Este tópico mostra como configurar políticas de entrega de elemento diferente com o SDK de .NET de serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b5e733c93fef8920c73c8cf460dac7a7051fddb5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180675"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurar políticas de entrega de elemento com o .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Descrição geral
Se planeja a recursos de entrega encriptado, uma das etapas do fluxo de trabalho de entrega de conteúdos de serviços de multimédia está a configurar políticas de entrega para ativos. A política de entrega de elementos informa os serviços de multimédia que pretende para o seu elemento seja entregue: em qual protocolo de transmissão em fluxo deve seu elemento ser dinamicamente empacotado (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), se pretende encriptar dinamicamente ou não o elemento e como (envelope ou encriptação comum).

Este artigo aborda o porquê e como criar e configurar políticas de entrega de ativo.

>[!NOTE]
>Quando a sua conta do AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à mesma, que está no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
>
>Além disso, para poder utilizar o empacotamento dinâmico e a encriptação dinâmica seu elemento tem de conter um conjunto de MP4s de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável.

Pode aplicar políticas diferentes para o mesmo elemento. Por exemplo, pode aplicar encriptação PlayReady para encriptação de transmissão em fluxo uniforme e AES Envelope para MPEG DASH e HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é se não tiver nenhuma política de entrega de elementos definida. Em seguida, todos os protocolos serão permitidos.

Se quiser fornecer um recurso de criptografado de armazenamento, é necessário configurar a política de entrega de elementos. Antes do seu elemento pode ser transmitido em fluxo, o servidor de transmissão em fluxo remove a encriptação de armazenamento e transmite os seus conteúdos através da política de entrega especificado. Por exemplo, para fornecer o seu elemento encriptado com a chave de encriptação de envelope Advanced Encryption Standard (AES), defina o tipo de política como **DynamicEnvelopeEncryption**. Para remover a encriptação de armazenamento e transmitir em fluxo o elemento transparente, defina o tipo de política como **NoDynamicEncryption**. Siga os exemplos que mostram como configurar esses tipos de política.

Dependendo de como configurar a política de entrega de elementos, pode dinamicamente o pacote, encriptar e transmitir os seguintes protocolos de transmissão em fluxo: Transmissão em fluxo uniforme, HLS e MPEG DASH.

A lista seguinte mostra os formatos que utilizou para transmitir em fluxo uniforme, HLS e DASH.

Transmissão em fluxo uniforme:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{transmissão em fluxo ponto final dos serviços de multimédia de nome de conta name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Considerações
* Antes de eliminar o AssetDeliveryPolicy, deve eliminar todos os localizadores de transmissão em fluxo associados com o elemento. Mais tarde, pode criar os localizadores de transmissão em fluxo novo, se assim o desejar, com um novo AssetDeliveryPolicy.
* Não é possível criar um localizador de transmissão em fluxo num recurso armazenamento encriptado quando não está definida nenhuma política de entrega de elementos.  Se o elemento não estiver armazenamento encriptado, o sistema lhe permitirá criar um localizador e transmitir em fluxo o elemento de forma sem uma política de entrega de elementos.
* Pode ter várias diretivas de entrega de elemento associadas a um recurso único, mas só pode especificar uma forma de lidar com um determinado AssetDeliveryProtocol.  Ou seja, se tentar ligar duas políticas de entrega que especificar o protocolo de AssetDeliveryProtocol.SmoothStreaming resultará num erro porque o sistema não sabe que aquele que pretende que se aplicam quando um cliente faz um pedido de transmissão em fluxo uniforme.
* Se tiver um recurso com um localizador de transmissão em fluxo existente, não é possível ligar uma nova política para o elemento (pode desassociar a uma política existente do elemento, ou atualizar uma política de entrega associada com o elemento).  Primeiro tem de remover o localizador de transmissão em fluxo, ajustar as políticas e, em seguida, voltar a criar o localizador de transmissão em fluxo.  Pode utilizar o mesmo locatorId quando recriar o localizador de transmissão em fluxo, mas deve garantir que não causará problemas para os clientes, uma vez que o conteúdo pode ser colocado em cache a origem ou de uma CDN downstream.

## <a name="clear-asset-delivery-policy"></a>Política de entrega de elemento clara

O seguinte procedimento **ConfigureClearAssetDeliveryPolicy** método especifica por não aplicar a encriptação dinâmica e para disponibilizar o fluxo em qualquer um dos seguintes protocolos:  Protocolos de MPEG DASH, HLS e Smooth Streaming. Pode querer aplicar esta política aos seus recursos de armazenamento encriptado.

Para informações sobre quais os valores que pode especificar durante a criação de um AssetDeliveryPolicy, consulte a [tipos utilizados para definir a AssetDeliveryPolicy](#types) secção.

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de elementos de DynamicCommonEncryption

O seguinte procedimento **CreateAssetDeliveryPolicy** método cria o **AssetDeliveryPolicy** que está configurada para aplicar encriptação comum dinâmica (**DynamicCommonEncryption**) a um protocolo de transmissão em fluxo uniforme (outros protocolos serão bloqueados da transmissão em fluxo). O método usa dois parâmetros: **Asset** (o elemento ao qual pretende aplicar a política de entrega) e **IContentKey** (a chave de conteúdo da **CommonEncryption** tipo, para obter mais informações, consulte: [Criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para informações sobre quais os valores que pode especificar durante a criação de um AssetDeliveryPolicy, consulte a [tipos utilizados para definir a AssetDeliveryPolicy](#types) secção.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Serviços de multimédia do Azure também permite-lhe adicionar encriptação Widevine. O exemplo seguinte demonstra o PlayReady e Widevine a ser adicionado para a política de entrega de elementos.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> Ao encriptar com Widevine, só seria capaz de fornecer com DASH. Certifique-se especificar o protocolo de entrega de elemento DASH.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de elementos de DynamicEnvelopeEncryption
O seguinte procedimento **CreateAssetDeliveryPolicy** método cria o **AssetDeliveryPolicy** que está configurada para aplicar a criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) para protocolos de transmissão em fluxo uniforme, HLS e travessão (se optar por não especificar alguns protocolos, serão bloqueados da transmissão em fluxo). O método usa dois parâmetros: **Asset** (o elemento ao qual pretende aplicar a política de entrega) e **IContentKey** (a chave de conteúdo da **EnvelopeEncryption** tipo, para obter mais informações, consulte: [Criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Para informações sobre quais os valores que pode especificar durante a criação de um AssetDeliveryPolicy, consulte a [tipos utilizados para definir a AssetDeliveryPolicy](#types) secção.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a id="types"></a>Tipos de utilizadas para definir a AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

A enumeração seguinte descreve os valores que pode definir para o protocolo de entrega de elemento.

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
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

A enumeração seguinte descreve os valores que pode definir para o tipo de política de entrega de ativo.  
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
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

A enumeração seguinte descreve os valores que pode utilizar para configurar o método de entrega da chave de conteúdo para o cliente.
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
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

A enumeração seguinte descreve os valores que pode definir para configurar as chaves utilizadas para obter uma configuração específica para uma política de entrega de elementos.
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
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

