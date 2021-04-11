---
title: Media Services v3 offline FairPlay Streaming para iOS
description: Este tópico dá uma visão geral e mostra como usar o Azure Media Services v3 para encriptar dinamicamente o seu conteúdo HTTP Live Streaming (HLS) com o Apple FairPlay no modo offline.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0412ef303a301873d437e03e91c58c0a2e8e7e15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640729"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Streaming Offline FairPlay para iOS com Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 A Azure Media Services fornece um conjunto de serviços de proteção de conteúdos bem [concebidos](https://azure.microsoft.com/services/media-services/content-protection/) que cobrem:

- Microsoft PlayReady
- Google Widevine
    
    Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.
- Apple FairPlay
- Encriptação de AES-128

A encriptação de conteúdos (DRM)/Advanced Encryption Standard (AES) é realizada dinamicamente mediante pedido de vários protocolos de streaming. Os serviços de entrega de chaves de desencriptação DRM/AES também são fornecidos pelos Media Services.

Além de proteger conteúdos para streaming online em vários protocolos de streaming, o modo offline para conteúdos protegidos é também uma funcionalidade frequentemente solicitada. O suporte offline é necessário para os seguintes cenários:

* Reprodução quando a ligação à Internet não estiver disponível, como durante a viagem.
* Alguns fornecedores de conteúdo podem não permitir a entrega de licenças de DRM para além da fronteira entre um país e uma região. Se os utilizadores quiserem ver o conteúdo durante a viagem para fora do país/região, é necessário descarregar offline.
* Em alguns países/regiões, a disponibilidade de internet e/ou largura de banda ainda é limitada. Os utilizadores podem optar por descarregar primeiro para poderem ver conteúdos numa resolução suficientemente alta para uma experiência de visualização satisfatória. Neste caso, o problema normalmente não é a disponibilidade da rede, mas a largura de banda de rede limitada. Os fornecedores de plataformas de vídeo online (OVP) de topo (OTT) solicitam suporte ao modo offline.

Este artigo abrange o suporte offline do FairPlay Streaming (FPS) que visa dispositivos com execução do iOS 10 ou posterior. Esta funcionalidade não é suportada por outras plataformas da Apple, como o watchOS, tvOS ou Safari no macOS.

> [!NOTE]
> A DRM offline é cobrada apenas para fazer um único pedido de licença quando você descarrega o conteúdo. Quaisquer erros não são cobrados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implementar DRM offline para FairPlay num dispositivo iOS 10+:

* Reveja a proteção de conteúdos on-line para FairPlay: 

    - [Requisitos de licença e configuração do Apple FairPlay](fairplay-license-overview.md)
    - [Utilize encriptação dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
    - Uma amostra .NET que inclui a configuração do streaming de FPS online: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Obtenha o FPS SDK da Rede de Desenvolvedores da Apple. O FPS SDK contém dois componentes:

    - O FPS Server SDK, que contém o Módulo de Segurança chave (KSM), amostras de clientes, uma especificação e um conjunto de vetores de teste.
    - O Pacote de Implementação FPS, que contém a especificação de função D, juntamente com instruções sobre como gerar o Certificado FPS, a chave privada específica do cliente e a Chave Secreta de Aplicação. A Apple emite o Pacote de Implementação FPS apenas a fornecedores de conteúdos licenciados.
* https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.gitClone. 

    Terá de modificar o código em [Criptografo com DRM utilizando .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) para adicionar configurações FairPlay.  

## <a name="configure-content-protection-in-azure-media-services"></a>Configure a proteção de conteúdos nos Serviços Azure Media

No método [GetOrCreateContentKeyPolicyAsync,](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) faça o seguinte:

Descodificá-lo o código que configura a opção política FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Além disso, descodifice o código que adiciona a CBCS ContentKeyPolicyOption na lista de ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Ativar o modo offline

Para ativar o modo offline, crie um StreamingPolicy personalizado e use o seu nome ao criar um StreamingLocator no [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

Agora a sua conta de Media Services está configurada para entregar licenças fairPlay offline.

## <a name="sample-ios-player"></a>Leitor de amostras iOS

O suporte ao modo offline FPS só está disponível no iOS 10 e posteriormente. O FPS Server SDK (versão 3.0 ou posterior) contém o documento e a amostra para o modo offline FPS. Especificamente, o FPS Server SDK (versão 3.0 ou posterior) contém os seguintes dois itens relacionados com o modo offline:

* Documento: "Reprodução offline com FairPlay Streaming e HTTP Live Streaming." Apple, 14 de setembro de 2016. Na versão 4.0 do FPS Server SDK, este documento é fundido no documento principal do FPS.
* Código de amostra: amostra de HLSCatalog (parte do SDK do Servidor FPS da Apple) para o modo offline FPS na versão SDK do servidor de streaming \FairPlay 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. Na aplicação de amostra HLSCatalog, são utilizados os seguintes ficheiros de código para implementar funcionalidades de modo offline:

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

![Streams de aplicativos offline FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Use o seu próprio URL de vídeo de teste, URL de aquisição de licença FairPlay e URL de certificado de aplicação, se os tiver configurado. Ou pode continuar para a próxima secção, que contém amostras de teste.

## <a name="integrated-test"></a>Ensaio integrado

Três amostras de ensaio nos Serviços de Comunicação Social cobrem os seguintes três cenários:

* FPS protegido, com vídeo, áudio e faixa de áudio alternativa
* FPS protegido, com vídeo e áudio, mas sem faixa sonora alternativa
* FPS protegido, apenas com vídeo e sem áudio

Pode encontrar estas amostras [neste site de demonstração,](https://aka.ms/poc#22)com o certificado de aplicação correspondente alojado numa aplicação web Azure.
Com a amostra da versão 3 ou da versão 4 do FPS Server SDK, se uma lista de reprodução principal contiver áudio alternativo, durante o modo offline reproduz apenas áudio. Portanto, é necessário despir o áudio alternativo. Por outras palavras, a segunda e terceira amostras listadas anteriormente funcionam em modo online e offline. A amostra listada pela primeira vez reproduz áudio apenas durante o modo offline, enquanto o streaming on-line funciona corretamente.

## <a name="offline-fairplay-questions"></a>Perguntas de fairplay offline

Consulte [as questões de fairplay offline](questions-collection.md#why-does-only-audio-play-but-not-video-during-offline-mode).
