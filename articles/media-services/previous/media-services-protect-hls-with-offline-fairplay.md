---
title: Proteger o conteúdo do HLS com o Apple FairPlay offline – Azure | Microsoft Docs
description: Este tópico fornece uma visão geral e mostra como usar os serviços de mídia do Azure para criptografar dinamicamente seu conteúdo de HTTP Live Streaming (HLS) com o Apple FairPlay no modo offline.
services: media-services
keywords: HLS, DRM, FairPlay streaming (FPS), offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 1644c00aea8eefa78550c8d0238dbedab0378492
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968703"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Streaming de FairPlay offline para iOS 

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 3](../latest/offline-fairplay-for-ios.md)
> * [Versão 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Os serviços de mídia do Azure fornecem um conjunto de [serviços de proteção de conteúdo](https://azure.microsoft.com/services/media-services/content-protection/) bem projetados que abrangem:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Encriptação de AES-128

A criptografia de conteúdo do/Advanced (gerenciamento de direitos digitais) do padrão de criptografia (DRM) é executada dinamicamente após a solicitação de vários protocolos de streaming. Os serviços de distribuição de chaves de descriptografia de licença DRM/AES também são fornecidos pelos serviços de mídia.

Além de proteger o conteúdo para streaming online em vários protocolos de streaming, o modo offline para conteúdo protegido também é um recurso solicitado com frequência. O suporte ao modo offline é necessário para os seguintes cenários:

* Reprodução quando a conexão com a Internet não está disponível, como durante viagens.
* Alguns provedores de conteúdo podem impedir a entrega de licença do DRM além da borda de um país/região. Se os usuários desejarem assistir ao conteúdo durante a viagem fora do país/região, o download offline será necessário.
* Em alguns países/regiões, a disponibilidade e/ou a largura de banda da Internet ainda é limitada. Os usuários podem optar por baixar primeiro para poder assistir o conteúdo em uma resolução que seja alta o suficiente para uma experiência de exibição satisfatória. Nesse caso, o problema normalmente não é a disponibilidade da rede, mas largura de banda de rede limitada. Os provedores de OVP (OTT)/online (plataforma de vídeo) para as partes superiores solicitam suporte de modo offline.

Este artigo aborda o suporte ao modo offline de FairPlay streaming (FPS) que visa dispositivos que executam o iOS 10 ou posterior. Esse recurso não tem suporte para outras plataformas da Apple, como watchOS, tvOS ou Safari no macOS.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de implementar o DRM offline para FairPlay em um dispositivo iOS 10 +:

* Familiarize-se com a proteção de conteúdo online para FairPlay. Para obter mais informações, consulte os seguintes artigos e exemplos:

    - [O Apple FairPlay streaming para serviços de mídia do Azure está disponível](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Proteja seu conteúdo do HLS com o Apple FairPlay ou o Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Um exemplo para streaming de FPS online](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Obtenha o SDK de FPS da rede de desenvolvedores da Apple. O SDK de FPS contém dois componentes:

    - O SDK do servidor de FPS, que contém o módulo de segurança de chave (KSM), exemplos de cliente, uma especificação e um conjunto de vetores de teste.
    - O pacote de implantação de FPS, que contém a especificação de função D, juntamente com instruções sobre como gerar o certificado de FPS, a chave privada específica do cliente e a chave secreta do aplicativo. A Apple emite o pacote de implantação de FPS somente para provedores de conteúdo licenciados.

## <a name="configuration-in-media-services"></a>Configuração nos serviços de mídia
Para configuração de modo offline de FPS por meio do [SDK do .net dos serviços de mídia](https://www.nuget.org/packages/windowsazure.mediaservices), use o SDK do .net dos serviços de mídia versão 4.0.0.4 ou posterior, que fornece a API necessária para configurar o modo offline de fps.
Você também precisa do código de trabalho para configurar a proteção de conteúdo do modo online de FPS. Depois de obter o código para configurar a proteção de conteúdo em modo online para FPS, você precisará apenas das duas alterações a seguir.

## <a name="code-change-in-the-fairplay-configuration"></a>Alteração de código na configuração FairPlay
A primeira alteração é definir um booliano "Habilitar modo offline", chamado objDRMSettings. EnableOfflineMode, que é verdadeiro quando habilita o cenário de DRM offline. Dependendo desse indicador, faça a seguinte alteração na configuração do FairPlay:

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
A segunda alteração é adicionar a terceira chave ao Dictionary < AssetDeliveryPolicyConfigurationKey, Cadeia de caracteres >.
Adicione AssetDeliveryPolicyConfigurationKey conforme mostrado aqui:
 
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

Após essa etapa, o < Dictionary_AssetDeliveryPolicyConfigurationKey > cadeia de caracteres na política de entrega de ativos de FPS contém as três entradas a seguir:

* AssetDeliveryPolicyConfigurationKey. FairPlayBaseLicenseAcquisitionUrl ou AssetDeliveryPolicyConfigurationKey. FairPlayLicenseAcquisitionUrl, dependendo de fatores como o servidor de FPS KSM/Key usado e se você reutiliza a mesma política de entrega de ativos em vários ativos
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Agora sua conta de serviços de mídia está configurada para fornecer licenças FairPlay offline.

## <a name="sample-ios-player"></a>Player iOS de exemplo
O suporte ao modo offline de FPS está disponível somente no iOS 10 e posterior. O SDK do servidor de FPS (versão 3,0 ou posterior) contém o documento e o exemplo para o modo offline de FPS. Especificamente, o SDK do servidor de FPS (versão 3,0 ou posterior) contém os dois itens a seguir relacionados ao modo offline:

* Documento: "reprodução offline com FairPlay streaming e HTTP Live Streaming". Apple, 14 de setembro de 2016. No SDK do servidor de FPS versão 4,0, este documento é mesclado no documento de FPS principal.
* Exemplo de código: exemplo de HLSCatalog para o modo offline de FPS no \FairPlay Streaming Server SDK versão 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. No aplicativo de exemplo HLSCatalog, os seguintes arquivos de código são usados para implementar recursos de modo offline:

    - Arquivo de código AssetPersistenceManager. Swift: AssetPersistenceManager é a classe principal neste exemplo que demonstra como:

        - Gerencie o download de fluxos de HLS, como as APIs usadas para iniciar e cancelar downloads e para excluir ativos existentes de dispositivos.
        - Monitore o progresso do download.
    - Arquivos de código AssetListTableViewController. Swift e AssetListTableViewCell. Swift: AssetListTableViewController é a interface principal deste exemplo. Ele fornece uma lista de ativos que o exemplo pode usar para reproduzir, baixar, excluir ou cancelar um download. 

Estas etapas mostram como configurar um player iOS em execução. Supondo que você comece do exemplo HLSCatalog na versão 4.0.1 do SDK do servidor de FPS, faça as seguintes alterações de código:

No HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` usando o código a seguir. Deixe que "drmUr" seja uma variável atribuída à URL HLS.

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

No HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente o método `requestApplicationCertificate()`. Essa implementação depende se você inserir o certificado (chave pública somente) com o dispositivo ou hospedar o certificado na Web. A implementação a seguir usa o certificado de aplicativo hospedado usado nos exemplos de teste. Deixe que "certUrl" seja uma variável que contém a URL do certificado do aplicativo.

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

Para o teste integrado final, tanto a URL do vídeo quanto a URL do certificado do aplicativo são fornecidas na seção "teste integrado".

Em HLSCatalog\Shared\Resources\Streams.plist, adicione a URL de vídeo de teste. Para a ID de chave de conteúdo, use a URL de aquisição de licença do FairPlay com o protocolo de SKD como o valor exclusivo.

![Fluxos offline do aplicativo iOS FairPlay](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Use sua própria URL de vídeo de teste, URL de aquisição de licença FairPlay e URL de certificado de aplicativo, se você as tiver configurado. Ou você pode continuar para a próxima seção, que contém exemplos de teste.

## <a name="integrated-test"></a>Teste integrado
Três exemplos de teste nos serviços de mídia abrangem os três cenários a seguir:

* FPS protegidos, com vídeo, áudio e faixa de áudio alternativo
* FPS protegido, com vídeo e áudio, mas sem faixa de áudio alternativa
* FPS protegido, com vídeo apenas e sem áudio

Você pode encontrar esses exemplos neste [site de demonstração](https://aka.ms/poc#22), com o certificado de aplicativo correspondente hospedado em um aplicativo Web do Azure.
Com o exemplo da versão 3 ou 4 do SDK do servidor de FPS, se uma lista de reprodução mestre contiver áudio alternativo, durante o modo offline, ele só tocará áudio. Portanto, você precisa remover o áudio alternativo. Em outras palavras, a segunda e a terceira amostra listadas anteriormente funcionam no modo online e offline. O exemplo listado primeiro Reproduz áudio somente durante o modo offline, enquanto o streaming online funciona corretamente.

## <a name="faq"></a>FAQ
As perguntas frequentes a seguir fornecem assistência com a solução de problemas:

- **Por que apenas o áudio toca, mas não o vídeo durante o modo offline?** Esse comportamento parece ser por design do aplicativo de exemplo. Quando uma faixa de áudio alternativa está presente (que é o caso de HLS) durante o modo offline, o iOS 10 e o iOS 11 têm como padrão a faixa de áudio alternativa. Para compensar esse comportamento para o modo offline de FPS, remova a faixa de áudio alternativa do fluxo. Para fazer isso nos serviços de mídia, adicione o filtro de manifesto dinâmico "áudio-only = false". Em outras palavras, uma URL HLS termina com. ISM/manifest (Format = M3U8-AAPL, áudio-only = false). 
- **Por que ele ainda tocará áudio somente sem vídeo durante o modo offline depois de adicionar somente áudio = falso?** Dependendo do design da chave de cache da CDN (rede de distribuição de conteúdo), o conteúdo pode ser armazenado em cache. Limpar o cache.
- **O modo de FPS offline também tem suporte no iOS 11, além do iOS 10?** Sim. O modo offline de FPS tem suporte para iOS 10 e iOS 11.
- **Por que não consigo encontrar o documento "reprodução offline com FairPlay streaming e HTTP Live Streaming" no SDK do servidor de FPS?** Desde o SDK do servidor de FPS versão 4, este documento foi mesclado no "guia de programação do FairPlay streaming".
- O **que o último parâmetro tem na seguinte API para o modo offline de FPS?** 
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Para obter a documentação para essa API, consulte o [método FairPlayConfiguration. CreateSerializedFairPlayOptionConfiguration](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). O parâmetro representa a duração do aluguel offline, com hora como a unidade.
- **O que é a estrutura de arquivo baixada/offline em dispositivos iOS?** A estrutura de arquivo baixada em um dispositivo iOS é semelhante à captura de tela a seguir. A pasta `_keys` armazena licenças de FPS baixadas, com um arquivo de armazenamento para cada host de serviço de licença. A pasta `.movpkg` armazena conteúdo de áudio e vídeo. A primeira pasta com um nome que termina com um traço seguido por um numérico contém conteúdo de vídeo. O valor numérico é o PeakBandwidth das representações de vídeo. A segunda pasta com um nome que termina com um traço seguido por 0 contém o conteúdo de áudio. A terceira pasta chamada "data" contém a lista de reprodução mestre do conteúdo de FPS. Por fim, o boot. XML fornece uma descrição completa do conteúdo da pasta `.movpkg`. 

![Estrutura de arquivos de aplicativo de exemplo do iOS FairPlay offline](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Um exemplo de arquivo boot. xml:
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

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="summary"></a>Resumo
Este documento inclui as seguintes etapas e informações que você pode usar para implementar o modo offline de FPS:

* A configuração de proteção de conteúdo dos serviços de mídia por meio da API .NET dos serviços de mídia configura a FairPlay de criptografia e a entrega de licença do FairPlay nos serviços de mídia.
* Um player iOS baseado no exemplo do SDK do servidor de FPS configura um player iOS que pode reproduzir conteúdo de FPS no modo de streaming online ou offline.
* Vídeos de FPS de exemplo são usados para testar o modo offline e streaming online.
* Perguntas FREQUENTEs respondem perguntas sobre o modo offline de FPS.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]