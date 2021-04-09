---
title: Proteja o conteúdo do HLS com Apple FairPlay offline - Azure | Microsoft Docs
description: Este tópico dá uma visão geral e mostra como usar o Azure Media Services para encriptar dinamicamente o seu conteúdo HTTP Live Streaming (HLS) com o Apple FairPlay em modo offline.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: dwgeo
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d05810da6abfd33ff6605fa7a6f3bfb6a0c202b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009577"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay Streaming for iOS (Transmissão Offline do FairPlay para iOS)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 3](../latest/offline-fairplay-for-ios.md)
> * [Versão 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

A Azure Media Services fornece um conjunto de serviços de proteção de conteúdos bem [concebidos](https://azure.microsoft.com/services/media-services/content-protection/) que cobrem:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Encriptação de AES-128

A encriptação de conteúdos (DRM)/Advanced Encryption Standard (AES) é realizada dinamicamente mediante pedido de vários protocolos de streaming. Os serviços de entrega de chaves de desencriptação DRM/AES também são fornecidos pelos Media Services.

Além de proteger conteúdos para streaming online em vários protocolos de streaming, o modo offline para conteúdos protegidos é também uma funcionalidade frequentemente solicitada. O suporte offline é necessário para os seguintes cenários:

* Reprodução quando a ligação à Internet não estiver disponível, como durante a viagem.
* Alguns fornecedores de conteúdo podem não permitir a entrega de licenças de DRM para além da fronteira entre um país e uma região. Se os utilizadores quiserem ver o conteúdo durante a viagem para fora do país/região, é necessário descarregar offline.
* Em alguns países/regiões, a disponibilidade de internet e/ou largura de banda ainda é limitada. Os utilizadores podem optar por descarregar primeiro para poderem ver conteúdos numa resolução suficientemente alta para uma experiência de visualização satisfatória. Neste caso, o problema normalmente não é a disponibilidade da rede, mas a largura de banda de rede limitada. Os fornecedores de plataformas de vídeo online (OVP) de topo (OTT) solicitam suporte ao modo offline.

Este artigo abrange o suporte offline do FairPlay Streaming (FPS) que visa dispositivos com execução do iOS 10 ou posterior. Esta funcionalidade não é suportada por outras plataformas da Apple, como o watchOS, tvOS ou Safari no macOS.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de implementar DRM offline para FairPlay num dispositivo iOS 10+:

* Familiarize-se com a proteção de conteúdos online para o FairPlay. Para obter mais informações, consulte os seguintes artigos e amostras:

    - [Apple FairPlay Streaming para Azure Media Services está geralmente disponível](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Proteja o seu conteúdo HLS com Apple FairPlay ou Microsoft PlayReady](./media-services-protect-hls-with-fairplay.md)
    - [Uma amostra para streaming de FPS on-line](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Obtenha o FPS SDK da Rede de Desenvolvedores da Apple. O FPS SDK contém dois componentes:

    - O FPS Server SDK, que contém o Módulo de Segurança chave (KSM), amostras de clientes, uma especificação e um conjunto de vetores de teste.
    - O Pacote de Implementação FPS, que contém a especificação de função D, juntamente com instruções sobre como gerar o Certificado FPS, a chave privada específica do cliente e a Chave Secreta de Aplicação. A Apple emite o Pacote de Implementação FPS apenas a fornecedores de conteúdos licenciados.

## <a name="configuration-in-media-services"></a>Configuração em Serviços de Mídia
Para a configuração do modo offline FPS através dos [Serviços de Mídia .NET SDK,](https://www.nuget.org/packages/windowsazure.mediaservices)utilize os Serviços de Mídia .NET SDK versão 4.0.0.4 ou posterior, que fornece a API necessária para configurar o modo offline FPS.
Também precisa do código de trabalho para configurar a proteção de conteúdos FPS em modo on-line. Depois de obter o código para configurar a proteção de conteúdos em modo on-line para FPS, só precisa das duas alterações seguintes.

## <a name="code-change-in-the-fairplay-configuration"></a>Alteração de código na configuração FairPlay
A primeira alteração é definir um "modo offline" Boolean, chamado objDRMSettings.EnableOfflineMode, que é verdade quando permite o cenário de DRM offline. Dependendo deste indicador, faça a seguinte alteração para a configuração FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Alteração de código na configuração da política de entrega de ativos
A segunda alteração é adicionar a terceira chave no Dicionário<AssetDeliveryPolicyConfigurationKey,> de cordas.
Adicione AssetDeliveryPolicyConfigurationKey como mostrado aqui:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Após este passo, a cadeia <Dictionary_AssetDeliveryPolicyConfigurationKey> na política de entrega de ativos FPS contém as seguintes três entradas:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl ou AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, dependendo de fatores como o FPS KSM/servidor chave utilizado e se reutiliza a mesma política de entrega de ativos em vários ativos
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Agora a sua conta de Media Services está configurada para entregar licenças fairPlay offline.

## <a name="sample-ios-player"></a>Leitor de amostras iOS
O suporte ao modo offline FPS só está disponível no iOS 10 e posteriormente. O FPS Server SDK (versão 3.0 ou posterior) contém o documento e a amostra para o modo offline FPS. Especificamente, o FPS Server SDK (versão 3.0 ou posterior) contém os seguintes dois itens relacionados com o modo offline:

* Documento: "Reprodução offline com FairPlay Streaming e HTTP Live Streaming." Apple, 14 de setembro de 2016. Na versão 4.0 do FPS Server SDK, este documento é fundido no documento principal do FPS.
* Código de amostra: Amostra de HLSCatalog para o modo offline FPS na versão 3.1\Development\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. Na aplicação de amostra HLSCatalog, são utilizados os seguintes ficheiros de código para implementar funcionalidades de modo offline:

    - Ficheiro de código .swift AssetPersistenceManager é a classe principal desta amostra que demonstra como:

        - Gerir o download de streams HLS, como as APIs usadas para iniciar e cancelar transferências e eliminar os ativos existentes fora dos dispositivos.
        - Monitorize o progresso do download.
    - AssetListTableViewController.swift e Ficheiros de código AssetListTableViewCell.swift: AssetListTableViewController é a interface principal desta amostra. Fornece uma lista de ativos que a amostra pode usar para reproduzir, transferir, eliminar ou cancelar um download. 

Estes passos mostram como configurar um leitor de iOS em execução. Assumindo que começa a partir da amostra HLSCatalog na versão 4.0.1 do FPS Server SDK, efetua as seguintes alterações de código:

Em HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` utilizando o seguinte código. Deixe que "drmUr" seja uma variável atribuída ao URL HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

No HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestApplicationCertificate()` . Esta implementação depende se incorpora o certificado (apenas na chave pública) com o dispositivo ou se hospeda o certificado na web. A seguinte implementação utiliza o certificado de pedido hospedado utilizado nas amostras de ensaio. Deixe o "certUrl" ser uma variável que contenha o URL do certificado de candidatura.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Para o teste integrado final, tanto o URL de vídeo como o URL do certificado de aplicação são fornecidos na secção "Teste Integrado".

Em HLSCatalog\Shared\Resources\Streams.plist, adicione o URL de vídeo de teste. Para a chave de conteúdo ID, utilize o URL de aquisição de licença FairPlay com o protocolo skd como o valor único.

![Streams de aplicativos offline FairPlay iOS](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Use o seu próprio URL de vídeo de teste, URL de aquisição de licença FairPlay e URL de certificado de aplicação, se os tiver configurado. Ou pode continuar para a próxima secção, que contém amostras de teste.

## <a name="integrated-test"></a>Ensaio integrado
Três amostras de ensaio nos Serviços de Comunicação Social cobrem os seguintes três cenários:

* FPS protegido, com vídeo, áudio e faixa de áudio alternativa
* FPS protegido, com vídeo e áudio, mas sem faixa sonora alternativa
* FPS protegido, apenas com vídeo e sem áudio

Pode encontrar estas amostras [neste site de demonstração,](https://aka.ms/poc#22)com o certificado de aplicação correspondente alojado numa aplicação web Azure.
Com a amostra da versão 3 ou da versão 4 do FPS Server SDK, se uma lista de reprodução principal contiver áudio alternativo, durante o modo offline reproduz apenas áudio. Portanto, é necessário despir o áudio alternativo. Por outras palavras, a segunda e terceira amostras listadas anteriormente funcionam em modo online e offline. A amostra listada pela primeira vez reproduz áudio apenas durante o modo offline, enquanto o streaming on-line funciona corretamente.

## <a name="faq"></a>FAQ
As seguintes perguntas frequentes prestam assistência à resolução de problemas:

- **Porque é que só o áudio reproduz, mas não o vídeo durante o modo offline?** Este comportamento parece ser por design da aplicação da amostra. Quando existe uma faixa de áudio alternativa (o que é o caso do HLS) durante o modo offline, tanto o iOS 10 como o iOS 11 estão por defeito na faixa de áudio alternativa. Para compensar este comportamento para o modo offline FPS, remova a faixa de áudio alternativa do fluxo. Para isso nos Serviços de Media, adicione o filtro manifesto dinâmico "audio-only=falso". Por outras palavras, um URL HLS termina com .ism/manifest (formato=m3u8-aapl,audio-only=falso). 
- **Porque é que ainda reproduz áudio apenas sem vídeo durante o modo offline depois de eu adicionar áudio-only=falso?** Dependendo do design da chave de cache da rede de entrega de conteúdos (CDN), o conteúdo pode ser em cache. Purgue a cache.
- **O modo offline FPS também é suportado no iOS 11, além do iOS 10?** Sim. O modo offline FPS é suportado para iOS 10 e iOS 11.
- **Por que não posso encontrar o documento "Offline Playback with FairPlay Streaming e HTTP Live Streaming" no FPS Server SDK?** Desde a versão 4 do FPS Server SDK, este documento foi fundido no "Guia de Programação de Streaming fairPlay".
- **O que significa o último parâmetro no seguinte modo API para o modo offline FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Para obter a documentação para esta API, consulte [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration Method](/dotnet/api/microsoft.windowsazure.mediaservices.client.fairplay.fairplayconfiguration.createserializedfairplayoptionconfiguration). O parâmetro representa a duração do aluguer offline, com o segundo como unidade.
- **O que é a estrutura de ficheiros descarregado/offline em dispositivos iOS?** A estrutura de ficheiros descarregada num dispositivo iOS parece a seguinte imagem. As `_keys` lojas de pasta descarregaram licenças de FPS, com um ficheiro de loja para cada anfitrião do serviço de licença. A `.movpkg` pasta armazena conteúdo sonoro e de vídeo. A primeira pasta com um nome que termina com um traço seguido de um numérico contém conteúdo de vídeo. O valor numérico é o PeakBandwidth das representações de vídeo. A segunda pasta com um nome que termina com um traço seguido de 0 contém conteúdo sonoro. A terceira pasta denominada "Data" contém a lista principal do conteúdo FPS. Finalmente, boot.xml fornece uma descrição completa do conteúdo da `.movpkg` pasta. 

![Estrutura de ficheiros de aplicativos de amostra de fairPlay offline FairPlay](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Uma amostra boot.xml ficheiro:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="summary"></a>Resumo
Este documento inclui os seguintes passos e informações que pode utilizar para implementar o modo offline FPS:

* A configuração de proteção de conteúdos dos Media Services através do Media Services .NET API configura a encriptação dinâmica fairPlay e a entrega de licenças FairPlay em Media Services.
* Um leitor de iOS baseado na amostra do FPS Server SDK configura um leitor de iOS que pode reproduzir conteúdo FPS no modo de streaming online ou no modo offline.
* Os vídeos FPS da amostra são utilizados para testar o modo offline e o streaming online.
* Um FAQ responde a perguntas sobre o modo offline FPS.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
