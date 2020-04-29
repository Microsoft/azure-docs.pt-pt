---
title: Configure a sua conta para streaming offline de conteúdos protegidos da Widevine - Azure
description: Este tópico mostra como configurar a sua conta Azure Media Services para streaming offline de conteúdo protegido widevine.
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
ms.openlocfilehash: f3bd7bc78eeb62cc33a01ed31bb04d94078cae4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294343"
---
# <a name="offline-widevine-streaming-for-android"></a>Transmissão offline em fluxo de Widevine para Android  

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 3](../latest/offline-widevine-for-android.md)
> * [Versão 2](offline-widevine-for-android.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

Além de proteger conteúdos para streaming online, a subscrição de conteúdos de mídia e os serviços de aluguer oferecem conteúdo descarregado que funciona quando não está ligado à internet. Pode ser necessário descarregar conteúdo no seu telemóvel ou tablet para reprodução no modo avião ao voar desligado da rede. Cenários adicionais, nos quais poderá querer descarregar conteúdo:

- Alguns fornecedores de conteúdos podem proibir a entrega de licença sem condutor para além da fronteira entre um país e uma região. Se um utilizador quiser ver conteúdo durante a viagem ao estrangeiro, é necessário descarregar offline.
- Em alguns países/regiões, a disponibilidade da Internet e/ou largura de banda é limitada. Os utilizadores podem optar por descarregar conteúdos para poderem vê-lo em resolução suficientemente elevada para uma experiência de visualização satisfatória.

Este artigo discute como implementar a reprodução de modo offline para conteúdo DASH protegido pela Widevine em dispositivos Android. A DRM offline permite-lhe fornecer modelos de subscrição, aluguer e compra para o seu conteúdo, permitindo aos clientes dos seus serviços levar facilmente conteúdo com eles quando desligados da internet.

Para a construção das aplicações para jogadores Android, delineamos três opções:

> [!div class="checklist"]
> * Construa um jogador usando a API Java do ExoPlayer SDK
> * Construa um jogador usando a ligação Xamarin do ExoPlayer SDK
> * Construa um leitor utilizando extensão de mídia encriptada (EME) e extensão de fonte de mídia (MSE) no navegador móvel Chrome v62 ou posterior

O artigo também responde a algumas questões comuns relacionadas com o streaming offline de conteúdos protegidos da Widevine.

## <a name="requirements"></a>Requisitos 

Antes de implementar o DRM offline para Widevine em dispositivos Android, deve primeiro:

- Conheça os conceitos introduzidos para a proteção de conteúdos online utilizando a Widevine DRM. Isto é coberto em pormenor nos seguintes documentos/amostras:
    - [Utilize a Azure Media Services para entregar licenças de DRM ou chaves AES](media-services-deliver-keys-and-licenses.md)
    - [CENC com Controlo de Acesso e Multi-DRM: Uma Estrutura de Referência e Implementação no Azure e Serviços de Multimédia do Azure](media-services-cenc-with-multidrm-access-control.md)
    - [Usando encriptação comum dinâmica playReady e/ou Widevine com .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Utilize os Serviços De Mídia Azure para entregar licenças PlayReady e/ou Widevine com .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Familiarize-se com o Google ExoPlayer SDK para Android, um leitor de vídeo de código aberto SDK capaz de suportar a reprodução offline widevine DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Guia de desenvolvedores exoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog de desenvolvimento eoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Configuração de proteção de conteúdos nos Serviços Azure Media

Quando configura a proteção widevine de um ativo nos Serviços de Media, precisa de criar contentKeyAuthorizationPolicyOption que especificou as seguintes três coisas:

1. Sistema DRM (Widevine)
2. ContentKeyAuthorizationPolicyRestriction especificando como a entrega da chave de conteúdo é autorizada no serviço de entrega de licenças (autorização aberta ou simbólica)
3. Modelo de licença DRM (Widevine)

Para ativar o modo **offline** para licenças Widevine, precisa configurar o modelo de [licença Widevine](media-services-widevine-license-template-overview.md). No objeto **policy_overrides,** coloque a propriedade **can_persist** **verdadeira** (o padrão é falso). 

A amostra de código seguinte utiliza .NET para ativar o modo **offline** para as licenças Widevine. O código [baseia-se na encriptação comum dinâmica de utilização playReady e/ou Widevine dynamic com](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) a amostra .NET. 

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

A forma mais fácil de desenvolver uma aplicação de jogadornativo para dispositivos Android é usar o [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), um leitor de vídeo de código aberto SDK. O ExoPlayer suporta funcionalidades que não são suportadas atualmente pela API nativa do Android, incluindo os protocolos de entrega mPEG-DASH e Microsoft Smooth Streaming.

ExoPlayer versão 2.6 e superior inclui muitas classes que suportam a reprodução offline Widevine DRM. Em particular, a classe OfflineLicenseHelper fornece funções de utilidade para facilitar a utilização do DefaultDrmSessionManager para descarregar, renovar e libertar licenças offline. As classes fornecidas na pasta SDK "library/core/src/main/java/com/google/android/exoplayer2/offline/" suportam o descarregamento de conteúdos de vídeo offline.

A seguinte lista de classes facilita o modo offline no ExoPlayer SDK para Android:

- biblioteca/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- biblioteca/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- biblioteca/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- biblioteca/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- biblioteca/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- biblioteca/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- biblioteca/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- biblioteca/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- biblioteca/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- biblioteca/traço/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Os desenvolvedores devem fazer referência ao Guia de [Desenvolvimento exoPlayer](https://google.github.io/ExoPlayer/guide.html) e ao blog de [desenvolvimento](https://medium.com/google-exoplayer) correspondente durante o desenvolvimento de uma aplicação. A Google não divulgou um código de aplicação de referência ou amostra totalmente documentado para a aplicação ExoPlayer que suporta a Widevine offline neste momento, pelo que a informação está limitada ao guia e blog dos desenvolvedores. 

### <a name="working-with-older-android-devices"></a>Trabalhar com dispositivos Android mais antigos

Para alguns dispositivos Android mais antigos, deve definir valores para as seguintes propriedades **policy_overrides** (definidas no modelo de [licença Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**e **license_duration_seconds**. Em alternativa, pode defini-los a zero, o que significa duração infinita/ilimitada.  

Os valores devem ser definidos para evitar um inseto de transbordo inteiro. Para mais explicações sobre https://github.com/google/ExoPlayer/issues/3150 https://github.com/google/ExoPlayer/issues/3112o assunto, veja e . <br/>Se não definir explicitamente os valores, serão atribuídos valores muito grandes para **ReproduçãoDuraRemaining** e **LicençaDuraçãoRemaining** (por exemplo, 9223372036854775807, que é o valor máximo positivo para um inteiro de 64 bits). Como resultado, a licença Widevine parece caducada e, portanto, a desencriptação não vai acontecer. 

Este problema não ocorre no Android 5.0 Lollipop ou mais tarde, uma vez que o Android 5.0 é a primeira versão Android, que foi projetada para suportar totalmente armv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) e plataformas de 64 bits, enquanto o Android 4.4 KitKat foi originalmente projetado para suportar plataformas ARMv7 e 32 bits como com outras versões Android mais antigas.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Usando Xamarin para construir uma aplicação de reprodução Android

Pode encontrar ligações Xamarin para ExoPlayer utilizando os seguintes links:

- [Biblioteca de encadernações de Xamarin para a biblioteca Do Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Ligações Xamarin para ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Consulte também o seguinte fio: [ligação Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplicativos de jogadores chrome para Android

A partir do lançamento do [Chrome para Android vs. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), é suportada a licença persistente em EME. [A Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) também é suportada no Chrome para Android. Isto permite criar aplicações offline de reprodução no Chrome se os seus utilizadores finais tiverem esta versão (ou superior) do Chrome. 

Além disso, a Google produziu uma amostra de Aplicação Web Progressiva (PWA) e a briu-a: 

- [Código de origem](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versão hospedada pela Google](https://biograf-155113.appspot.com/ttt/episode-2/) (apenas funciona no Chrome v 62 e superior em dispositivos Android)

Se atualizar o seu navegador Chrome móvel para v62 (ou superior) num telefone Android e testar a aplicação de amostras hospedada acima, verá que tanto o streaming online como o reprodução offline funcionam.

A aplicação PWA de código aberto acima é da autoria do Node.js. Se quiser hospedar a sua própria versão num servidor Ubuntu, tenha em mente os seguintes problemas comuns encontrados que podem impedir a reprodução:

1. Problema CORS: O vídeo da amostra na https://storage.googleapis.com/biograf-video-files/videos/aplicação da amostra está hospedado em . A Google criou o CORS para todas as suas amostras de teste alojadas no balde do Armazenamento de Nuvem do Google. São servidos com cabeçalhos CORS, especificando `https://biograf-155113.appspot.com` explicitamente a entrada cors: (o domínio em que o Google acolhe a sua amostra) impedindo o acesso por quaisquer outros sites. Se tentar, verá o seguinte erro HTTP:`Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Emissão de certificado: A partir do Chrome v 58, EME for Widevine requer HTTPS. Por isso, é necessário hospedar a aplicação de amostras em HTTPS com um certificado X509. Um certificado de teste habitual não funciona devido aos seguintes requisitos: É necessário obter um certificado que satisfaça os seguintes requisitos mínimos:
    - Chrome e Firefox exigem que a definição de nome alternativo san-subject exista no certificado
    - O certificado deve ter confiado na AC e um certificado de desenvolvimento auto-assinado não funciona
    - O certificado deve ter um NC que corresponda ao nome DNS do servidor web ou gateway

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="question"></a>Pergunta

Como posso entregar licenças persistentes (offline) para alguns clientes/utilizadores e licenças não persistentes (offline- offline) para outros? Tenho de duplicar o conteúdo e usar a chave de conteúdo separada?

### <a name="answer"></a>Resposta
Não precisa duplicar o conteúdo. Pode simplesmente utilizar uma única cópia do conteúdo e uma única Política de Autorização de Autorização de Conteúdo, mas duas opções separadas de ContentKeyAuthorizationPolicyOption:

1. IContentKeyAuthorizationPolicyOption 1: utiliza licença persistente e ContentKeyAuthorizationPolicyRestriction 1 que contém uma reivindicação como license_type = "Persistente"
2. IContentKeyAuthorizationPolicyOption 2: utiliza licença não persistente e ContentKeyAuthorizationPolicyRestriction 2 que contém uma reclamação como license_type = "Não persistente"

Desta forma, quando um pedido de licença vem da app do cliente, a partir do pedido de licença não há diferença. No entanto, para diferentes utilizadores/dispositivos finais, o STS deve ter a lógica de negócio para emitir diferentes tokens JWT contendo diferentes alegações (uma das duas license_type acima). O valor das reclamações no token JWT será utilizado para decidir pelo serviço de licença para emitir que tipo de licença: persistente ou não persistente.

Isto significa que o Serviço De Token Seguro (STS) precisa de ter a lógica do negócio e informações do cliente/dispositivo para adicionar o valor de reclamação correspondente num símbolo.

### <a name="question"></a>Pergunta

Para os níveis de segurança widevine, na documentação do doc de [arquitetura drm widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) da Google, define três diferentes níveis de segurança. No entanto, na documentação da Azure Media Services sobre o [modelo de licença Widevine,](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)estão delineados cinco níveis de segurança diferentes. Qual é a relação ou o mapeamento entre os dois diferentes conjuntos de níveis de segurança?

### <a name="answer"></a>Resposta

Na visão geral da [Arquitetura DRM widevine](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)da Google, define os seguintes três níveis de segurança:

1.  Nível de Segurança 1: Todo o processamento de conteúdo, criptografia e controlo são realizados no Ambiente de Execução Fidedigna (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em diferentes chips.
2.  Nível de Segurança 2: Executa criptografia (mas não processamento de vídeo) dentro do TEE: os tampões desencriptados são devolvidos ao domínio da aplicação e processados através de hardware ou software de vídeo separados. No nível 2, no entanto, a informação criptográfica ainda é processada apenas dentro do TEE.
3.  Nível de segurança 3 Não tem um TEE no dispositivo. Podem ser tomadas medidas adequadas para proteger as informações criptográficas e o conteúdo desencriptado no sistema operativo do hospedeiro. Uma implementação de Nível 3 também pode incluir um motor criptográfico de hardware, mas que apenas melhora o desempenho, não a segurança.

Ao mesmo tempo, na documentação da Azure Media Services sobre o modelo de [licença Widevine,](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)a propriedade security_level de content_key_specs pode ter os seguintes cinco valores diferentes (requisitos de robustez do cliente para reprodução):

1.  É necessário um cripto cripto de caixa branca baseado em software.
2.  É necessário um crypto de software e um descodificador obfuscado.
3.  O material-chave e as operações de cripto devem ser realizadas dentro de um TEE apoiado por hardware.
4.  A criptoe e descodificação de conteúdos devem ser realizadas dentro de um TEE apoiado por hardware.
5.  O crypto, a descodificação e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados dentro de um TEE apoiado por hardware.

Ambos os níveis de segurança são definidos pelo Google Widevine. A diferença está no seu nível de utilização: nível de arquitetura ou nível de API. Os cinco níveis de segurança são usados na API widevine. O objeto content_key_specs, que contém security_level é desserializado e passado para o serviço de entrega global Widevine pelo serviço de licença Azure Media Services Widevine. A tabela abaixo mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura de grande videira** |**Níveis de segurança utilizados na API de Widevine**|
|---|---| 
| **Nível de Segurança 1:** Todo o processamento de conteúdos, criptografia e controlo são realizados no Ambiente de Execução Fidedigna (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em diferentes chips.|**security_level=5**: O crypto, a descodificação e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados dentro de um TEE apoiado por hardware.<br/><br/>**security_level=4**: A criptoee e a descodificação de conteúdos devem ser realizadas dentro de um TEE apoiado por hardware.|
**Nível de Segurança 2:** Executa criptografia (mas não processamento de vídeo) dentro do TEE: os tampões desencriptados são devolvidos ao domínio da aplicação e processados através de hardware ou software de vídeo separados. No nível 2, no entanto, a informação criptográfica ainda é processada apenas dentro do TEE.| **security_level=3**: O material-chave e as operações de cripto devem ser realizados dentro de um TEE apoiado por hardware. |
| **Nível de segurança 3:** Não tem um TEE no aparelho. Podem ser tomadas medidas adequadas para proteger as informações criptográficas e o conteúdo desencriptado no sistema operativo do hospedeiro. Uma implementação de Nível 3 também pode incluir um motor criptográfico de hardware, mas que apenas melhora o desempenho, não a segurança. | **security_level=2**: É necessário um crypto de software e um descodificador obfuscado.<br/><br/>**security_level=1**: É necessário um cripto de caixa branca baseado em software.|

### <a name="question"></a>Pergunta

Porque é que o download de conteúdos demora tanto tempo?

### <a name="answer"></a>Resposta

Existem duas formas de melhorar a velocidade de descarregamento:

1.  Ative o CDN de modo a que os utilizadores finais sejam mais propensos a atingir cdN em vez de origem/ponto final de streaming para download de conteúdo. Se o utilizador atingir o ponto final de streaming, cada segmento HLS ou fragmento de DASH é embalado e encriptado dinamicamente. Mesmo que esta latência esteja em escala de milissegundos para cada segmento/fragmento, quando você tem um vídeo de uma hora de duração, a latência acumulada pode ser grande causando um download mais longo.
2.  Forneça aos utilizadores finais a opção de descarregar seletivamente camadas de qualidade de vídeo e faixas de áudio em vez de todos os conteúdos. Para o modo offline, não faz sentido descarregar todas as camadas de qualidade. Há duas formas de o conseguir:
    1.  Controlado pelo cliente: ou a aplicação de jogador seleciona automaticamente ou o utilizador seleciona camada de qualidade de vídeo e faixas de áudio para descarregar;
    2.  Controlado pelo serviço: pode-se utilizar a funcionalidade Dynamic Manifest nos Serviços De Mídia Azure para criar um filtro (global), que limita a lista de reprodução de HLS ou OMPD do DASH a uma única camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, o URL de descarregamento apresentado aos utilizadores finais incluirá este filtro.

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="summary"></a>Resumo

Este artigo discutiu como implementar a reprodução de modo offline para conteúdo DASH protegido pela Widevine em dispositivos Android.  Também respondeu a algumas questões comuns relacionadas com o streaming offline de conteúdo protegido da Widevine.
