---
title: Configure as políticas de entrega de ativos com .NET SDK / Microsoft Docs
description: Este tópico mostra como configurar diferentes políticas de entrega de ativos com a Azure Media Services .NET SDK.
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
ms.openlocfilehash: ab3c40ee408498453bb137c63c440d980b0b7255
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74974517"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Configure as políticas de entrega de ativos com .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Descrição geral
Se planeia entregar ativos encriptados, uma das etapas do fluxo de trabalho de entrega de conteúdos dos Serviços de Media é configurar políticas de entrega de ativos. A política de entrega de ativos diz aos Media Services como pretende que o seu ativo seja entregue: no qual o protocolo de streaming deve ser embalado dinamicamente (por exemplo, MPEG DASH, HLS, Smooth Streaming, ou todos), quer queira ou não encriptar de forma dinâmica o seu ativo e como (envelope ou encriptação comum).

Este artigo discute porquê e como criar e configurar políticas de entrega de ativos.

>[!NOTE]
>Quando a sua conta AMS é criada, um ponto final de streaming **predefinido** é adicionado à sua conta no estado **Deter.** Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
>
>Além disso, para poder utilizar embalagens dinâmicas e encriptação dinâmica, o seu ativo deve conter um conjunto de MP4s bitrate adaptativos ou ficheiros de streaming suave de bitrate adaptativo.

Pode aplicar políticas diferentes ao mesmo ativo. Por exemplo, pode aplicar encriptação PlayReady ao Smooth Streaming e encriptação do Envelope AES ao MPEG DASH e HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é se não tiver nenhuma política de entrega de elementos definida. Em seguida, todos os protocolos serão permitidos.

Se pretender entregar um ativo encriptado de armazenamento, tem de configurar a política de entrega do ativo. Antes de o seu ativo poder ser transmitido, o servidor de streaming remove a encriptação de armazenamento e transmite o seu conteúdo utilizando a política de entrega especificada. Por exemplo, para entregar o seu ativo encriptado com a chave de encriptação do envelope Advanced Encryption Standard (AES), defino o tipo de política para **DynamicEnvelopeEncryption**. Para remover a encriptação do armazenamento e transmitir o ativo de forma clara, delineie o tipo de política para **NoDynamicEncryption**. Exemplos que mostram como configurar estes tipos de políticas seguem.

Dependendo da configuração da política de entrega de ativos, pode embalar, encriptar e transmitir os seguintes protocolos de streaming: Smooth Streaming, HLS e MPEG DASH.

A lista que se segue mostra os formatos que utiliza para transmitir Smooth, HLS e DASH.

Streaming suave:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Considerações
* Antes de eliminar a Política de Entrega de Ativos, deve eliminar todos os localizadores de streaming associados ao ativo. Mais tarde, poderá criar novos localizadores de streaming, se desejar, com uma nova AssetDeliveryPolicy.
* Um localizador de streaming não pode ser criado num ativo encriptado de armazenamento quando não é definida nenhuma política de entrega de ativos.  Se o Ativo não estiver encriptado, o sistema permitirá criar um localizador e transmitir o ativo de forma clara sem uma política de entrega de ativos.
* Pode ter múltiplas políticas de entrega de ativos associadas a um único ativo, mas só pode especificar uma forma de lidar com um determinado Protocolo de Entrega de Ativos.  Ou seja, se tentar ligar duas políticas de entrega que especificam o protocolo AssetDelivery.SmoothStreaming que resultará num erro porque o sistema não sabe qual pretende que ele aplique quando um cliente faz um pedido de Streaming Suave.
* Se tiver um ativo com um localizador de streaming existente, não pode ligar uma nova política ao ativo (pode desvincular uma política existente do ativo, ou atualizar uma política de entrega associada ao ativo).  Primeiro tem de remover o localizador de streaming, ajustar as políticas e, em seguida, recriar o localizador de streaming.  Pode utilizar o mesmo localizadorId quando recriar o localizador de streaming, mas deve certificar-se de que isso não causará problemas aos clientes, uma vez que o conteúdo pode ser cached pela origem ou por um CDN a jusante.

## <a name="clear-asset-delivery-policy"></a>Clara política de entrega de ativos

O seguinte método **ConfigureClearAssetDeliveryPolicy** especifica não aplicar encriptação dinâmica e entregar o fluxo em qualquer um dos seguintes protocolos: MPEG DASH, HLS e Smooth Streaming. Talvez queira aplicar esta política aos seus ativos encriptados de armazenamento.

Para obter informações sobre quais os valores que pode especificar ao criar uma Política de Entrega de Ativos, consulte os Tipos utilizados na definição da secção [AssetDeliveryPolicy.](#types)

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
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicCommonEncryption

O seguinte método **CreateAssetDeliveryPolicy** cria a Política de Entrega de **Ativos** que está configurada para aplicar encriptação comum dinâmica **(DynamicCommonEncryption**) a um protocolo de streaming suave (outros protocolos serão bloqueados do streaming). O método leva dois parâmetros: **Ativo** (o ativo ao qual pretende aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **CommonEncryption,** para mais informações, ver: Criar uma chave de [conteúdo).](media-services-dotnet-create-contentkey.md#common_contentkey)

Para obter informações sobre quais os valores que pode especificar ao criar uma Política de Entrega de Ativos, consulte os Tipos utilizados na definição da secção [AssetDeliveryPolicy.](#types)

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

O Azure Media Services também lhe permite adicionar encriptação Widevine. O exemplo que se segue demonstra que tanto a PlayReady como a Widevine são adicionadas à política de entrega de ativos.

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
> Ao encriptar com a Widevine, só seria possível entregar usando o DASH. Certifique-se de especificar o DASH no protocolo de entrega de ativos.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicEnvelopeEncryption
O seguinte método **CreateAssetDeliveryPolicy** cria a Política de Entrega de **Ativos** que está configurada para aplicar encriptação dinâmica do envelope **(DynamicEnvelopeEncryption**) aos protocolos Smooth Streaming, HLS e DASH (se decidir não especificar alguns protocolos, eles serão bloqueados do streaming). O método leva dois parâmetros: **Ativo** (o ativo ao qual pretende aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **EnvelopeEncryption,** para mais informações, ver: Criar uma chave de [conteúdo).](media-services-dotnet-create-contentkey.md#envelope_contentkey)

Para obter informações sobre quais os valores que pode especificar ao criar uma Política de Entrega de Ativos, consulte os Tipos utilizados na definição da secção [AssetDeliveryPolicy.](#types)   

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

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Tipos utilizados na definição da Política de Entrega de Ativos

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>Protocolo de Entrega de Ativos

O seguinte enum descreve valores que pode definir para o protocolo de entrega de ativos.

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
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>Tipo de Política de Entrega de Ativos

O enum seguinte descreve valores que pode definir para o tipo de política de entrega de ativos.  
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
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>Tipo de entrega de chaves de conteúdo

O enum seguinte descreve valores que pode utilizar para configurar o método de entrega da chave de conteúdo para o cliente.
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
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>Chave de configuração de política de entrega de ativos

O enum seguinte descreve valores que pode definir para configurar teclas usadas para obter uma configuração específica para uma política de entrega de ativos.
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

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

