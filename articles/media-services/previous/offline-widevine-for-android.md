---
title: Configurar sua conta para streaming offline de conteúdo protegido por Widevine – Azure
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
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 5137f35a4707aa68adfbf3f326ca9e4bfb40f0f4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970334"
---
# <a name="offline-widevine-streaming-for-android"></a>Streaming de Widevine offline para Android  

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 3](../latest/offline-widevine-for-android.md)
> * [Versão 2](offline-widevine-for-android.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

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

## <a name="requirements"></a>Requisitos 

Antes de implementar o DRM offline para Widevine em dispositivos Android, primeiro você deve:

- Familiarize-se com os conceitos introduzidos para proteção de conteúdo online usando o Widevine DRM. Isso é abordado em detalhes nos seguintes documentos/exemplos:
    - [Usar os serviços de mídia do Azure para fornecer licenças DRM ou chaves AES](media-services-deliver-keys-and-licenses.md)
    - [CENC com Controlo de Acesso e Multi-DRM: Uma Estrutura de Referência e Implementação no Azure e Serviços de Multimédia do Azure](media-services-cenc-with-multidrm-access-control.md)
    - [Usando Criptografia Comum dinâmicos PlayReady e/ou Widevine com o .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Usar os serviços de mídia do Azure para fornecer licenças PlayReady e/ou Widevine com o .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Familiarize-se com o SDK do Google ExoPlayer para Android, um SDK de player de vídeo de software livre capaz de dar suporte à reprodução de DRM offline Widevine. 
    - [SDK do ExoPlayer](https://github.com/google/ExoPlayer)
    - [Guia do desenvolvedor do ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog do desenvolvedor do EoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Configuração de proteção de conteúdo nos serviços de mídia do Azure

Ao configurar a proteção Widevine de um ativo nos serviços de mídia, você precisa criar o ContentKeyAuthorizationPolicyOption que especificou as três coisas a seguir:

1. Sistema DRM (Widevine)
2. ContentKeyAuthorizationPolicyRestriction especificando como a entrega de chave de conteúdo é autorizada no serviço de entrega de licença (autorização aberta ou de token)
3. Modelo de licença do DRM (Widevine)

Para habilitar o modo **offline** para licenças Widevine, você precisa configurar o [modelo de licença Widevine](media-services-widevine-license-template-overview.md). No objeto **policy_overrides** , defina a propriedade **can_persist** como **true** (o padrão é false). 

O exemplo de código a seguir usa o .NET para habilitar o modo **offline** para licenças Widevine. O código é baseado no exemplo [usando o PlayReady e/ou Widevine Dynamic criptografia comum com .net](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) . 

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
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
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

Os desenvolvedores devem fazer referência ao [Guia do desenvolvedor do ExoPlayer](https://google.github.io/ExoPlayer/guide.html) e ao blog do [desenvolvedor](https://medium.com/google-exoplayer) correspondente durante o desenvolvimento de um aplicativo. O Google não lançou uma implementação de referência totalmente documentada ou um código de exemplo para o aplicativo ExoPlayer que dá suporte a Widevine offline no momento, portanto, as informações estão limitadas ao blog e ao guia dos desenvolvedores. 

### <a name="working-with-older-android-devices"></a>Trabalhando com dispositivos Android mais antigos

Para alguns dispositivos Android mais antigos, você deve definir valores para as seguintes propriedades de **policy_overrides** (definidas no [modelo de licença Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**e **license_duration_seconds**. Como alternativa, você pode defini-los como zero, o que significa duração infinita/ilimitada.  

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
Você não precisa duplicar o conteúdo. Você pode simplesmente usar uma única cópia do conteúdo e uma única ContentKeyAuthorizationPolicy, mas duas ContentKeyAuthorizationPolicyOption separadas:

1. IContentKeyAuthorizationPolicyOption 1: usa a licença persistente e o ContentKeyAuthorizationPolicyRestriction 1 que contém uma declaração como license_type = "Persistent"
2. IContentKeyAuthorizationPolicyOption 2: usa licença não persistente e ContentKeyAuthorizationPolicyRestriction 2 que contém uma declaração como license_type = "não persistente"

Dessa forma, quando uma solicitação de licença chega do aplicativo cliente, a partir da solicitação de licença não há diferença. No entanto, para um usuário/dispositivo final diferente, o STS deve ter a lógica de negócios para emitir diferentes tokens JWT contendo declarações diferentes (uma das license_type duas opções acima). O valor de declarações no token JWT será usado para decidir pelo serviço de licença para emitir o tipo de licença: persistente ou não persistente.

Isso significa que o serviço de token de segurança (STS) precisa ter a lógica de negócios e as informações de cliente/dispositivo para adicionar o valor de declaração correspondente em um token.

### <a name="question"></a>Pergunta

Para os níveis de segurança do Widevine, na documentação do [documento visão geral da arquitetura DRM do Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) do Google, ele define três níveis de segurança diferentes. No entanto, na [documentação dos serviços de mídia do Azure no modelo de licença do Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), cinco níveis de segurança diferentes são descritos. Qual é a relação ou o mapeamento entre os dois conjuntos diferentes de níveis de segurança?

### <a name="answer"></a>Resposta

Na [visão geral da arquitetura de DRM do Widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)do Google, ele define os três níveis de segurança a seguir:

1.  Nível de segurança 1: todo o processamento, a criptografia e o controle de conteúdo são executados no sistema de execução confiável (desempenho). Em alguns modelos de implementação, o processamento de segurança pode ser executado em diferentes chips.
2.  Nível de segurança 2: executa criptografia (mas não processamento de vídeo) dentro do monitor de desempenho: os buffers descriptografados são retornados ao domínio do aplicativo e processados por meio de um software ou hardware de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas apenas dentro do "t".
3.  O nível de segurança 3 não tem um "t" no dispositivo. As medidas apropriadas podem ser tomadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso só melhora o desempenho, não a segurança.

Ao mesmo tempo, na [documentação dos serviços de mídia do Azure no modelo de licença do Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), a propriedade security_level de content_key_specs pode ter os cinco seguintes valores diferentes (requisitos de robustez do cliente para reprodução):

1.  A criptografia whitebox baseada em software é necessária.
2.  A criptografia de software e um decodificador ofuscado são necessários.
3.  As operações de criptografia e material de chave devem ser executadas em um backup de hardware com suporte.
4.  A criptografia e a decodificação de conteúdo devem ser executadas em um backup de hardware com suporte.
5.  A criptografia, a decodificação e todos os tratamentos da mídia (compactados e descompactados) devem ser tratados em um software com suporte de hardware.

Os dois níveis de segurança são definidos pelo Google Widevine. A diferença está em seu nível de uso: nível de arquitetura ou nível de API. Os cinco níveis de segurança são usados na API do Widevine. O objeto content_key_specs, que contém security_level é desserializado e passado para o serviço de entrega global Widevine pelo serviço de licença do Widevine de serviços de mídia do Azure. A tabela a seguir mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura Widevine** |**Níveis de segurança usados na API do Widevine**|
|---|---| 
| **Nível de segurança 1**: todo o processamento, a criptografia e o controle de conteúdo são executados no sistema de execução confiável (desempenho). Em alguns modelos de implementação, o processamento de segurança pode ser executado em diferentes chips.|**security_level = 5**: a criptografia, a decodificação e todos os tratamentos da mídia (compactados e descompactados) devem ser tratados em um backup de hardware com suporte.<br/><br/>**security_level = 4**: a criptografia e a decodificação de conteúdo devem ser executadas em um backup de hardware com suporte.|
**Nível de segurança 2**: executa criptografia (mas não processamento de vídeo) dentro do monitor de desempenho: os buffers descriptografados são retornados ao domínio do aplicativo e processados por meio de um software ou hardware de vídeo separado. No entanto, no nível 2, as informações de criptografia ainda são processadas apenas dentro do "t".| **security_level = 3**: o material de chave e as operações de criptografia devem ser executadas em um backup de hardware com suporte. |
| **Nível de segurança 3**: não tem um "t" no dispositivo. As medidas apropriadas podem ser tomadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operacional do host. Uma implementação de nível 3 também pode incluir um mecanismo de criptografia de hardware, mas isso só melhora o desempenho, não a segurança. | **security_level = 2**: a criptografia de software e um decodificador ofuscado são necessários.<br/><br/>**security_level = 1**: a criptografia whitebox baseada em software é necessária.|

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
