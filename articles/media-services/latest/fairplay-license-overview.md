---
title: Suporte à licença Apple FairPlay dos Media Services
description: Este tópico fornece uma visão geral dos requisitos e configurações de licença apple FairPlay.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 187c1e60d97e0bebb3b6216b0055ddffe6e6cb4c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454386"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Requisitos de licença e configuração do Apple FairPlay

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Azure Media Services permite-lhe encriptar o seu conteúdo HLS com **o Apple FairPlay** (AES-128 CBC). Os Media Services também fornecem um serviço para a entrega de licenças FairPlay. Quando um jogador tenta reproduzir o seu conteúdo protegido pelo FairPlay, é enviado um pedido ao serviço de entrega de licenças para obter uma licença. Se o serviço de licença aprovar o pedido, emite a licença que é enviada ao cliente e é usada para desencriptar e reproduzir o conteúdo especificado.

Os Serviços de Mídia também fornecem APIs que pode utilizar para configurar as suas licenças FairPlay. Este tópico discute os requisitos de licença fairPlay e demonstra como pode configurar uma licença **FairPlay** usando APIs de Serviços de Media. 

## <a name="requirements"></a>Requisitos

São necessários os seguintes ao utilizar os Serviços de Comunicação Social para encriptar o seu conteúdo HLS com **o Apple FairPlay** e utilizar os Serviços de Media para fornecer licenças FairPlay:

* Inscreva-se no [Apple Development Program](https://developer.apple.com/).
* A Apple exige que o proprietário do conteúdo obtenha o [pacote de implementação.](https://developer.apple.com/contact/fps/) Diga que já implementou o Módulo de Segurança Chave (KSM) com os Media Services e que está a solicitar o pacote final de FPS. Existem instruções no pacote final de FPS para gerar certificação e obter a Chave Secreta de Aplicação (ASK). Usa o ASK para configurar o FairPlay.
* As seguintes coisas devem ser definidas na chave dos Serviços de Mídia/lado da entrega da licença:

    * **App Cert (AC)**: Este é um ficheiro .pfx que contém a chave privada. Cria este ficheiro e encripta-o com uma palavra-passe. O ficheiro .pfx deve estar no formato Base64.

        Os seguintes passos descrevem como gerar um ficheiro de certificado .pfx para FairPlay:

        1. Instale o OpenSSL a partir de https://slproweb.com/products/Win32OpenSSL.html .

            Vá à pasta onde estão o certificado FairPlay e outros ficheiros entregues pela Apple.
        2. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro .cer num ficheiro .pem.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -informe der -in FairPlay.cer -out FairPlay-out.pem
        3. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro .pem para um ficheiro .pfx com a chave privada. A palavra-passe para o ficheiro .pfx é então solicitada pela OpenSSL.

            Pkcs12 "C:\OpenSSL-Win32\bin\openssl.exe" -exportação -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **Aplicação Cert password**: A palavra-passe para criar o ficheiro .pfx.
    * **ASK:** Esta chave é recebida quando gera a certificação utilizando o portal Apple Developer. Cada equipa de desenvolvimento recebe um ASK único. Guarde uma cópia do ASK e guarde-a num local seguro. Você precisa configurar ASK como FairPlayAsk com Media Services.
    
* As seguintes coisas devem ser definidas pelo lado do cliente FPS:

  * **App Cert (AC)**: Este é um ficheiro .cer/.der que contém a chave pública, que o sistema operativo utiliza para encriptar alguma carga útil. Os Serviços de Comunicação Social precisam de saber porque é exigido pelo jogador. O serviço de entrega de chaves desencripta-o utilizando a chave privada correspondente.

* Para reproduzir um stream encriptado FairPlay, obtenha um ASK real primeiro e, em seguida, gere um certificado real. Este processo cria as três partes:

  * .der arquivo
  * Arquivo .pfx
  * palavra-passe para o .pfx
  
> [!NOTE]
> A Azure Media Services não verifica a data de validade do certificado durante a embalagem ou entrega de chaves. Continuará a funcionar após o termo do certificado.

## <a name="fairplay-and-player-apps"></a>FairPlay e aplicativos de jogadores

Quando o seu conteúdo é encriptado com **o Apple FairPlay,** as amostras individuais de vídeo e áudio são encriptadas utilizando o modo **CBC AES-128.** **O FairPlay Streaming** (FPS) está integrado nos sistemas operativos do dispositivo, com suporte nativo no iOS e Apple TV. O Safari no OS X permite fps utilizando o suporte de interface de extensões de mídia encriptadas (EME).

O Azure Media Player também suporta a reprodução do FairPlay. Para mais informações, consulte [a documentação do Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Pode desenvolver as suas próprias aplicações de jogadores utilizando o iOS SDK. Para poder jogar o conteúdo do FairPlay, tem de implementar o protocolo de troca de licenças. Este protocolo não é especificado pela Apple. Cabe a cada app enviar pedidos de entrega chave. O serviço de entrega chave de media Serviços FairPlay espera que o SPC venha como uma mensagem postal codificada www-form-url, na seguinte forma:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Configuração FairPlay .NET exemplo

Pode utilizar a API dos Serviços de Comunicação Social para configurar licenças FairPlay. Quando o jogador tenta reproduzir o seu conteúdo protegido pelo FairPlay, é enviado um pedido ao serviço de entrega de licenças para obter a licença. Se o serviço de licença aprovar o pedido, o serviço emite a licença. É enviado para o cliente e é usado para desencriptar e reproduzir o conteúdo especificado.

> [!NOTE]
> Normalmente, gostaria de configurar as opções de política do FairPlay apenas uma vez, porque só terá um conjunto de certificação e um ASK.

O exemplo a seguir utiliza [os Serviços de Comunicação Social .NET SDK](/dotnet/api/microsoft.azure.management.media.models) para configurar a licença.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Passos seguintes

Confira como [proteger com DRM](protect-with-drm.md)
