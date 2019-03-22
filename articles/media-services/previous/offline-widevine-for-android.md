---
title: Configurar a sua conta para a transmissão em fluxo offline do conteúdo do Widevine protegido - Azure
description: Este tópico mostra como configurar a sua conta de Media Services do Azure para transmissão em fluxo offline do Widevine protegido conteúdo.
services: media-services
keywords: Android de modo Offline, ExoPlayer, DASH, DRM, Widevine
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: 9570982e18f2698400c2798dd3e29b0ca6160b8c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309747"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine para transmissão em fluxo para Android  

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versão 3](../latest/offline-widevine-for-android.md)
> * [Versão 2](offline-widevine-for-android.md)

Além de proteger conteúdo para a transmissão em fluxo online, suporte de dados de conteúdo de subscrição e aluguel serviços oferta conteúdo transferível que funciona quando não estiver ligado à internet. Poderá ter de transferir o conteúdo no seu telemóvel ou tablet para reprodução em modo de avião, quando se soltando desligado da rede. Cenários adicionais, em que pode querer transferir conteúdo:

- Alguns fornecedores de conteúdo pode não permitir a entrega de licenças DRM além do limite de um país. Se um usuário desejar ver o conteúdo quando estiverem viajando exterior, é necessário o offline download.
- Em alguns países, disponibilidade de Internet e/ou de largura de banda é limitada. Os utilizadores podem optar por transferir conteúdo para conseguir vê-lo em resolução alta o suficiente para a experiência de visualização satisfatória.

Este artigo discute como implementar a reprodução de modo offline para o conteúdo de TRAÇO protegido pelo Widevine em dispositivos Android. Offline DRM permite-lhe fornecer a subscrição, alugueres e compras modelos para o seu conteúdo, permitindo aos clientes dos seus serviços colocar facilmente conteúdo com os mesmos quando desligado da internet.

Para a criação de aplicações do Android player, descrevemos as três opções:

> [!div class="checklist"]
> * Crie um leitor usando a API de ExoPlayer SDK de Java
> * Crie um leitor usando a ligação do Xamarin do SDK de ExoPlayer
> * Crie um leitor usando a extensão de multimédia encriptadas (EME) e a extensão de origem de mídia (MSE) no Chrome v62 de browser para dispositivos móveis ou posterior

O artigo também responde a algumas perguntas comuns relacionadas com a transmissão em fluxo offline do conteúdo do Widevine protegido.

## <a name="requirements"></a>Requisitos 

Antes de implementar offline DRM para Widevine em dispositivos Android, deve primeiro:

