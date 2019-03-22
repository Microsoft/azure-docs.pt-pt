---
title: Proteger HLS conteúdo com o offline Apple FairPlay - Azure | Documentos da Microsoft
description: Este tópico fornece uma visão geral e mostra como utilizar os serviços de multimédia do Azure para encriptar dinamicamente o seu conteúdo de HTTP Live Streaming (HLS) com o Apple FairPlay no modo offline.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: a0e3b0758f8c70bec14d533ef62ce75f57a198cb
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259657"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay Streaming para iOS 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versão 3](../latest/offline-fairplay-for-ios.md)
> * [Versão 2](media-services-protect-hls-with-offline-fairplay.md)

 Serviços de multimédia do Azure fornece um conjunto de bem projetado [serviços de proteção de conteúdo](https://azure.microsoft.com/services/media-services/content-protection/) que descrevem:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Encriptação AES-128

Gestão de direitos digitais (DRM) / encriptação Advanced Encryption Standard (AES) de conteúdo é realizada dinamicamente mediante pedido para diversos protocolos de transmissão em fluxo. Serviços de entrega de chave de desencriptação de licença/AES de DRM também são fornecidos pelos serviços de multimédia.

Além de proteger conteúdo para a transmissão online ao longo de vários protocolos de transmissão em fluxo, modo offline para conteúdo protegido também é um recurso solicitado com freqüência. Suporte de modo offline é necessária para os seguintes cenários:

* Reprodução quando a ligação à internet não estiver disponível, tal como durante a viagem.
* Alguns fornecedores de conteúdo poderá não permitir a entrega de licenças DRM além do limite de um país. Se quiserem que os utilizadores ver o conteúdo quando estiverem viajando fora do seu país, é necessário o offline download.
* Em alguns países, disponibilidade de internet e/ou de largura de banda é continua a ser limitada. Os utilizadores podem optar por transferir primeiro para conseguir ver o conteúdo numa resolução que seja alto o suficiente para uma experiência de visualização satisfatória. Neste caso, o problema, normalmente, não é a disponibilidade de rede, mas a largura de banda de rede limitada. Over-the-top (OTT) / fornecedores de plataforma de vídeo online (OVP) peça ao apoio de modo offline.

Este artigo aborda o suporte de modo offline FairPlay Streaming (FPS) que se destina a dispositivos com iOS 10 ou posterior. Esta funcionalidade não é suportada para outras plataformas Apple, como watchOS, tvOS ou Safari no macOS.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de implementar offline DRM para o FairPlay num dispositivo iOS 10 e posterior:

* Familiarize-se com a proteção de conteúdo online para o FairPlay. Para obter mais informações, consulte os artigos e exemplos que se seguem:

    - [Apple FairPlay Streaming para serviços de multimédia do Azure está disponível em geral](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Proteger o seu HLS conteúdo com o Apple FairPlay ou o Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Um exemplo para a transmissão de FPS online](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Obter o SDK de FPS a partir da rede de programador da Apple. O SDK de FPS contém dois componentes:

    - O SDK de servidor FPS, que contém o módulo de segurança da chave (KSM), exemplos de cliente, uma especificação e um conjunto de vetores de teste.
    - O pacote de implementação FPS, que contém a especificação de função de D, juntamente com instruções sobre como gerar o certificado de FPS, a chave privada do específicas do cliente e a chave secreta da aplicação. Apple emite o pacote de implementação de FPS apenas para fornecedores de conteúdo licenciado.

## <a name="configuration-in-media-services"></a>Configuração nos serviços de multimédia
Para a configuração de modo offline FPS através da [SDK .NET dos Media Services](https://www.nuget.org/packages/windowsazure.mediaservices), utilize o SDK de .NET de Media Services versão 4.0.0.4 ou posterior, que fornece a API necessária para configurar o modo offline do FPS.
Também é necessário o código de trabalho para configurar a proteção de conteúdo de FPS no modo online. Depois de obter o código para configurar a proteção de conteúdo de modo online para FPS, precisa apenas as seguintes duas alterações.

## <a name="code-change-in-the-fairplay-configuration"></a>Alteração de código na configuração do FairPlay
A primeira alteração é definir "ativar modo offline" Boolean, chamado objDRMSettings.EnableOfflineMode, o que acontece quando ele permite que o cenário DRM offline. Consoante este indicador, fazer a alteração seguinte na configuração do FairPlay:

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

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Código de alterar a configuração de política de entrega de elementos
A segunda alteração é adicionar a terceira chave no dicionário < AssetDeliveryPolicyConfigurationKey, string >.
Adicione AssetDeliveryPolicyConfigurationKey, conforme mostrado aqui:
 
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

Após este passo, a cadeia de caracteres < Dictionary_AssetDeliveryPolicyConfigurationKey > política de entrega de elementos FPS contém as seguintes entradas de três:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl ou AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, dependendo de fatores, como o servidor de FPS KSM/chave utilizados e se reutilizar a mesmo entrega de ativo política em vários recursos
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Agora a sua conta de Media Services é configurada para entregar licenças do FairPlay offline.

## <a name="sample-ios-player"></a>Exemplo iOS Player
O suporte de modo offline do FPS está disponível apenas no iOS 10 e posterior. O SDK de servidor FPS (versão 3.0 ou posterior) contém o documento e o exemplo para o modo offline do FPS. Especificamente, FPS Server SDK (versão 3.0 ou posterior) contém os seguintes dois itens relacionados para o modo offline:

* Documento: "Reprodução offline com FairPlay Streaming e HTTP Live Streaming." Apple, 14 de Setembro de 2016. No SDK de servidor FPS versão 4.0, este documento é intercalado com o documento FPS principal.
* Código de exemplo: Exemplo de HLSCatalog para o modo offline do FPS no \FairPlay SDK do servidor de transmissão em fluxo versão 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. No aplicativo de exemplo HLSCatalog, os arquivos de código seguintes são utilizados para implementar recursos no modo offline:

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

![Offline FairPlay iOS fluxos de aplicação](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

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
- **O que o último parâmetro significam na API do seguinte para o modo offline do FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Para a documentação para esta API, consulte [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration método](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). O parâmetro representa o período do aluguel de offline, com a hora como a unidade.
- **O que é a estrutura do ficheiro transferido/offline em dispositivos iOS?** A estrutura do arquivo baixado num dispositivo iOS é semelhante à seguinte captura de ecrã. O `_keys` armazenamentos de pasta transferida licenças FPS, com um armazenamento de ficheiros para cada anfitrião do serviço de licença. O `.movpkg` pasta armazena conteúdo de áudio e vídeo. A primeira pasta com um nome que termina com um hífen seguido por um numérico contém conteúdo de vídeo. O valor numérico é PeakBandwidth das representações de vídeo. A segunda pasta com um nome que termina com um hífen seguido por 0 contém conteúdo de áudio. A terceira pasta chamada "Data" contém a lista de reprodução do conteúdo FPS mestre. Por fim, boot.xml fornece uma descrição completa da `.movpkg` conteúdo da pasta. 

![Estrutura de ficheiros de aplicação de exemplo de iOS do FairPlay offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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

## <a name="summary"></a>Resumo
Este documento inclui os seguintes passos e as informações que pode utilizar para implementar o modo offline do FPS:

* Configuração de proteção de conteúdo de serviços de multimédia através da API de .NET de serviços de multimédia configura a encriptação FairPlay dinâmica e a entrega de licenças do FairPlay nos serviços de multimédia.
* Um leitor de iOS com base no exemplo do SDK de servidor FPS configura um leitor de iOS que pode atuar como FPS conteúdo no modo de transmissão em fluxo online ou offline.
* Vídeos de FPS de exemplo são usados para testar o modo offline e online de transmissão em fluxo.
* Perguntas Freqüentes respondem a perguntas sobre o modo offline do FPS.
