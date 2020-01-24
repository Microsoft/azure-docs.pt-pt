---
title: Streaming FairPlay offline para iOS com os serviços de mídia do Azure v3
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
ms.openlocfilehash: 70256046089a59df1de79b78124c5d60fde77080
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705943"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline FairPlay Streaming para iOS com Media Services v3

 A Azure Media Services fornece um conjunto de serviços de proteção de conteúdos bem [concebidos](https://azure.microsoft.com/services/media-services/content-protection/) que cobrem:

- Microsoft PlayReady
- Google Widevine
    
    O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.
- Apple FairPlay
- Encriptação de AES-128

A encriptação de conteúdos (DRM)/Advanced Encryption Standard (AES) é realizada de forma dinâmica a pedido de vários protocolos de streaming. Os serviços de entrega da chave de desencriptação da licença DRM/AES também são fornecidos pelos Serviços de Media.

Além de proteger conteúdos para streaming online através de vários protocolos de streaming, o modo offline para conteúdo protegido também é uma funcionalidade frequentemente solicitada. É necessário suporte para o modo offline para os seguintes cenários:

* Reprodução quando a ligação à internet não está disponível, como durante as viagens.
* Alguns fornecedores de conteúdos podem proibir a entrega de licenças de DRM para além da fronteira entre um país e região. Se os utilizadores quiserem ver conteúdo durante a viagem para fora do país/região, é necessário descarregar offline.
* Em alguns países/regiões, a disponibilidade de internet e/ou largura de banda ainda é limitada. Os utilizadores podem optar por descarregar primeiro para poderem ver conteúdos numa resolução suficientemente alta para uma experiência de visualização satisfatória. Neste caso, o problema normalmente não é a disponibilidade da rede, mas a largura de banda limitada da rede. Os fornecedores de plataformas de vídeo on-line (OTT)/online (OVP) solicitam suporte offline.

Este artigo abrange o suporte de modo offline FairPlay Streaming (FPS) que visa dispositivos que executam o iOS 10 ou mais tarde. Esta funcionalidade não é suportada para outras plataformas da Apple, como watchOS, tvOS ou Safari no macOS.

> [!NOTE]
> O DRM offline é cobrado apenas para fazer uma única solicitação de licença quando você baixa o conteúdo. Quaisquer erros não são cobrados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implementar o DRM offline para FairPlay num dispositivo iOS 10+:

* Reveja a proteção de conteúdos online para fairPlay: 

    - [Requisitos de licença e configuração do Apple FairPlay](fairplay-license-overview.md)
    - [Use DRM dynamic encryption and license delivery service](protect-with-drm.md) (Procedimentos: utilizar a encriptação dinâmica e o serviço de entrega de licenças do DRM)
    - Uma amostra .NET que inclui a configuração do streaming de FPS on-line: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Obtenha o FPS SDK da Rede de Desenvolvedores da Apple. O FPS SDK contém dois componentes:

    - O FPS Server SDK, que contém o Módulo de Segurança Chave (KSM), amostras de clientes, uma especificação e um conjunto de vetores de teste.
    - O Pacote de Implementação fps, que contém a especificação de função D, juntamente com instruções sobre como gerar o Certificado FPS, a chave privada específica do cliente e a chave secreta da aplicação. A Apple emite o Pacote de Implementação fps apenas a fornecedores de conteúdos licenciados.
* Clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Terá de modificar o código em [Encrypt com DRM utilizando .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) para adicionar configurações fairPlay.  

## <a name="configure-content-protection-in-azure-media-services"></a>Configurar a proteção de conteúdo nos serviços de mídia do Azure

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

## <a name="enable-offline-mode"></a>Habilitar o modo offline

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

As seguintes perguntas frequentemente colocadas fornecem assistência com resolução de problemas:

- **Porque é que só o áudio reprodução mas não o vídeo durante o modo offline?** Este comportamento parece ser por design da aplicação de amostra. Quando existe uma faixa de áudio alternativa (o que é o caso do HLS) durante o modo offline, tanto o iOS 10 como o iOS 11 predefinidos para a faixa de áudio alternativa. Para compensar este comportamento do modo offline FPS, retire a faixa de áudio alternativa do fluxo. Para isso nos Serviços de Media, adicione o filtro manifesto dinâmico "audio-only=falso". Por outras palavras, um URL HLS termina com .ism/manifest (formato=m3u8-apl,audio-only=falso). 
- **Porque é que ainda reprodução áudio sem vídeo durante o modo offline depois de eu adicionar áudio-only=falso?** Dependendo do design da chave cache da rede de entrega de conteúdos (CDN), o conteúdo pode ser cached. Purgue a cache.
- **O modo offline FPS também é suportado no iOS 11 para além do iOS 10?** Sim. O modo offline FPS é suportado para iOS 10 e iOS 11.
- **Por que não encontro o documento "Offline Playback with FairPlay Streaming e HTTP Live Streaming" no FPS Server SDK?** Desde a versão 4 do FPS Server SDK, este documento foi fundido no "FairPlay Streaming Programming Guide".
- **Qual é a estrutura de ficheiros descarregado/offline nos dispositivos iOS?** A estrutura de ficheiros descarregada num dispositivo iOS parece ser a seguinte imagem. As lojas de pastas `_keys` descarregaram licenças FPS, com um ficheiro de loja para cada anfitrião do serviço de licença. A pasta `.movpkg` armazena conteúdo sonoro e vídeo. A primeira pasta com um nome que termina com um traço seguido de um numérico contém conteúdo de vídeo. O valor numérico é a largura de PeakBand das representações em vídeo. A segunda pasta com um nome que termina com um traço seguido de 0 contém conteúdo áudio. A terceira pasta denominada "Data" contém a lista de reprodução principal do conteúdo do FPS. Finalmente, boot.xml fornece uma descrição completa do conteúdo da pasta `.movpkg`. 

![Estrutura de ficheiros de amostra seleções iOS da FairPlay offline](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Um ficheiro boot.xml de amostra:

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

## <a name="next-steps"></a>Passos seguintes

Veja como pode [proteger com AES-128](protect-with-aes128.md)