- Familiarize-se com os conceitos apresentados para proteção de conteúdo online com o Widevine DRM. Tudo isso é explicado detalhadamente nos exemplos a seguir documentos /:
    - [Utilizar serviços de multimédia do Azure para entregar licenças DRM ou chaves AES](media-services-deliver-keys-and-licenses.md)
    - [CENC com múltipla DRM e controlo de acesso: Uma estrutura de referência e a implementação no Azure e o Azure dos serviços de multimédia](media-services-cenc-with-multidrm-access-control.md)
    - [Utilizar encriptação comum PlayReady e/ou Widevine dinâmico com .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Utilizar serviços de multimédia do Azure para entregar licenças do PlayReady e/ou Widevine com .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Se familiarizar com o SDK de ExoPlayer Google para Android, um player de vídeo do código-fonte aberto SDK capaz de suportar a reprodução do offline Widevine DRM. 
    - [SDK de ExoPlayer](https://github.com/google/ExoPlayer)
    - [Guia do Programador de ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog de desenvolvedores EoPlayer](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Configuração de proteção de conteúdos nos serviços de multimédia do Azure

Ao configurar a proteção do Widevine de um ativo nos serviços de multimédia, tem de criar ContentKeyAuthorizationPolicyOption que especificou as seguintes três coisas:

1. Sistema DRM (Widevine)
2. ContentKeyAuthorizationPolicyRestriction especificando a entrega de chave como conteúda está autorizado no serviço de entrega de licença (aberto ou token de autorização)
3. Modelo de licença DRM (Widevine)

Para habilitar **offline** modo para licenças do Widevine, terá de configurar [modelo de licença do Widevine](media-services-widevine-license-template-overview.md). No **policy_overrides** de objeto, defina o **can_persist** propriedade **verdadeiro** (a predefinição é falso). 

O código de exemplo seguinte utiliza o .NET para habilitar **offline** modo para licenças do Widevine. O código se baseia a [ usando o PlayReady e/ou Widevine encriptação comum dinâmica com o .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) exemplo. 

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

## <a name="configuring-the-android-player-for-offline-playback"></a>Configurar o Android player para reprodução offline

A maneira mais fácil desenvolver uma aplicação de leitor nativo para dispositivos Android está a utilizar o [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), um player de vídeo do código-fonte aberto SDK. ExoPlayer oferece suporte a recursos não são atualmente suportados pelo API nativa do Android do MediaPlayer, incluindo MPEG-DASH e Smooth Streaming do Microsoft protocolos de entrega.

ExoPlayer versão 2.6 e superior inclui muitas classes que oferecem suporte a reprodução do offline Widevine DRM. Em particular, a classe OfflineLicenseHelper fornece funções de utilitário para facilitar a utilização do DefaultDrmSessionManager para baixar, efetuar a renovação e libertar licenças offline. As classes fornecidas na pasta SDK "biblioteca/core/src/main/java/com/google/android/exoplayer2/offline /" baixar conteúdo offline vídeo de suporte.

A seguinte lista de classes de facilitar o modo offline no SDK do ExoPlayer para Android:

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

Os desenvolvedores devem fazer referência a [Guia do programador ExoPlayer](https://google.github.io/ExoPlayer/guide.html) e correspondentes [blogue para programadores](https://medium.com/google-exoplayer) durante o desenvolvimento de um aplicativo. Google não lançou um referência totalmente documentada implementação ou código de exemplo para a aplicação de ExoPlayer suporte Widevine offline neste momento, pelo que as informações são limitadas para o blog e um guia para programadores. 

### <a name="working-with-older-android-devices"></a>Trabalhar com dispositivos Android mais antigos

Para alguns dispositivos Android mais antigos, tem de definir valores para o seguinte procedimento **policy_overrides** propriedades (definidos na [modelo de licença do Widevine](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, e **license_duration_seconds**. Em alternativa, pode defini-los como zero, o que significa que a duração infinito/ilimitado.  

Os valores tem de ser definidos para evitar um erro de capacidade excedida de número inteiro. Para mais explicações sobre o problema, consulte https://github.com/google/ExoPlayer/issues/3150 e https://github.com/google/ExoPlayer/issues/3112. <br/>Se não definir os valores explicitamente, valores muito grandes para **PlaybackDurationRemaining** e **LicenseDurationRemaining** será atribuída, (por exemplo, 9223372036854775807, o que é o máximo valor positivo para um número inteiro de 64 bits). Como resultado, a licença do Widevine aparece expirada e, por conseguinte, a desencriptação não acontecerá. 

Este problema não ocorre no Android 5.0 Lollipop ou posterior, uma vez que o Android 5.0 é a primeira versão Android, que foi concebida para suportar totalmente ARMv8 ([Advanced o RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) e plataformas de 64 bits, enquanto estava Android 4.4 KitKat projetado originalmente para suportar plataformas de 32 bits como com outras versões mais antigas do Android e ARMv7.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Com o Xamarin para criar uma aplicação Android de reprodução

Pode encontrar os enlaces Xamarin para ExoPlayer utilizando as ligações seguintes:

- [Biblioteca de enlaces Xamarin para a biblioteca de ExoPlayer do Google](https://github.com/martijn00/ExoPlayerXamarin)
- [Enlaces Xamarin do ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Além disso, veja o thread seguinte: [Enlace de Xamarin](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Aplicações de leitor do Chrome para Android

A partir da versão de [Chrome para Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), licença persistente no EME é suportada. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) agora também é suportado no Chrome para Android. Isto permite-lhe criar aplicativos de reprodução offline no Chrome, se os utilizadores finais têm isto (ou superior) versão do Chrome. 

Além disso, o Google tem produziu um exemplo de aplicação de Web progressivo (PWA) e aberto-lo: 

- [Código fonte](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versão do Google alojado](https://biograf-155113.appspot.com/ttt/episode-2/) (só funciona no Chrome v 62 e superior em dispositivos Android)

Se atualizar o browser do dispositivo móvel Chrome para v62 (ou superior) num telemóvel Android e o teste hospedado acima, aplicação de exemplo, verá essa reprodução de transmissão em fluxo tanto online offline de trabalho.

A aplicação de PWA acima do código-fonte aberto foi criada no node. js. Se desejar hospedar sua própria versão num Ubuntu server, tenha em atenção os seguintes problemas de foi encontrados comuns que impedem a reprodução:

1. Problema CORS: O exemplo de vídeo no aplicativo de exemplo está alojado no https://storage.googleapis.com/biograf-video-files/videos/. Google tem de configurar a CORS para todas as suas amostras de teste alojadas no bucket de Google Cloud Storage. Eles são enviados com cabeçalhos CORS, especificar explicitamente a entrada CORS: https://biograf-155113.appspot.com (o domínio no qual google hospeda seu exemplo) a impedir o acesso através de quaisquer outros sites. Se tentar, verá o seguinte erro HTTP: Falha ao carregar https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: nenhum cabeçalho "Access-Control-Allow-Origin" está presente no recurso pedido. Origem "https:\//13.85.80.81:8080" não, portanto, é permitido o acesso. Se uma resposta opaca atende suas necessidades, defina o modo do pedido para "não-cors' para obter o recurso com a CORS desativada.
2. Problema de certificado: A partir do Chrome v 58, EME para Widevine requer HTTPS. Portanto, precisa alojar a aplicação de exemplo através de HTTPS com uma X509 certificado. Um certificado de teste normal não funciona porque os seguintes requisitos: Terá de obter um certificado que cumprem os requisitos mínimos seguintes:
    - Chrome e Firefox exijam a configuração de nome alternativo do requerente de SAN de existir no certificado
    - O certificado AC tem de ter considerado fidedigno e um certificado de desenvolvimento autoassinado não funciona
    - O certificado tem de ter um CN correspondente ao nome DNS do servidor web ou o gateway

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="question"></a>Pergunta

Como posso fornecer licenças persistentes (offline habilitados) para alguns clientes/utilizadores e licenças não persistentes (offline desativado) para outras pessoas? É necessário duplicar o conteúdo e utilizar a chave de conteúdo separada?

### <a name="answer"></a>Resposta
Não é necessário duplicar o conteúdo. Pode simplesmente usar uma única cópia do conteúdo e um único ContentKeyAuthorizationPolicy, mas do duas separado ContentKeyAuthorizationPolicyOption:

1. IContentKeyAuthorizationPolicyOption 1: utiliza licença persistente e ContentKeyAuthorizationPolicyRestriction 1 que contém uma declaração como license_type = "Persistente"
2. IContentKeyAuthorizationPolicyOption 2: utiliza licença não persistentes e ContentKeyAuthorizationPolicyRestriction 2 que contém uma declaração como license_type = "Nonpersistent"

Dessa forma, quando chegar uma solicitação de licença da aplicação de cliente, pedido de licença não há diferença. No entanto, para outro dispositivo/utilizador final, o STS deve ter a lógica de negócios para emitir os tokens JWT diferentes que contêm afirmações diferentes (um do license_type dois acima). O valor de afirmações no JWT token será utilizado para decidir pelo serviço de licença para emitir o tipo de licença: persistentes ou não persistente.

Isso significa que a proteger o serviço de Token (STS) tem de ter as informações de lógica e cliente/dispositivo de negócios para adicionar o valor de afirmação correspondente a um token.

### <a name="question"></a>Pergunta

Para níveis de segurança do Widevine, da Google [documento de visão geral de arquitetura do Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) documentação, ele define três níveis de segurança diferentes. No entanto, no [documentação de serviços de multimédia do Azure no modelo de licença do Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), descrito cinco níveis de segurança diferentes. O que é a relação ou o mapeamento entre os dois conjuntos diferentes de níveis de segurança?

### <a name="answer"></a>Resposta

Da Google [descrição geral de arquitetura do Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), define os níveis de três segurança seguintes:

1.  Nível de segurança 1: Processamento de conteúdo, criptografia e controle de todos os são executadas dentro do ambiente de execução fidedigno (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em chips diferentes.
2.  Nível de segurança 2: Executa criptografia (mas o processamento de vídeo não) dentro do TEE: buffers desencriptados são devolvidos para o domínio do aplicativo e processados por meio de software ou hardware de vídeo separado. No nível 2, no entanto, informações de criptografia ainda estão processadas apenas dentro do TEE.
3.  Nível de segurança 3 não tem um TEE no dispositivo. Podem ser demoradas medidas adequadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operativo anfitrião. Uma implementação de nível 3 também pode incluir um mecanismo de criptografia de hardware, mas que apenas melhora o desempenho, não a segurança.

AT o mesmo tempo, em [documentação de serviços de multimédia do Azure no modelo de licença do Widevine](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), a propriedade security_level de content_key_specs pode ter os seguintes cinco diferentes valores (requisitos de robustez de cliente para a reprodução):

1.  É necessária a criptografia baseada em software whitebox.
2.  Criptografia de software e um Decodificador oculto é necessária.
3.  As operações de criptografia e material de chave tem de ser efetuadas dentro de um hardware de segurança TEE.
4.  A criptografia e decodificação de conteúdo tem de ser efetuadas dentro de um hardware de segurança TEE.
5.  A criptografia, decodificação e tudo manipulação do suporte de dados (comprimidos e descomprimidos) devem ser tratados dentro de um hardware de segurança TEE.

Ambos os níveis de segurança são definidos pelo Google Widevine. A diferença está em seu nível de utilização: nível de arquitetura ou de API. Os níveis de cinco segurança são usados na API do Widevine. O objeto de content_key_specs, que contém security_level é anular a serialização e passado para o serviço de entrega global Widevine pelo serviço de licença do Widevine de serviços de multimédia do Azure. A tabela abaixo mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidas na arquitetura do Widevine** |**Níveis de segurança utilizados na API do Widevine**|
|---|---| 
| **Nível de segurança 1**: Processamento de conteúdo, criptografia e controle de todos os são executadas dentro do ambiente de execução fidedigno (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em chips diferentes.|**security_level=5**: A criptografia, decodificação e tudo manipulação do suporte de dados (comprimidos e descomprimidos) devem ser tratados dentro de um hardware de segurança TEE.<br/><br/>**security_level=4**: A criptografia e decodificação de conteúdo tem de ser efetuadas dentro de um hardware de segurança TEE.|
**Nível de segurança 2**: Executa criptografia (mas o processamento de vídeo não) dentro do TEE: buffers desencriptados são devolvidos para o domínio do aplicativo e processados por meio de software ou hardware de vídeo separado. No nível 2, no entanto, informações de criptografia ainda estão processadas apenas dentro do TEE.| **security_level=3**: As operações de criptografia e material de chave tem de ser efetuadas dentro de um hardware de segurança TEE. |
| **Nível de segurança 3**: Não tem um TEE no dispositivo. Podem ser demoradas medidas adequadas para proteger as informações de criptografia e o conteúdo descriptografado no sistema operativo anfitrião. Uma implementação de nível 3 também pode incluir um mecanismo de criptografia de hardware, mas que apenas melhora o desempenho, não a segurança. | **security_level=2**: Criptografia de software e um Decodificador oculto é necessária.<br/><br/>**security_level=1**: É necessária a criptografia baseada em software whitebox.|

### <a name="question"></a>Pergunta

Por que motivo transferência de conteúdo demora tanto tempo?

### <a name="answer"></a>Resposta

Existem duas formas de melhorar a velocidade de transferência:

1.  Ative a CDN, para que os utilizadores finais têm maior probabilidade de atingir o CDN em vez de ponto final de transmissão em fluxo origem/para transferência de conteúdo. Se o usuário acessa o ponto final de transmissão, cada segmento HLS ou fragmento de TRAÇO é dinamicamente empacotado e encriptado. Apesar desta latência é na escala de milissegundos para cada segmento/fragmento, quando tem uma hora longa vídeo, a latência acumulada pode ser grande, fazendo com que o download mais tempo.
2.  Fornece aos utilizadores finais a opção para transferir seletivamente as camadas de qualidade de vídeo e faixas de áudio em vez de todo o conteúdo. Para o modo offline, não faz sentido para transferir todas as camadas de qualidade. Existem duas formas de fazer isso:
    1.  Cliente controlado: o seleciona automática da aplicação de leitor ou o utilizador seleciona a camada de qualidade de vídeo e faixas de áudio para transferir;
    2.  Serviço controlado: é possível usar o recurso de manifesto dinâmico nos serviços de multimédia do Azure para criar um filtro (global), o que limita a lista de reprodução HLS ou travessão MPD para uma camada de qualidade de vídeo único e selecionado faixas de áudio. Em seguida, o URL de transferência apresentado aos utilizadores finais irão incluir este filtro.

## <a name="summary"></a>Resumo

Este artigo abordou a forma de implementar a reprodução de modo offline para o conteúdo de TRAÇO protegido pelo Widevine em dispositivos Android.  Também respostas para algumas perguntas comuns relacionadas com a transmissão em fluxo offline do conteúdo do Widevine protegido.
