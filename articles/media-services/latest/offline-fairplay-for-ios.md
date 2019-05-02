---
title: Proteger HLS conteúdo com o offline Apple FairPlay - Azure | Documentos da Microsoft
description: Este tópico fornece uma visão geral e mostra como utilizar os serviços de multimédia do Azure para encriptar dinamicamente o seu conteúdo de HTTP Live Streaming (HLS) com o Apple FairPlay no modo offline.
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
ms.openlocfilehash: 58315e639a54b67bdef6751004b7a768e4f92224
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937505"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay Streaming para iOS 

 Serviços de multimédia do Azure fornece um conjunto de bem projetado [serviços de proteção de conteúdo](https://azure.microsoft.com/services/media-services/content-protection/) que descrevem:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Encriptação de AES-128

Gestão de direitos digitais (DRM) / encriptação Advanced Encryption Standard (AES) de conteúdo é realizada dinamicamente mediante pedido para diversos protocolos de transmissão em fluxo. Serviços de entrega de chave de desencriptação de licença/AES de DRM também são fornecidos pelos serviços de multimédia.

Além de proteger conteúdo para a transmissão online ao longo de vários protocolos de transmissão em fluxo, modo offline para conteúdo protegido também é um recurso solicitado com freqüência. Suporte de modo offline é necessária para os seguintes cenários:

* Reprodução quando a ligação à internet não estiver disponível, tal como durante a viagem.
* Alguns fornecedores de conteúdo poderá não permitir a entrega de licenças DRM além do limite de um país/região. Se quiserem que os utilizadores ver o conteúdo quando estiverem viajando fora o país/região, é necessário o offline download.
* Em alguns países/regiões, disponibilidade de internet e/ou de largura de banda é continua a ser limitada. Os utilizadores podem optar por transferir primeiro para conseguir ver o conteúdo numa resolução que seja alto o suficiente para uma experiência de visualização satisfatória. Neste caso, o problema, normalmente, não é a disponibilidade de rede, mas a largura de banda de rede limitada. Over-the-top (OTT) / fornecedores de plataforma de vídeo online (OVP) peça ao apoio de modo offline.

Este artigo aborda o suporte de modo offline FairPlay Streaming (FPS) que se destina a dispositivos com iOS 10 ou posterior. Esta funcionalidade não é suportada para outras plataformas Apple, como watchOS, tvOS ou Safari no macOS.

## <a name="prerequisites"></a>Pré-requisitos

Antes de implementar offline DRM para o FairPlay num dispositivo iOS 10 e posterior:

* Reveja a proteção de conteúdo online para o FairPlay: 

    - [Requisitos de licença e configuração do Apple FairPlay](fairplay-license-overview.md)
    - [Use DRM dynamic encryption and license delivery service](protect-with-drm.md) (Procedimentos: utilizar a encriptação dinâmica e o serviço de entrega de licenças do DRM)
    - Um exemplo de .NET que inclui a configuração de online FPS de transmissão em fluxo: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Obter o SDK de FPS a partir da rede de programador da Apple. O SDK de FPS contém dois componentes:

    - O SDK de servidor FPS, que contém o módulo de segurança da chave (KSM), exemplos de cliente, uma especificação e um conjunto de vetores de teste.
    - O pacote de implementação FPS, que contém a especificação de função de D, juntamente com instruções sobre como gerar o certificado de FPS, a chave privada do específicas do cliente e a chave secreta da aplicação. Apple emite o pacote de implementação de FPS apenas para fornecedores de conteúdo licenciado.
* Clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Terá de modificar o código na [Encrypt com o DRM usando o .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) para adicionar configurações de FairPlay.  

## <a name="configure-content-protection-in-azure-media-services"></a>Configurar a proteção de conteúdo nos serviços de multimédia do Azure

Na [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) método, efetue o seguinte procedimento:

Anule os comentários do código que configura a opção de política do FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Além disso, anule os comentários do código que adiciona CBCS ContentKeyPolicyOption na lista de ContentKeyPolicyOptions

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

Para ativar o modo offline, criar um StreamingPolicy personalizada e utilizar o respetivo nome durante a criação de um StreamingLocator na [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
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

Agora a sua conta de Media Services é configurada para entregar licenças do FairPlay offline.

## <a name="sample-ios-player"></a>Exemplo iOS Player

O suporte de modo offline do FPS está disponível apenas no iOS 10 e posterior. O SDK de servidor FPS (versão 3.0 ou posterior) contém o documento e o exemplo para o modo offline do FPS. Especificamente, FPS Server SDK (versão 3.0 ou posterior) contém os seguintes dois itens relacionados para o modo offline:

* Documento: "Reprodução offline com FairPlay Streaming e HTTP Live Streaming." Apple, 14 de Setembro de 2016. No SDK de servidor FPS versão 4.0, este documento é intercalado com o documento FPS principal.
* Código de exemplo: Exemplo de HLSCatalog (parte do SDK de servidor da Apple FPS) para o modo offline do FPS no \FairPlay SDK do servidor de transmissão em fluxo versão 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. No aplicativo de exemplo HLSCatalog, os arquivos de código seguintes são utilizados para implementar recursos no modo offline:

    - Arquivo de código AssetPersistenceManager.swift: AssetPersistenceManager é a classe principal nesse exemplo que demonstra como:

        - Gerir fluxos HLS download, como as APIs utilizadas para iniciar e cancelar downloads e eliminar recursos existentes de desativar dispositivos.
        - Monitorize o progresso do download.
    - AssetListTableViewController.swift e AssetListTableViewCell.swift arquivos de código: AssetListTableViewController é a interface principal deste exemplo. Ele fornece uma lista de recursos que o exemplo que pode utilizar para reproduzir, transferir, eliminar ou cancelar um download. 

Estes passos mostram como configurar um leitor de iOS em execução. Partindo do princípio de que começar a partir do exemplo HLSCatalog no SDK de servidor FPS versão 4.0.1, efetue as seguintes alterações de código:

No HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementar o método `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` utilizando o código a seguir. Permitir que "drmUr" ser uma variável atribuída para o URL de HLS.

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

No HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementar o método `requestApplicationCertificate()`. Essa implementação depende quer incorporar o certificado (apenas a chave pública) com o dispositivo ou a alojar o certificado na web. A seguinte implementação utiliza o certificado de aplicativo hospedado utilizado nos exemplos de teste. Permitir que "certUrl" ser uma variável que contém o URL do certificado de aplicação.

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

Para o teste final integrado, o URL de vídeo e o URL de certificado de aplicação são fornecidos na secção "Teste integrado."

No HLSCatalog\Shared\Resources\Streams.plist, adicione o seu URL de vídeo de teste. Para o conteúdo ID da chave, utilize o URL de aquisição de licença do FairPlay com o protocolo de skd como o valor exclusivo.

![Offline FairPlay iOS fluxos de aplicação](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Utilize a sua própria URL de vídeo de teste, o URL de aquisição de licença do FairPlay e o URL de certificado da aplicação, se tiver configurado. Ou pode continuar para a secção seguinte, que contém amostras de teste.

## <a name="integrated-test"></a>Teste integrado

Três exemplos de teste nos serviços de multimédia abrangem os seguintes três cenários:

* FPS protegido, com a faixa de áudio alternativa, áudio e vídeo
* FPS protegido, com vídeo e áudio, mas não faixa de áudio alternativa
* FPS protegidos, com o vídeo apenas e não áudio

Pode encontrar estes exemplos nas [este site de demonstração](https://aka.ms/poc#22), com o certificado de aplicativo correspondente hospedado num aplicativo web do Azure.
Com a versão 3 ou exemplo 4 de versão do SDK de servidor FPS, se uma lista de reprodução mestre contém áudio alternativo, durante o modo offline, desempenha áudio apenas. Por conseguinte, terá de retirar o áudio alternativo. Em outras palavras, os exemplos de segundo e terceiro listados anteriormente a funcionar no modo online e offline. O exemplo listado primeiro desempenha áudio apenas durante o modo offline, enquanto online de transmissão em fluxo funciona corretamente.

## <a name="faq"></a>FAQ

As seguintes perguntas mais frequentes sobre fornecem assistência na resolução de problemas:

- **Por que motivo apenas áudio reproduzir, mas não vídeo durante o modo offline?** Este comportamento parece ser por design da aplicação de exemplo. Quando uma faixa de áudio alternativa está presente (o que é o caso para HLS) durante o modo offline, o iOS 10 e o iOS 11 padrão para a faixa de áudio alternativa. Para compensar esse comportamento para o modo offline do FPS, remova a faixa de áudio alternativa do fluxo. Para fazê-lo em serviços de multimédia, adicione o filtro do manifesto dinâmico "só de áudio = false." Em outras palavras, um URL de HLS termina com .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Por que isso ainda reproduza o som apenas sem vídeo durante o modo offline depois adiciono só de áudio = false?** Consoante o entrega de conteúdos (CDN) cache chave design de rede, o conteúdo pode ser colocado em cache. Limpar a cache.
- **Modo offline do FPS também é suportado no iOS 11 para além do iOS 10?** Sim. Modo offline do FPS é suportado para iOS 10 e o iOS 11.
- **Por que não é possível localizar o documento "Offline reprodução com FairPlay Streaming e HTTP Live Streaming" no SDK de servidor FPS?** Desde FPS servidor SDK versão 4, este documento foi intercalado o "FairPlay Streaming guia de programação."
- **O que é a estrutura do ficheiro transferido/offline em dispositivos iOS?** A estrutura do arquivo baixado num dispositivo iOS é semelhante à seguinte captura de ecrã. O `_keys` armazenamentos de pasta transferida licenças FPS, com um armazenamento de ficheiros para cada anfitrião do serviço de licença. O `.movpkg` pasta armazena conteúdo de áudio e vídeo. A primeira pasta com um nome que termina com um hífen seguido por um numérico contém conteúdo de vídeo. O valor numérico é PeakBandwidth das representações de vídeo. A segunda pasta com um nome que termina com um hífen seguido por 0 contém conteúdo de áudio. A terceira pasta chamada "Data" contém a lista de reprodução do conteúdo FPS mestre. Por fim, boot.xml fornece uma descrição completa da `.movpkg` conteúdo da pasta. 

![Estrutura de ficheiros de aplicação de exemplo de iOS do FairPlay offline](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Um ficheiro de boot.xml de exemplo:

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

## <a name="next-steps"></a>Passos Seguintes

Veja como pode [proteger com AES-128](protect-with-aes128.md)
