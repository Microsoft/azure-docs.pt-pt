---
title: Stream Widevine Android offline
description: Este tópico mostra como configurar a sua conta Azure Media Services v3 para o streaming offline de conteúdos protegidos de Widevine.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c7adab9c14075e12cf5656f9e798e78ff26ff0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955212"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Streaming offline Widevine para Android com Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Além de proteger conteúdos para streaming online, a subscrição de conteúdos de mídia e os serviços de aluguer oferecem conteúdo transferível que funciona quando não está ligado à internet. Pode ser necessário transferir conteúdo para o seu telemóvel ou tablet para reprodução no modo avião quando voar desligado da rede. Cenários adicionais, nos quais poderá querer descarregar conteúdo:

- Alguns fornecedores de conteúdo podem não permitir a entrega de licenças de DRM para além da fronteira entre um país e uma região. Se um utilizador quiser ver conteúdo durante a viagem ao estrangeiro, é necessário descarregar offline.
- Em alguns países/regiões, a disponibilidade de Internet e/ou largura de banda é limitada. Os utilizadores podem optar por descarregar conteúdo para poderem vê-lo em resolução suficientemente alta para uma experiência de visualização satisfatória.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Este artigo discute como implementar a reprodução em modo offline para conteúdo DASH protegido pela Widevine em dispositivos Android. A DRM offline permite-lhe fornecer modelos de subscrição, aluguer e compra para o seu conteúdo, permitindo que os clientes dos seus serviços levem facilmente conteúdo com eles quando desligados da internet.

Para a construção das aplicações para jogadores Android, delineamos três opções:

> [!div class="checklist"]
> * Construa um jogador usando a API java do ExoPlayer SDK
> * Construa um jogador usando a ligação Xamarin da ExoPlayer SDK
> * Construa um leitor utilizando a Extensão de Mídia Encriptada (EME) e a Extensão de Fonte de Mídia (MSE) no navegador móvel Chrome v62 ou posterior

O artigo também responde a algumas questões comuns relacionadas com o streaming offline de conteúdos protegidos de Widevine.

> [!NOTE]
> A DRM offline é cobrada apenas para fazer um único pedido de licença quando você descarrega o conteúdo. Quaisquer erros não são cobrados.

## <a name="prerequisites"></a>Pré-requisitos 

Antes de implementar DRM offline para Widevine em dispositivos Android, você deve primeiro:

- Familiarize-se com os conceitos introduzidos para a proteção de conteúdos on-line utilizando a Widevine DRM. Este facto é abordado em pormenor nos seguintes documentos/amostras:
    - [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)
    - [Utilize encriptação dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)
- https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.gitClone.

    Terá de modificar o código em [Criptografo com DRM utilizando .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) para adicionar configurações de Widevine.  
- Familiarize-se com o Google ExoPlayer SDK para Android, um leitor de vídeo de código aberto SDK capaz de suportar a reprodução de DRM offline Widevine. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Guia de desenvolvedores exoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog de desenvolvimento do EoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Configure a proteção de conteúdos nos Serviços Azure Media

No método [GetOrCreateContentKeyPolicyAsync,](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) estão presentes os seguintes passos necessários:

1. Especifique como a entrega da chave de conteúdo é autorizada no serviço de entrega de licenças: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Configurar o modelo de licença widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Criar ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Ativar o modo offline

