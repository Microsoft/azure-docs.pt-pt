---
title: Configurar políticas de entrega de ativos com o SDK do .NET | Microsoft Docs
description: Este tópico mostra como configurar políticas de entrega de ativos diferentes com o SDK do .NET dos serviços de mídia do Azure.
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
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974517"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Configurar políticas de entrega de ativos com o SDK do .NET
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Visão geral
Se você planeja entregar ativos criptografados, uma das etapas no fluxo de trabalho de distribuição de conteúdo dos serviços de mídia está configurando políticas de entrega para ativos. A política de entrega de ativos informa aos serviços de mídia como você deseja que seu ativo seja entregue: em qual protocolo de streaming seu ativo deve ser empacotado dinamicamente (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos), seja ou não você deseja criptografar dinamicamente seu ativo e como (envelope ou criptografia comum).

Este artigo discute por que e como criar e configurar políticas de entrega de ativos.

>[!NOTE]
>Quando a sua conta do AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à mesma, que está no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 
>
>Além disso, para poder usar o empacotamento dinâmico e a criptografia dinâmica, seu ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável.

Você pode aplicar políticas diferentes ao mesmo ativo. Por exemplo, você pode aplicar a criptografia PlayReady à criptografia de envelope Smooth Streaming e AES para MPEG DASH e HLS. Quaisquer protocolos que não estão definidos numa política de entrega (por exemplo, adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é se não tiver nenhuma política de entrega de elementos definida. Em seguida, todos os protocolos serão permitidos.

Se você quiser entregar um ativo de armazenamento criptografado, deverá configurar a política de entrega do ativo. Antes que o ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite seu conteúdo usando a política de entrega especificada. Por exemplo, para entregar seu ativo criptografado com a chave de criptografia de envelope criptografia AES (AES), defina o tipo de política como **DynamicEnvelopeEncryption**. Para remover a criptografia de armazenamento e transmitir o ativo em claro, defina o tipo de política como **NoDynamicEncryption**. Seguem exemplos que mostram como configurar esses tipos de política.

Dependendo de como você configura a política de entrega de ativos, você pode empacotar, criptografar e transmitir dinamicamente os seguintes protocolos de streaming: Smooth Streaming, HLS e MPEG DASH.

A lista a seguir mostra os formatos que você usa para transmitir Smooth, HLS e DASH.

Smooth Streaming:

{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = M3U8-AAPL)

MPEG DASH

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = MPD-time-CSF)

## <a name="considerations"></a>Considerações
* Antes de excluir o AssetDeliveryPolicy, você deve excluir todos os localizadores de streaming associados ao ativo. Posteriormente, você poderá criar novos localizadores de streaming, se desejar, com um novo AssetDeliveryPolicy.
* Um localizador de streaming não pode ser criado em um ativo de armazenamento criptografado quando nenhuma política de entrega de ativo é definida.  Se o ativo não for um armazenamento criptografado, o sistema permitirá que você crie um localizador e transmita o ativo em claro sem uma política de entrega de ativos.
* Você pode ter várias políticas de entrega de ativos associadas a um único ativo, mas só pode especificar uma maneira de lidar com um determinado AssetDeliveryProtocol.  Significando que se você tentar vincular duas políticas de entrega que especificam o protocolo AssetDeliveryProtocol. SmoothStreaming, que resultará em um erro, pois o sistema não sabe qual deles você deseja aplicar quando um cliente fizer uma solicitação de Smooth Streaming.
* Se você tiver um ativo com um localizador de streaming existente, não será possível vincular uma nova política ao ativo (você pode desvincular uma política existente do ativo ou atualizar uma política de entrega associada ao ativo).  Primeiro, você precisa remover o localizador de streaming, ajustar as políticas e, em seguida, recriar o localizador de streaming.  Você pode usar o mesmo locatorid ao recriar o localizador de streaming, mas deve garantir que não causará problemas para clientes, já que o conteúdo pode ser armazenado em cache pela origem ou por uma CDN downstream.

## <a name="clear-asset-delivery-policy"></a>Limpar política de entrega de ativos

O seguinte método **ConfigureClearAssetDeliveryPolicy** especifica a não aplicação da criptografia dinâmica e a entrega do fluxo em qualquer um dos seguintes protocolos: MPEG Dash, HLS e protocolos Smooth streaming. Talvez você queira aplicar essa política aos ativos criptografados de armazenamento.

Para obter informações sobre quais valores você pode especificar ao criar um AssetDeliveryPolicy, consulte os [tipos usados ao definir](#types) a seção AssetDeliveryPolicy.

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

O método **CreateAssetDeliveryPolicy** a seguir cria o **AssetDeliveryPolicy** que é configurado para aplicar a criptografia comum dinâmica (**DynamicCommonEncryption**) a um protocolo Smooth streaming (outros protocolos serão impedidos de streaming). O método usa dois parâmetros: **ativo** (o ativo ao qual você deseja aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **CommonEncryption** , para obter mais informações, consulte: [criando uma chave de conteúdo](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para obter informações sobre quais valores você pode especificar ao criar um AssetDeliveryPolicy, consulte os [tipos usados ao definir](#types) a seção AssetDeliveryPolicy.

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

Os serviços de mídia do Azure também permitem que você adicione criptografia Widevine. O exemplo a seguir demonstra o PlayReady e o Widevine sendo adicionados à política de entrega de ativos.

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
> Ao criptografar com Widevine, você só poderá entregar usando DASH. Certifique-se de especificar DASH no protocolo de entrega de ativos.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Política de entrega de ativos DynamicEnvelopeEncryption
O método **CreateAssetDeliveryPolicy** a seguir cria o **AssetDeliveryPolicy** que é configurado para aplicar a criptografia de envelope dinâmico (**DynamicEnvelopeEncryption**) aos protocolos Smooth streaming, HLS e Dash (se você decidir não especificar alguns protocolos, eles serão impedidos de streaming). O método usa dois parâmetros: **ativo** (o ativo ao qual você deseja aplicar a política de entrega) e **IContentKey** (a chave de conteúdo do tipo **EnvelopeEncryption** , para obter mais informações, consulte: [criando uma chave de conteúdo](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Para obter informações sobre quais valores você pode especificar ao criar um AssetDeliveryPolicy, consulte os [tipos usados ao definir](#types) a seção AssetDeliveryPolicy.   

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

## <a id="types"></a>Tipos usados ao definir AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

A enumeração a seguir descreve os valores que você pode definir para o protocolo de entrega de ativos.

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

A enumeração a seguir descreve os valores que você pode definir para o tipo de política de entrega de ativos.  
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

A enumeração a seguir descreve os valores que você pode usar para configurar o método de entrega da chave de conteúdo para o cliente.
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

A enumeração a seguir descreve os valores que você pode definir para configurar as chaves usadas para obter uma configuração específica para uma política de entrega de ativos.
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

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

