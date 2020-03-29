---
title: Media Services e suporte à licença Apple FairPlay - Azure / Microsoft Docs
description: Este tópico dá uma visão geral dos requisitos e configurações da licença Apple FairPlay.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733577"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Requisitos de licença e configuração do Apple FairPlay 

O Azure Media Services permite-lhe encriptar o seu conteúdo HLS com o **Apple FairPlay** (AES-128 CBC). A Media Services também fornece um serviço para a entrega de licenças FairPlay. Quando um jogador tenta reproduzir o seu conteúdo protegido pelo FairPlay, um pedido é enviado para o serviço de entrega de licença para obter uma licença. Se o serviço de licença aprovar o pedido, emite a licença que é enviada ao cliente e é usada para desencriptar e reproduzir o conteúdo especificado.

A Media Services também fornece APIs que pode usar para configurar as suas licenças FairPlay. Este tópico discute os requisitos de licença fairPlay e demonstra como pode configurar uma licença **FairPlay** usando APIs de Serviços de Media. 

## <a name="requirements"></a>Requisitos

São necessários os seguintes serviços de comunicação social para encriptar o seu conteúdo HLS com o **Apple FairPlay** e utilizar os Serviços de Media para entregar licenças FairPlay:

* Inscreva-se no Programa de Desenvolvimento da [Apple](https://developer.apple.com/).
* A Apple exige que o proprietário do conteúdo obtenha o pacote de [implementação.](https://developer.apple.com/contact/fps/) Diga que já implementou o Módulo de Segurança Chave (KSM) com os Serviços de Media e que está a solicitar o pacote final de FPS. Existem instruções no pacote final de FPS para gerar certificação e obter a Chave Secreta da Aplicação (ASK). Usa ask para configurar fairPlay.
* As seguintes coisas devem ser definidas no lado da chave/entrega da licença dos Serviços de Media:

    * **App Cert (AC)**: Este é um ficheiro .pfx que contém a chave privada. Cria este ficheiro e encripta-o com uma palavra-passe. O ficheiro .pfx deve estar no formato Base64.

        Os seguintes passos descrevem como gerar um ficheiro de certificado .pfx para FairPlay:

        1. Instale OpenSSL a partir de https://slproweb.com/products/Win32OpenSSL.html.

            Vá para a pasta onde estão o certificado FairPlay e outros ficheiros entregues pela Apple.
        2. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro .cer num ficheiro .pem.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro .pem num ficheiro .pfx com a chave privada. A palavra-passe para o ficheiro .pfx é então pedida pela OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -exportação -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **Palavra-passe do App Cert**: A palavra-passe para criar o ficheiro .pfx.
    * **ASK**: Esta chave é recebida quando gera a certificação utilizando o portal Apple Developer. Cada equipa de desenvolvimento recebe um ASK único. Guarde uma cópia do ASK e guarde-a num local seguro. Você precisa configurar ASK como FairPlayAsk com Media Services.
    
* As seguintes coisas devem ser definidas pelo lado do cliente da FPS:

  * **App Cert (AC)**: Este é um ficheiro .cer/.der que contém a chave pública, que o sistema operativo utiliza para encriptar alguma carga útil. A Media Services precisa de saber sobre isso porque é exigida pelo jogador. O serviço de entrega chave desencripta-o utilizando a chave privada correspondente.

* Para reproduzir um stream encriptado FairPlay, obtenha primeiro um ASK real e, em seguida, gere um certificado real. Este processo cria as três partes:

  * ficheiro .der
  * arquivo .pfx
  * senha para o .pfx

## <a name="fairplay-and-player-apps"></a>FairPlay e aplicativos de jogadores

Quando o seu conteúdo é encriptado com **o Apple FairPlay,** as amostras individuais de vídeo e áudio são encriptadas utilizando o modo **AES-128 CBC.** O **FairPlay Streaming** (FPS) está integrado nos sistemas operativos do dispositivo, com suporte nativo no iOS e apple TV. O Safari no OS X ativa o FPS utilizando o suporte de interface de extensões de mídia encriptada (EME).

O Azure Media Player também suporta a reprodução do FairPlay. Para mais informações, consulte a [documentação do Azure Media Player.](https://amp.azure.net/libs/amp/latest/docs/index.html)

Pode desenvolver as suas próprias aplicações de jogadores utilizando o iOS SDK. Para poder jogar conteúdo fairPlay, tem de implementar o protocolo de troca de licenças. Este protocolo não é especificado pela Apple. Cabe a cada aplicação como enviar pedidos de entrega chave. O serviço de entrega de chaves Media Services FairPlay espera que o SPC venha como uma mensagem postal codificada em www-form-url, no seguinte formulário:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Configuração FairPlay .NET exemplo

Pode utilizar a API dos Serviços de Media para configurar as licenças FairPlay. Quando o jogador tenta reproduzir o seu conteúdo protegido pelo FairPlay, um pedido é enviado para o serviço de entrega de licença para obter a licença. Se o serviço de licença aprovar o pedido, o serviço emite a licença. É enviado para o cliente e é usado para desencriptar e reproduzir o conteúdo especificado.

> [!NOTE]
> Normalmente, você quereria configurar opções de política FairPlay apenas uma vez, porque você terá apenas um conjunto de certificação e um ASK.

O exemplo seguinte utiliza [media services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) para configurar a licença.

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
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Passos seguintes

Confira como [proteger com a DRM](protect-with-drm.md)
