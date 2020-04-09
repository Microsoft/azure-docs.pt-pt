---
title: Offline FairPlay Streaming para iOS com Azure Media Services v3
description: Este tópico apresenta uma visão geral e mostra como usar os Serviços De Mídia Azure para encriptar dinamicamente o seu conteúdo http Live Streaming (HLS) com o Apple FairPlay em modo offline.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 41893c2460ecb2d17e3893f867bc460105d57bbd
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887219"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline FairPlay Streaming para iOS com Media Services v3

 A Azure Media Services fornece um conjunto de serviços de proteção de conteúdos bem [concebidos](https://azure.microsoft.com/services/media-services/content-protection/) que cobrem:

- Microsoft PlayReady
- Google Widevine
    
    A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.
- Apple FairPlay
- Encriptação de AES-128

A encriptação de conteúdos (DRM)/Advanced Encryption Standard (AES) é realizada de forma dinâmica a pedido de vários protocolos de streaming. Os serviços de entrega da chave de desencriptação da licença DRM/AES também são fornecidos pelos Serviços de Media.

Além de proteger conteúdos para streaming online através de vários protocolos de streaming, o modo offline para conteúdo protegido também é uma funcionalidade frequentemente solicitada. É necessário suporte para o modo offline para os seguintes cenários:

* Reprodução quando a ligação à internet não está disponível, como durante as viagens.
* Alguns fornecedores de conteúdos podem proibir a entrega de licenças de DRM para além da fronteira entre um país e região. Se os utilizadores quiserem ver conteúdo durante a viagem para fora do país/região, é necessário descarregar offline.
* Em alguns países/regiões, a disponibilidade de internet e/ou largura de banda ainda é limitada. Os utilizadores podem optar por descarregar primeiro para poderem ver conteúdos numa resolução suficientemente alta para uma experiência de visualização satisfatória. Neste caso, o problema normalmente não é a disponibilidade da rede, mas a largura de banda limitada da rede. Os fornecedores de plataformas de vídeo on-line (OTT)/online (OVP) solicitam suporte offline.

Este artigo abrange o suporte de modo offline FairPlay Streaming (FPS) que visa dispositivos que executam o iOS 10 ou mais tarde. Esta funcionalidade não é suportada para outras plataformas da Apple, como watchOS, tvOS ou Safari no macOS.

> [!NOTE]
> A DRM offline só é cobrada por fazer um único pedido de licença quando descarrega o conteúdo. Quaisquer erros não são contabilizados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implementar o DRM offline para FairPlay num dispositivo iOS 10+:

* Reveja a proteção de conteúdos online para fairPlay: 

    - [Requisitos de licença e configuração do Apple FairPlay](fairplay-license-overview.md)
    - [Utilize encriptação dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
    - Uma amostra .NET que inclui a configuração do streaming de FPS on-line: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Obtenha o FPS SDK da Rede de Desenvolvedores da Apple. O FPS SDK contém dois componentes:

    - O FPS Server SDK, que contém o Módulo de Segurança Chave (KSM), amostras de clientes, uma especificação e um conjunto de vetores de teste.
    - O Pacote de Implementação fps, que contém a especificação de função D, juntamente com instruções sobre como gerar o Certificado FPS, a chave privada específica do cliente e a chave secreta da aplicação. A Apple emite o Pacote de Implementação fps apenas a fornecedores de conteúdos licenciados.
* Clone. https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git 

    Terá de modificar o código em [Encrypt com DRM utilizando .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) para adicionar configurações fairPlay.  

## <a name="configure-content-protection-in-azure-media-services"></a>Configure a proteção de conteúdos nos Serviços De Mídia Azure

No método [GetOrCreateContentKeyPolicyAsync,](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) faça o seguinte:

Não comente o código que configura a opção política FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Além disso, descodere o código que adiciona o CÓDIGO DE Política de Conteúdo CBCS na lista de Opções de Política de Conteúdo

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

Para ativar o modo offline, crie uma Política de Streaming personalizada e use o seu nome ao criar um StreamingLocator em [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Agora a sua conta de Serviços de Media está configurada para fornecer licenças fairPlay offline.

## <a name="sample-ios-player"></a>Jogador de iOS da amostra

O suporte ao modo offline FPS só está disponível no iOS 10 e posteriormente. O FPS Server SDK (versão 3.0 ou posterior) contém o documento e a amostra para o modo offline FPS. Especificamente, o FPS Server SDK (versão 3.0 ou posterior) contém os seguintes dois itens relacionados com o modo offline:

* Documento: "Reprodução offline com FairPlay Streaming e HTTP Live Streaming." Apple, 14 de setembro de 2016. Na versão 4.0 do FPS Server SDK, este documento é fundido no documento principal do FPS.
* Código da amostra: amostra HLSCatalog (parte do FPS Server SDK da Apple) para o modo offline FPS na versão SDK do Servidor de Streaming \FairPlay, versão 3.1\Development\Cliente\HLSCatalog_With_FPS\HLSCatalog\. Na aplicação de amostraHLSCatalog, os seguintes ficheiros de código são utilizados para implementar funcionalidades de modo offline:

    - Ficheiro de código AssetPersistenceManager.swift: AssetPersistenceManager é a classe principal nesta amostra que demonstra como:

        - Gerir o download de streams hls, como as APIs usadas para iniciar e cancelar downloads e eliminar os ativos existentes fora dos dispositivos.
        - Monitorize o progresso do download.
    - Os ficheiros de código AssetListTableViewController.swift e AssetListTableViewCell.swift: AssetListTableViewController é a interface principal desta amostra. Fornece uma lista de ativos que a amostra pode usar para reproduzir, descarregar, excluir ou cancelar um download. 

Estes passos mostram como configurar um leitor de iOS em execução. Assumindo que começa a partir da amostra HLSCatalog na versão 4.0.1 do Servidor FPS, enumere as seguintes alterações de código:

No HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` utilizando o seguinte código. Deixe que "drmUr" seja uma variável atribuída ao URL HLS.

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

No HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestApplicationCertificate()`. Esta implementação depende se você incorpora o certificado (apenas chave pública) com o dispositivo ou hospedar o certificado na web. A seguinte implementação utiliza o certificado de candidatura alojado utilizado nas amostras de ensaio. Deixe que "certUrl" seja uma variável que contenha o URL do certificado de candidatura.

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

No HLSCatalog\Shared\Resources\Streams.plist, adicione o url de vídeo de teste. Para o ID da chave de conteúdo, utilize o URL de aquisição da licença FairPlay com o protocolo skd como o valor único.

![Streams de aplicações offline FairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Utilize o seu próprio URL de vídeo de teste, URL de aquisição de licença FairPlay e URL de certificado de aplicação, se os tiver configurado. Ou pode continuar até à próxima secção, que contém amostras de teste.

## <a name="integrated-test"></a>Teste integrado

Três amostras de ensaio nos Serviços de Media cobrem os três cenários seguintes:

* FPS protegido, com video, áudio e faixa de áudio alternativa
* FPS protegido, com vídeo e áudio, mas sem faixa de áudio alternativa
* FPS protegido, apenas com vídeo e sem áudio

Pode encontrar estas amostras [neste site de demonstração,](https://aka.ms/poc#22)com o certificado de candidatura correspondente hospedado numa aplicação web do Azure.
Com a amostra da versão 3 ou da versão 4 do FPS Server SDK, se uma lista de reprodução principal contiver áudio alternativo, durante o modo offline reproduz apenas áudio. Por isso, é necessário despir o áudio alternativo. Por outras palavras, a segunda e terceira amostras listadas anteriormente funcionam em modo online e offline. A amostra listada reproduz primeiro áudio apenas durante o modo offline, enquanto o streaming online funciona corretamente.

## <a name="faq"></a>FAQ

Consulte [perguntas frequentes que fornecem assistência com resolução](frequently-asked-questions.md#why-does-only-audio-play-but-not-video-during-offline-mode)de problemas .

## <a name="next-steps"></a>Passos seguintes

Veja como pode [proteger com AES-128](protect-with-aes128.md)
