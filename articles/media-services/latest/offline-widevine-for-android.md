---
title: Transmitir Widevine Android offline com os serviços de mídia do Azure v3
description: Este tópico mostra como configurar sua conta dos serviços de mídia do Azure para streaming offline de conteúdo protegido por Widevine.
services: media-services
keywords: DASH, DRM, modo offline Widevine, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 55ed849b6083435e70d0943a359c83793ca0842d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705909"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Offline Widevine streaming para Android com Media Services v3

Além de proteger o conteúdo para streaming online, a assinatura de conteúdo de mídia e os serviços de aluguel oferecem Conteúdo baixável que funciona quando você não está conectado à Internet. Talvez seja necessário baixar conteúdo em seu telefone ou tablet para reprodução no modo avião quando estiver desconectado da rede. Cenários adicionais, nos quais você pode desejar baixar o conteúdo:

- Alguns provedores de conteúdo podem impedir a entrega de licença do DRM além da borda de um país/região. Se um usuário quiser assistir ao conteúdo ao viajar para o exterior, o download offline será necessário.
- Em alguns países/regiões, a disponibilidade e/ou a largura de banda da Internet é limitada. Os usuários podem optar por baixar conteúdo para poder observá-lo em resolução alta o suficiente para uma experiência de exibição satisfatória.

Este artigo discute como implementar a reprodução de modo offline para conteúdo DASH protegido pelo Widevine em dispositivos Android. O DRM offline permite que você forneça modelos de assinatura, aluguel e compra para seu conteúdo, permitindo que os clientes de seus serviços facilmente façam conteúdo com eles quando desconectados da Internet.

Para criar aplicativos do Android Player, descrevemos três opções:

> [!div class="checklist"]
> * Criar um player usando a API do Java do SDK do ExoPlayer
> * Criar um player usando a associação do Xamarin do SDK do ExoPlayer
> * Criar um player usando a EME (extensão de mídia criptografada) e o MSE (extensão de origem de mídia) no Chrome Mobile Browser V62 ou posterior

O artigo também responde a algumas perguntas comuns relacionadas ao streaming offline de conteúdo protegido por Widevine.

> [!NOTE]
> O DRM offline é cobrado apenas para fazer uma única solicitação de licença quando você baixa o conteúdo. Quaisquer erros não são cobrados.

## <a name="prerequisites"></a>Pré-requisitos 

Antes de implementar o DRM offline para Widevine em dispositivos Android, primeiro você deve:

- Familiarize-se com os conceitos introduzidos para proteção de conteúdo online usando o Widevine DRM. Isso é abordado em detalhes nos seguintes documentos/exemplos:
    - [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)
    - [Use DRM dynamic encryption and license delivery service](protect-with-drm.md) (Procedimentos: utilizar a encriptação dinâmica e o serviço de entrega de licenças do DRM)
- Clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Você precisará modificar o código em [criptografar com DRM usando .net](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) para adicionar configurações de Widevine.  
- Familiarize-se com o SDK do Google ExoPlayer para Android, um SDK de player de vídeo de software livre capaz de dar suporte à reprodução de DRM offline Widevine. 
    - [SDK do ExoPlayer](https://github.com/google/ExoPlayer)
    - [Guia do desenvolvedor do ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog do desenvolvedor do EoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Configurar a proteção de conteúdo nos serviços de mídia do Azure

No método [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) , as seguintes etapas necessárias estão presentes:

1. Especifique como a entrega de chave de conteúdo é autorizada no serviço de entrega de licença: 

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
2. Configurar modelo de licença do Widevine:  

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

## <a name="enable-offline-mode"></a>Habilitar o modo offline

Para habilitar o modo **offline** para licenças Widevine, você precisa configurar o [modelo de licença Widevine](widevine-license-template-overview.md). No **policy_overrides** objeto, detete teda a propriedade **can_persist** **verdadeira** (o padrão é falso), como mostrado no [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Configurando o Android Player para reprodução offline

A maneira mais fácil de desenvolver um aplicativo de Player nativo para dispositivos Android é usar o [SDK do Google ExoPlayer](https://github.com/google/ExoPlayer), um SDK de player de vídeo de software livre. O ExoPlayer dá suporte a recursos que não têm suporte atualmente pela API do MediaPlayer nativo do Android, incluindo os protocolos de entrega MPEG-DASH e Microsoft Smooth Streaming.

A versão 2,6 e superior do ExoPlayer inclui muitas classes que dão suporte à reprodução de DRM offline do Widevine. Em particular, a classe OfflineLicenseHelper fornece funções utilitárias para facilitar o uso do DefaultDrmSessionManager para baixar, renovar e liberar licenças offline. As classes fornecidas na pasta do SDK "library/Core/src/main/java/com/Google/Android/exoplayer2/offline/" dão suporte ao download de conteúdo de vídeo offline.

A lista de classes a seguir facilita o modo offline no SDK do ExoPlayer para Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Os desenvolvedores devem fazer referência ao [Guia do desenvolvedor do ExoPlayer](https://google.github.io/ExoPlayer/guide.html) e ao blog do [desenvolvedor](https://medium.com/google-exoplayer) correspondente durante o desenvolvimento de um aplicativo. O Google não lançou uma implementação de referência totalmente documentada ou um código de exemplo para o aplicativo ExoPlayer que dá suporte a Widevine offline no momento, portanto, as informações estão limitadas ao guia e ao blog dos desenvolvedores. 

### <a name="working-with-older-android-devices"></a>Trabalhando com dispositivos Android mais antigos

Para alguns dispositivos Android mais antigos, deve definir valores para as seguintes propriedades **policy_overrides** (definidas no modelo de [licença Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**e **license_duration_seconds**. Como alternativa, você pode defini-los como zero, o que significa duração infinita/ilimitada.  

Os valores devem ser definidos para evitar um bug de estouro de número inteiro. Para obter mais explicações sobre o problema, consulte https://github.com/google/ExoPlayer/issues/3150 e https://github.com/google/ExoPlayer/issues/3112. <br/>Se você não definir os valores explicitamente, valores muito grandes para **PlaybackDurationRemaining** e **LicenseDurationRemaining** serão atribuídos (por exemplo, 9223372036854775807, que é o valor positivo máximo para um inteiro de 64 bits). Como resultado, a licença Widevine aparece expirada e, portanto, a descriptografia não ocorrerá. 

Esse problema não ocorre no Android 5,0 pirulito ou posterior, uma vez que o Android 5,0 é a primeira versão do Android, que foi projetada para suportar totalmente o ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) e plataformas de 64 bits, enquanto o Android 4,4 KitKat foi originalmente projetado para dar suporte a plataformas ARMv7 e de 32 bits como com outras versões mais antigas do Android.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Usando o Xamarin para criar um aplicativo de reprodução Android

Você pode encontrar associações do Xamarin para ExoPlayer usando os seguintes links:

- [Biblioteca de associações do Xamarin para a biblioteca do Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Associações do Xamarin para NuGet ExoPlayer](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Além disso, consulte o seguinte thread: [associação do Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplicativos do Player Chrome para Android

A partir do lançamento do [Chrome para Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), há suporte para a licença persistente em eme. O [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) agora também tem suporte no Chrome para Android. Isso permite que você crie aplicativos de reprodução offline no Chrome se os usuários finais tiverem essa versão (ou superior) do Chrome. 

Além disso, o Google produziu um exemplo de aplicativo Web progressivo (PWA) e o código-fonte aberto: 

- [Código fonte](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versão hospedada do Google](https://biograf-155113.appspot.com/ttt/episode-2/) (funciona apenas no Chrome v 62 e superior em dispositivos Android)

Se você atualizar seu navegador Chrome móvel para V62 (ou superior) em um telefone Android e testar o aplicativo de exemplo hospedado acima, verá que o streaming online e o trabalho de reprodução offline.

O aplicativo do PWA de código aberto acima é criado no node. js. Se você quiser hospedar sua própria versão em um servidor Ubuntu, tenha em mente os seguintes problemas comuns encontrados que podem impedir a reprodução:

1. Problema de CORS: o vídeo de exemplo no aplicativo de exemplo é hospedado em https://storage.googleapis.com/biograf-video-files/videos/. O Google configurou o CORS para todos os seus exemplos de teste hospedados no Google Cloud Storage Bucket. Eles são servidos com cabeçalhos CORS, especificando explicitamente a entrada CORS: https://biograf-155113.appspot.com (o domínio no qual o Google hospeda seu exemplo) impedindo o acesso por outros sites. Se você tentar, verá o seguinte erro HTTP: falha ao carregar https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: nenhum cabeçalho ' Access-Control-Allow-Origin ' está presente no recurso solicitado. A origem ' https:\//13.85.80.81:8080 ', portanto, não tem permissão de acesso. Se uma resposta opaca atende às suas necessidades, defina o modo da solicitação como ' no-CORS ' para buscar o recurso com CORS desabilitado.
2. Problema de certificado: a partir do Chrome v 58, o EME para Widevine requer HTTPS. Portanto, você precisa hospedar o aplicativo de exemplo por HTTPS com um certificado X509. Um certificado de teste comum não funciona devido aos seguintes requisitos: você precisa obter um certificado atendendo aos seguintes requisitos mínimos:
    - O Chrome e o Firefox exigem que a configuração de nome alternativo da entidade SAN exista no certificado
    - O certificado deve ter uma AC confiável e um certificado de desenvolvimento autoassinado não funciona
    - O certificado deve ter um CN correspondente ao nome DNS do servidor Web ou gateway

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="question"></a>Pergunta

Como posso entregar licenças persistentes (habilitadas offline) para alguns clientes/usuários e licenças não persistentes (desabilitadas offline) para outras? É necessário duplicar o conteúdo e usar uma chave de conteúdo separada?

### <a name="answer"></a>Resposta
Como o Media Services V3 permite que um ativo tenha vários StreamingLocators. Você pode ter

1.  One ContentKeyPolicy com license_type = "persistente", ContentKeyPolicyRestriction com reivindicação sobre "persistente", e o seu Localizador de Streaming;
2.  Outra ContentKeyPolicy com license_type="não persistente", ContentKeyPolicyRestriction com reivindicação sobre "não persistente", e o seu StreamingLocator.
3.  Os dois StreamingLocators têm ContentKey diferentes.

Dependendo da lógica de negócios do STS personalizado, declarações diferentes são emitidas no token JWT. Com o token, somente a licença correspondente pode ser obtida e somente a URL correspondente pode ser reproduzida.

### <a name="question"></a>Pergunta

Para os níveis de segurança do Widevine, na documentação do [documento visão geral da arquitetura DRM do Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) do Google, ele define três níveis de segurança diferentes. No entanto, na [documentação dos serviços de mídia do Azure no modelo de licença do Widevine](widevine-license-template-overview.md), cinco níveis de segurança diferentes são descritos. Qual é a relação ou o mapeamento entre os dois conjuntos diferentes de níveis de segurança?

### <a name="answer"></a>Resposta

Na [visão geral da arquitetura de DRM do Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)do Google, ele define os três níveis de segurança a seguir:

1.  Nível de segurança 1: todo o processamento, a criptografia e o controle de conteúdo são executados no sistema de execução confiável (desempenho). Em alguns modelos de implementação, o processamento de segurança pode ser executado em diferentes chips.
2.  Nível de segurança 2: executa criptografia (mas não processamento de vídeo) dentro do monitor de desempenho: os buffers descriptografados são retornados ao domínio do aplicativo e processados por meio de um software ou hardware de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas apenas dentro do "t".
3.  O nível de segurança 3 não tem um "t" no dispositivo. As medidas apropriadas podem ser tomadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso só melhora o desempenho, não a segurança.

Ao mesmo tempo, na documentação da Azure Media Services sobre o modelo de [licença Widevine,](widevine-license-template-overview.md)a propriedade security_level de content_key_specs pode ter os seguintes cinco valores diferentes (requisitos de robustez do cliente para reprodução):

1.  A criptografia whitebox baseada em software é necessária.
2.  A criptografia de software e um decodificador ofuscado são necessários.
3.  As operações de criptografia e material de chave devem ser executadas em um backup de hardware com suporte.
4.  A criptografia e a decodificação de conteúdo devem ser executadas em um backup de hardware com suporte.
5.  A criptografia, a decodificação e todos os tratamentos da mídia (compactados e descompactados) devem ser tratados em um software com suporte de hardware.

Os dois níveis de segurança são definidos pelo Google Widevine. A diferença está em seu nível de uso: nível de arquitetura ou nível de API. Os cinco níveis de segurança são usados na API do Widevine. O objeto content_key_specs, que contém security_level é desserializado e passado para o serviço de entrega global Widevine pelo serviço de licença Azure Media Services Widevine. A tabela a seguir mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura Widevine** |**Níveis de segurança usados na API do Widevine**|
|---|---| 
| **Nível de segurança 1**: todo o processamento, a criptografia e o controle de conteúdo são executados no sistema de execução confiável (desempenho). Em alguns modelos de implementação, o processamento de segurança pode ser executado em diferentes chips.|**security_level=5**: O crypto, a descodificação e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados dentro de um TEE apoiado por hardware.<br/><br/>**security_level=4**: A criptoee e a descodificação de conteúdos devem ser realizadas dentro de um TEE apoiado por hardware.|
**Nível de segurança 2**: executa criptografia (mas não processamento de vídeo) dentro do monitor de desempenho: os buffers descriptografados são retornados ao domínio do aplicativo e processados por meio de um software ou hardware de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas apenas dentro do "t".| **security_level=3**: O material-chave e as operações de cripto devem ser realizados dentro de um TEE apoiado por hardware. |
| **Nível de segurança 3**: não tem um "t" no dispositivo. As medidas apropriadas podem ser tomadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso só melhora o desempenho, não a segurança. | **security_level=2**: São necessários criptografias de software e um descodificador obfuscado.<br/><br/>**security_level=1**: É necessário um cripto de caixa branca baseado em software.|

### <a name="question"></a>Pergunta

Por que o download do conteúdo leva tanto tempo?

### <a name="answer"></a>Resposta

Há duas maneiras de melhorar a velocidade de download:

1.  Habilite a CDN para que os usuários finais tenham mais probabilidade de atingir a CDN em vez do ponto de extremidade de origem/streaming para download de conteúdo. Se o usuário atingir o ponto de extremidade de streaming, cada segmento HLS ou fragmento de TRACEJADO será empacotado e criptografado dinamicamente. Embora essa latência esteja em escala de milissegundos para cada segmento/fragmento, quando você tem um vídeo de hora longa, a latência acumulada pode ser grande, causando um download mais longo.
2.  Forneça aos usuários finais a opção de baixar seletivamente camadas de qualidade de vídeo e faixas de áudio em vez de todo o conteúdo. Para o modo offline, não há nenhum ponto para baixar todas as camadas de qualidade. Há duas maneiras de conseguir isso:
    1.  Controlado pelo cliente: o aplicativo de jogador seleciona automaticamente ou seleciona a camada de qualidade de vídeo e as faixas de áudio para download;
    2.  Controlado pelo serviço: um pode usar o recurso de manifesto dinâmico nos serviços de mídia do Azure para criar um filtro (global), que limita a lista de reprodução HLS ou o DASH MPD a uma única camada de qualidade de vídeo e a faixas de áudio selecionadas. Em seguida, a URL de download apresentada aos usuários finais incluirá esse filtro.

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="summary"></a>Resumo

Este artigo abordou como implementar a reprodução de modo offline para conteúdo DASH protegido pelo Widevine em dispositivos Android.  Ele também respondeu algumas perguntas comuns relacionadas ao streaming offline de conteúdo protegido por Widevine.