Para ativar o modo **offline** para licenças widevine, é necessário configurar [o modelo de licença Widevine](widevine-license-template-overview.md). No **objeto policy_overrides,** dedireta a propriedade **can_persist** a **verdade** (o padrão é falso), como mostra o [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Configurar o leitor Android para reprodução offline

A forma mais fácil de desenvolver uma aplicação de jogadores nativos para dispositivos Android é utilizar o [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), um leitor de vídeo sDK de código aberto. O ExoPlayer suporta funcionalidades não suportadas atualmente pela API mediaplayer nativa do Android, incluindo os protocolos de entrega mpeg-DASH e Microsoft Smooth Streaming.

A versão ExoPlayer 2.6 e superior inclui muitas classes que suportam a reprodução de DRM offline. Em particular, a classe OfflineLicenseHelper fornece funções de utilidade para facilitar a utilização do DefaultDrmSessionManager para o download, renovação e libertação de licenças offline. As aulas fornecidas na pasta SDK "library/core/src/main/java/com/google/android/exoplayer2/offline/" suportam o descarregamento de conteúdos de vídeo offline.

A seguinte lista de classes facilita o modo offline no ExoPlayer SDK para Android:

- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- biblioteca/núcleo/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- biblioteca/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Os desenvolvedores devem fazer referência ao [Guia de Desenvolvimento do ExoPlayer](https://google.github.io/ExoPlayer/guide.html) e ao blog de [desenvolvimento](https://medium.com/google-exoplayer) correspondente durante o desenvolvimento de uma aplicação. A Google não divulgou um código de referência ou de amostra totalmente documentado para a aplicação ExoPlayer que suporta a Widevine offline neste momento, pelo que a informação está limitada ao guia e blog dos desenvolvedores. 

### <a name="working-with-older-android-devices"></a>Trabalhar com dispositivos Android mais antigos

Para alguns dispositivos Android mais antigos, deve definir valores para as seguintes propriedades **policy_overrides** (definidas no [modelo de licença Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** e **license_duration_seconds**. Em alternativa, pode defini-los a zero, o que significa duração infinita/ilimitada.  

Os valores devem ser definidos para evitar que um inseto inteiro transborde. Para mais explicações sobre o assunto, consulte https://github.com/google/ExoPlayer/issues/3150 https://github.com/google/ExoPlayer/issues/3112 e. <br/>Se não definir explicitamente os valores para  **PlaybackDurationRemaining** e **LicenseDurationRemaining** será atribuído, (por exemplo, 9223372036854775807, que é o valor máximo positivo para um número inteiro de 64 bits). Como resultado, a licença Widevine aparece caducada e, portanto, a desencriptação não vai acontecer. 

Este problema não ocorre no Android 5.0 Lollipop ou mais tarde, uma vez que o Android 5.0 é a primeira versão Android, que foi projetada para suportar totalmente arMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) e plataformas de 64 bits, enquanto o Android 4.4 KitKat foi originalmente projetado para suportar plataformas ARMv7 e 32 bits como em outras versões Android mais antigas.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Usar o Xamarin para construir uma aplicação de reprodução Android

Pode encontrar ligações Xamarin para ExoPlayer utilizando os seguintes links:

- [Biblioteca de encadernações Xamarin para a biblioteca do Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Ligações Xamarin para ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Consulte também o seguinte fio: [ligação Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplicativos de jogadores chrome para Android

A partir do lançamento do [Chrome para Android vs. 62,](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)a licença persistente em EME é suportada. [O Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) também é suportado no Chrome para Android. Isto permite criar aplicações de reprodução offline no Chrome se os seus utilizadores finais tiverem esta (ou superior) versão do Chrome. 

Além disso, a Google produziu uma amostra de Aplicação Web Progressiva (PWA) e de origem aberta: 

- [Código fonte](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versão hospedada pela Google](https://biograf-155113.appspot.com/ttt/episode-2/) (só funciona no Chrome v 62 e mais alto em dispositivos Android)

Se atualizar o seu navegador Chrome móvel para v62 (ou superior) num telefone Android e testar a aplicação de amostras acima hospedada, verá que tanto o streaming online como o trabalho de reprodução offline funcionam.

A aplicação PWA de código aberto acima é da autoria em Node.js. Se pretender hospedar a sua própria versão num servidor Ubuntu, lembre-se dos seguintes problemas comuns encontrados que podem impedir a reprodução:

1. Problema CORS: O vídeo da amostra na aplicação da amostra está hospedado em https://storage.googleapis.com/biograf-video-files/videos/ . A Google criou o CORS para todas as suas amostras de teste hospedadas no balde de armazenamento do Google Cloud. São servidos com cabeçalhos CORS, especificando explicitamente a entrada CORS: `https://biograf-155113.appspot.com` (o domínio em que o Google hospeda a sua amostra) impedindo o acesso por qualquer outro sites. Se tentar, verá o seguinte erro HTTP: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Emissão de certificado: A partir do Chrome v 58, a EME for Widevine requer HTTPS. Por isso, é necessário hospedar a aplicação da amostra em HTTPS com um certificado X509. Um certificado de ensaio habitual não funciona devido aos seguintes requisitos: É necessário obter um certificado que cumpra os seguintes requisitos mínimos:
    - Chrome e Firefox exigem SAN-Subject definição de Nome Alternativo para existir no certificado
    - O certificado deve ter confiado em AC e um certificado de desenvolvimento auto-assinado não funciona
    - O certificado deve ter um CN correspondente ao nome DNS do servidor web ou gateway

## <a name="faqs"></a>FAQs

Para mais informações, consulte [as FAQs de Widevine.](frequently-asked-questions.md#widevine-streaming-for-android)

## <a name="additional-notes"></a>Notas adicionais

Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="summary"></a>Resumo

Este artigo discutiu como implementar a reprodução em modo offline para conteúdo DASH protegido pela Widevine em dispositivos Android.  Respondeu igualmente a algumas questões comuns relacionadas com o streaming offline de conteúdos protegidos de Widevine.
