---
title: Configure a sua conta para o streaming offline de conteúdos protegidos widevine - Azure
description: Este tópico mostra como configurar a sua conta Azure Media Services para o streaming offline de conteúdos protegidos de Widevine.
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
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 4b3b2b8c39b5b2552b5ce9f508bacd1ea86b2638
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006371"
---
# <a name="offline-widevine-streaming-for-android"></a>Transmissão offline em fluxo de Widevine para Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 3](../latest/offline-widevine-for-android.md)
> * [Versão 2](offline-widevine-for-android.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Além de proteger conteúdos para streaming online, a subscrição de conteúdos de mídia e os serviços de aluguer oferecem conteúdo transferível que funciona quando não está ligado à internet. Pode ser necessário transferir conteúdo para o seu telemóvel ou tablet para reprodução no modo avião quando voar desligado da rede. Cenários adicionais, nos quais poderá querer descarregar conteúdo:

- Alguns fornecedores de conteúdo podem não permitir a entrega de licenças de DRM para além da fronteira entre um país e uma região. Se um utilizador quiser ver conteúdo durante a viagem ao estrangeiro, é necessário descarregar offline.
- Em alguns países/regiões, a disponibilidade de Internet e/ou largura de banda é limitada. Os utilizadores podem optar por descarregar conteúdo para poderem vê-lo em resolução suficientemente alta para uma experiência de visualização satisfatória.

Este artigo discute como implementar a reprodução em modo offline para conteúdo DASH protegido pela Widevine em dispositivos Android. A DRM offline permite-lhe fornecer modelos de subscrição, aluguer e compra para o seu conteúdo, permitindo que os clientes dos seus serviços levem facilmente conteúdo com eles quando desligados da internet.

Para a construção das aplicações para jogadores Android, delineamos três opções:

> [!div class="checklist"]
> * Construa um jogador usando a API java do ExoPlayer SDK
> * Construa um jogador usando a ligação Xamarin da ExoPlayer SDK
> * Construa um leitor utilizando a Extensão de Mídia Encriptada (EME) e a Extensão de Fonte de Mídia (MSE) no navegador móvel Chrome v62 ou posterior

O artigo também responde a algumas questões comuns relacionadas com o streaming offline de conteúdos protegidos de Widevine.

## <a name="requirements"></a>Requisitos 

Antes de implementar DRM offline para Widevine em dispositivos Android, você deve primeiro:

- Familiarize-se com os conceitos introduzidos para a proteção de conteúdos on-line utilizando a Widevine DRM. Este facto é abordado em pormenor nos seguintes documentos/amostras:
    - [Utilize os Serviços de Media Azure para entregar licenças DE DRM ou chaves AES](media-services-deliver-keys-and-licenses.md)
    - [CENC com Controlo de Acesso e Multi-DRM: Uma Estrutura de Referência e Implementação no Azure e Serviços de Multimédia do Azure](media-services-cenc-with-multidrm-access-control.md)
    - [Utilizando a encriptação comum dinâmica playReady e/ou widevine dynamic com .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Utilize os Serviços de Media Azure para entregar licenças PlayReady e/ou Widevine com .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Familiarize-se com o Google ExoPlayer SDK para Android, um leitor de vídeo de código aberto SDK capaz de suportar a reprodução de DRM offline Widevine. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Guia de desenvolvedores exoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog de desenvolvimento do EoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Configuração de proteção de conteúdos nos Serviços Azure Media

Ao configurar a proteção widevine de um ativo em Serviços de Mídia, precisa de criar a ContentKeyAuthorizationPolicyOption que especificou as seguintes três coisas:

1. Sistema DRM (Widevine)
2. ContentKeyAuthorizationPolicyRestriction especificando como a entrega da chave de conteúdo é autorizada no serviço de entrega de licenças (autorização aberta ou simbólica)
3. Modelo de licença DRM (Widevine)

Para ativar o modo **offline** para licenças widevine, é necessário configurar [o modelo de licença Widevine](media-services-widevine-license-template-overview.md). No **objeto policy_overrides,** dedije a propriedade **can_persist** a **verdade** (o padrão é falso). 

A amostra de código que se segue utiliza .NET para ativar o modo **offline** para licenças Widevine. O código baseia-se na encriptação comum dinâmica [ de utilização do PlayReady e/ou da Widevine Dynamic Com](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) a amostra .NET. 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

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

Para alguns dispositivos Android mais antigos, deve definir valores para as seguintes propriedades **policy_overrides** (definidas no [modelo de licença Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** e **license_duration_seconds**. Em alternativa, pode defini-los a zero, o que significa duração infinita/ilimitada.  

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

- [Código de origem](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versão hospedada pela Google](https://biograf-155113.appspot.com/ttt/episode-2/) (só funciona no Chrome v 62 e mais alto em dispositivos Android)

Se atualizar o seu navegador Chrome móvel para v62 (ou superior) num telefone Android e testar a aplicação de amostras acima hospedada, verá que tanto o streaming online como o trabalho de reprodução offline funcionam.

A aplicação PWA de código aberto acima é da autoria em Node.js. Se pretender hospedar a sua própria versão num servidor Ubuntu, lembre-se dos seguintes problemas comuns encontrados que podem impedir a reprodução:

1. Problema CORS: O vídeo da amostra na aplicação da amostra está hospedado em https://storage.googleapis.com/biograf-video-files/videos/ . A Google criou o CORS para todas as suas amostras de teste hospedadas no balde de armazenamento do Google Cloud. São servidos com cabeçalhos CORS, especificando explicitamente a entrada CORS: `https://biograf-155113.appspot.com` (o domínio em que o Google hospeda a sua amostra) impedindo o acesso por qualquer outro sites. Se tentar, verá o seguinte erro HTTP: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Emissão de certificado: A partir do Chrome v 58, a EME for Widevine requer HTTPS. Por isso, é necessário hospedar a aplicação da amostra em HTTPS com um certificado X509. Um certificado de ensaio habitual não funciona devido aos seguintes requisitos: É necessário obter um certificado que cumpra os seguintes requisitos mínimos:
    - Chrome e Firefox exigem SAN-Subject definição de Nome Alternativo para existir no certificado
    - O certificado deve ter confiado em AC e um certificado de desenvolvimento auto-assinado não funciona
    - O certificado deve ter um CN correspondente ao nome DNS do servidor web ou gateway

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="question"></a>Pergunta

Como posso entregar licenças persistentes (ativadas offline) para alguns clientes/utilizadores e licenças não persistentes (offline-desativadas) para outros? Tenho de duplicar o conteúdo e utilizar a chave de conteúdo separada?

### <a name="answer"></a>Resposta
Não é necessário duplicar o conteúdo. Pode simplesmente utilizar uma única cópia do conteúdo e uma única ContentKeyAuthorizationPolicy, mas dois ContentKeyAuthorizationPolicyOption's:

1. IContentKeyAuthorizationPolicyOption 1: usa licença persistente, e ContentKeyAuthorizationPolicyRestriction 1 que contém uma reivindicação como license_type = "Persistente"
2. IContentKeyAuthorizationPolicyOption 2: usa licença não persistente, e ContentKeyAuthorizationPolicyRestriction 2 que contém uma reivindicação como license_type = "Nonpersistent"

Desta forma, quando um pedido de licença chega da app do cliente, a partir do pedido de licença não há diferença. No entanto, para diferentes utilizador/dispositivo final, o STS deve ter a lógica de negócio para emitir diferentes fichas JWT contendo diferentes alegações (uma das duas license_type acima). O valor das reclamações no token JWT será usado para decidir pelo serviço de licença para emitir que tipo de licença: persistente ou não persistente.

Isto significa que o Serviço DeKen Seguro (STS) precisa de ter a lógica de negócio e informações de cliente/dispositivo para adicionar o valor de reclamação correspondente num token.

### <a name="question"></a>Pergunta

Para os níveis de segurança widevine, na documentação geral do doc de [arquitetura DRM da](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) Google, define três níveis de segurança diferentes. No entanto, na [documentação do Azure Media Services sobre o modelo de licença widevine,](./media-services-widevine-license-template-overview.md)são delineados cinco níveis de segurança diferentes. Qual é a relação ou mapeamento entre os dois diferentes conjuntos de níveis de segurança?

### <a name="answer"></a>Resposta

Na visão geral da [arquitetura drm widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)da Google, define os seguintes três níveis de segurança:

1.  Nível de Segurança 1: Todo o processamento de conteúdos, criptografia e controlo são realizados dentro do Ambiente de Execução Fidedigna (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em diferentes chips.
2.  Nível de Segurança 2: Executa a criptografia (mas não o processamento de vídeo) dentro do TEE: os amortecedores desencriptados são devolvidos ao domínio da aplicação e processados através de hardware ou software de vídeo separados. No nível 2, no entanto, a informação criptográfica ainda é processada apenas dentro do TEE.
3.  Segurança Nível 3 Não tem um TEE no dispositivo. Podem ser tomadas medidas adequadas para proteger as informações criptográficas e o conteúdo desencriptado no sistema operativo hospedeiro. Uma implementação de Nível 3 também pode incluir um motor criptográfico de hardware, mas que apenas aumenta o desempenho, não a segurança.

Ao mesmo tempo, na documentação do [Azure Media Services sobre o modelo de licença Widevine,](./media-services-widevine-license-template-overview.md)a propriedade security_level de content_key_specs pode ter os seguintes cinco valores diferentes (requisitos de robustez do cliente para reprodução):

1.  É necessário cripto de caixa branca baseado em software.
2.  É necessário cripto de software e um descodificador obfuscado.
3.  O material chave e as operações de cripto devem ser realizados dentro de um TEE apoiado por hardware.
4.  A cripto e a descodição dos conteúdos devem ser realizadas dentro de um TEE apoiado por hardware.
5.  A criptografia, a descodão e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados dentro de um TEE apoiado por hardware.

Ambos os níveis de segurança são definidos pelo Google Widevine. A diferença está no seu nível de utilização: nível de arquitetura ou nível de API. Os cinco níveis de segurança são usados na API widevine. O objeto content_key_specs, que contém security_level é deseralizado e passado para o serviço de entrega global Widevine pelo serviço de licenças Azure Media Services Widevine. A tabela abaixo mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de Segurança Definidos em Arquitetura Widevine** |**Níveis de Segurança Utilizados na API de Widevine**|
|---|---| 
| **Nível de Segurança 1**: Todos os conteúdos processados, criptografia e controlo são realizados dentro do Ambiente de Execução Fidedigna (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em diferentes chips.|**security_level=5**: A cripto, descodante e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados dentro de um TEE apoiado por hardware.<br/><br/>**security_level=4**: A cripto e a descodição dos conteúdos devem ser efetuadas dentro de um TEE apoiado por hardware.|
**Nível de Segurança 2**: Executa a criptografia (mas não o processamento de vídeo) dentro do TEE: os tampões desencriptados são devolvidos ao domínio da aplicação e processados através de hardware ou software de vídeo separados. No nível 2, no entanto, a informação criptográfica ainda é processada apenas dentro do TEE.| **security_level=3**: O material chave e as operações de cripto devem ser efetuadas dentro de um TEE apoiado por hardware. |
| **Nível de Segurança 3**: Não tem um TEE no aparelho. Podem ser tomadas medidas adequadas para proteger as informações criptográficas e o conteúdo desencriptado no sistema operativo hospedeiro. Uma implementação de Nível 3 também pode incluir um motor criptográfico de hardware, mas que apenas aumenta o desempenho, não a segurança. | **security_level=2**: É necessário cripto de software e um descodificador obfuscado.<br/><br/>**security_level=1**: É necessário cripto de caixa branca baseado em software.|

### <a name="question"></a>Pergunta

Porque é que o download de conteúdos demora tanto tempo?

### <a name="answer"></a>Resposta

Existem duas formas de melhorar a velocidade de descarregamento:

1.  Ativar o CDN de modo a que os utilizadores finais sejam mais propensos a atingir o CDN em vez de origin/streaming para download de conteúdo. Se o utilizador atingir o ponto final de streaming, cada segmento HLS ou fragmento de DASH é dinamicamente embalado e encriptado. Mesmo que esta latência esteja em escala milissegundo para cada segmento/fragmento, quando você tem um vídeo de uma hora de duração, a latência acumulada pode ser grande causando um download mais longo.
2.  Forneça aos utilizadores finais a opção de descarregar seletivamente camadas de qualidade de vídeo e faixas de áudio em vez de todos os conteúdos. Para o modo offline, não faz sentido baixar todas as camadas de qualidade. Há duas maneiras de o conseguir:
    1.  Controlado pelo cliente: ou a aplicação do jogador seleciona automaticamente ou o utilizador seleciona camadas de qualidade de vídeo e faixas de áudio para descarregar;
    2.  Controlado por serviço: pode-se utilizar a função Dynamic Manifest nos Serviços Azure Media para criar um filtro (global), que limita a lista de reprodução HLS ou o MPD do DASH a uma única camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, o URL de descarregamento apresentado aos utilizadores finais incluirá este filtro.

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="summary"></a>Resumo

Este artigo discutiu como implementar a reprodução em modo offline para conteúdo DASH protegido pela Widevine em dispositivos Android.  Respondeu igualmente a algumas questões comuns relacionadas com o streaming offline de conteúdos protegidos de Widevine.
