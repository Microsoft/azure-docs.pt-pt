---
title: Entrega de conteúdo aos clientes | Microsoft Docs
description: Este tópico fornece uma visão geral do que está envolvido no fornecimento de conteúdo com os serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 22d98656f42f52f2fba0845fac6f1d210d2cf0bd
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264683"
---
# <a name="deliver-content-to-customers"></a>Entregar conteúdo aos clientes
Quando você está entregando seu conteúdo de streaming ou vídeo sob demanda aos clientes, seu objetivo é fornecer vídeo de alta qualidade para vários dispositivos sob condições de rede diferentes.

Para atingir essa meta, você pode:

* Codifique seu fluxo para um fluxo de vídeo de múltiplas taxas de bits (taxa de bits adaptável). Isso cuidará da qualidade e das condições de rede.
* Use Serviços de Mídia do Microsoft Azure [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) para reempacotar dinamicamente seu fluxo em protocolos diferentes. Isso cuidará do streaming em dispositivos diferentes. Os serviços de mídia oferecem suporte à entrega das seguintes tecnologias de streaming de taxa de bits adaptável: <br/>
    * **Http Live streaming** (hls) – adicione o caminho "(Format = M3U8-AAPL)" à parte "/manifest" da URL para informar ao servidor de origem de streaming para retornar o conteúdo de HLS para consumo em dispositivos nativos da **Apple Ios** (para obter detalhes, consulte [localizadores](#locators) e [URLs](#URLs)),
    * **MPEG-Dash** – adicione o caminho "(Format = MPD-time-CSF)" à parte "/manifest" da URL para informar ao servidor de origem de streaming para retornar MPEG-Dash (para obter detalhes, consulte [localizadores](#locators) e [URLs](#URLs)),
    * **Smooth streaming**.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Este artigo fornece uma visão geral dos conceitos importantes de fornecimento de conteúdo.

Para verificar problemas conhecidos, consulte [problemas conhecidos](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Empacotamento dinâmico
Com o empacotamento dinâmico que os serviços de mídia fornecem, você pode entregar seu conteúdo codificado para MP4 ou Smooth Streaming de taxa de bits adaptável em formatos de streaming com suporte dos serviços de mídia (MPEG-DASH, HLS, Smooth Streaming,) sem precisar reempacotar nesses formatos de streaming. É recomendável entregar seu conteúdo com o empacotamento dinâmico.

Para aproveitar o empacotamento dinâmico, você precisa codificar seu arquivo de mezanino (fonte) em um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável.

Com o empacotamento dinâmico, você armazena e paga pelos arquivos em um único formato de armazenamento. Os serviços de mídia irão criar e fornecer a resposta apropriada com base em suas solicitações.

O empacotamento dinâmico está disponível para pontos de extremidade de streaming Standard e Premium. 

Para obter mais informações, consulte [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos
Você pode definir filtros para seus ativos com os serviços de mídia. Esses filtros são regras do lado do servidor que ajudam os clientes a fazer coisas como reproduzir uma seção específica de um vídeo ou especificar um subconjunto de representações de áudio e vídeo que o dispositivo do cliente pode manipular (em vez de todas as representações associadas ao ativo). Essa filtragem é obtida por meio de *manifestos dinâmicos* que são criados quando o cliente solicita a transmissão de um vídeo com base em um ou mais filtros especificados.

Para obter mais informações, consulte [filtros e dos manifestos dinâmicos](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a>Localizadores de <a id="locators"/>
Para fornecer ao usuário uma URL que pode ser usada para transmitir ou baixar seu conteúdo, primeiro você precisa publicar seu ativo criando um localizador. Um localizador fornece um ponto de entrada para acessar os arquivos contidos em um ativo. Os Media Services suportam dois tipos de localizadores:

* Localizadores OnDemandOrigin. Eles são usados para transmitir mídia (por exemplo, MPEG-DASH, HLS ou Smooth Streaming) ou baixar arquivos progressivamente.
* Localizadores de URL de SAS (assinatura de acesso compartilhado). Eles são usados para baixar arquivos de mídia em seu computador local.

Uma *política de acesso* é usada para definir as permissões (como leitura, gravação e lista) e a duração pela qual um cliente tem acesso para um determinado ativo. Observe que a permissão de lista (AccessPermissions. List) não deve ser usada na criação de um localizador OnDemandOrigin.

Os localizadores têm datas de expiração. O portal do Azure define uma data de validade de 100 anos no futuro para localizadores.

> [!NOTE]
> Se você usou o portal do Azure para criar localizadores antes de 2015 de março, esses localizadores foram definidos para expirar após dois anos.
> 
> 

Para atualizar uma data de expiração num localizador, utilize as APIs [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Observe que, ao atualizar a data de expiração de um localizador SAS, o URL é alterado.

Os localizadores não foram projetados para gerenciar o controle de acesso por usuário. Você pode conceder direitos de acesso diferentes a usuários individuais usando soluções de Rights Management digital (DRM). Para obter mais informações, consulte [Securing Media](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando você cria um localizador, pode haver um atraso de 30 segundos devido a processos de armazenamento e de propagação necessários no armazenamento do Azure.

## <a name="adaptive-streaming"></a>Streaming adaptável
As tecnologias de taxa de bits adaptável permitem que os aplicativos de player de vídeo determinem as condições de rede e selecionem várias taxas de bits. Quando a comunicação de rede degrada, o cliente pode selecionar uma taxa de bits inferior para que a reprodução possa continuar com uma qualidade de vídeo inferior. Conforme as condições de rede aumentam, o cliente pode alternar para uma taxa de bits mais alta com qualidade de vídeo aprimorada. Os serviços de mídia do Azure dão suporte às seguintes tecnologias de taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming e MPEG-DASH.

Para fornecer aos usuários URLs de streaming, você deve primeiro criar um localizador OnDemandOrigin. A criação do localizador fornece o caminho base para o ativo que contém o conteúdo que você deseja transmitir. No entanto, para poder transmitir esse conteúdo, você precisa modificar esse caminho ainda mais. Para construir uma URL completa para o arquivo de manifesto de streaming, você deve concatenar o valor do caminho do localizador e o nome do arquivo de manifesto (filename. ISM). Em seguida, acrescente **/manifest** e um formato apropriado (se necessário) ao caminho do localizador.

> [!NOTE]
> Você também pode transmitir seu conteúdo por uma conexão SSL. Para fazer isso, verifique se suas URLs de streaming começam com HTTPS. Observe que, atualmente, o AMS não dá suporte a SSL com domínios personalizados.  
> 

Você só poderá transmitir por SSL se o ponto de extremidade de streaming do qual você entregar seu conteúdo tiver sido criado após 10 de setembro de 2014. Se suas URLs de streaming forem baseadas nos pontos de extremidade de streaming criados após 10 de setembro de 2014, a URL conterá "streaming.mediaservices.windows.net". As URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não oferecem suporte a SSL. Se a URL estiver no formato antigo e você quiser ser capaz de transmitir por SSL, crie um novo ponto de extremidade de streaming. Use URLs com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>formatos de URL de streaming

### <a name="mpeg-dash-format"></a>Formato MPEG-DASH
{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = MPD-time-CSF)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Formato do Apple HTTP Live Streaming (HLS) v4
{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = M3U8-AAPL)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato da Apple HTTP Live Streaming (HLS) v3
{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = M3U8-AAPL-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato Apple HTTP Live Streaming (HLS) com filtro somente áudio
Por padrão, as faixas somente áudio são incluídas no manifesto HLS. Isso é necessário para a certificação da Apple Store para redes de celular. Nesse caso, se um cliente não tiver largura de banda suficiente ou estiver conectado por uma conexão 2G, a reprodução alternará para somente áudio. Isso ajuda a manter o fluxo de conteúdo sem buffer, mas não há vídeo. Em alguns cenários, o armazenamento em buffer do player pode ser preferível somente em áudio. Se você quiser remover a faixa somente de áudio, adicione **somente áudio = false** à URL.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (Format = M3U8-AAPL-v3, áudio-only = false)

Para obter mais informações, consulte [suporte de composição de manifesto dinâmico e recursos adicionais de saída de HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formato de Smooth Streaming
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemplo:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Manifesto do Smooth Streaming 2,0 (manifesto herdado)
Por padrão, Smooth Streaming formato de manifesto contém a marca de repetição (r-tag). No entanto, alguns jogadores não dão suporte à marca r. Os clientes com esses players podem usar um formato que desabilita a marca r:

{nome do ponto de extremidade de streaming-nome da conta dos serviços de mídia}. streaming. mediaservices. Windows. net/{ID do localizador}/{nome (Format = fmp4-V20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Download progressivo
Com o download progressivo, você pode iniciar a reprodução de mídia antes de o arquivo inteiro ter sido baixado. Não é possível baixar progressivamente arquivos. ISM * (ismv, Isma, ISMT ou ismc).

Para baixar o conteúdo progressivamente, use o tipo de localizador OnDemandOrigin. O exemplo a seguir mostra a URL com base no tipo de localizador OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Você deve descriptografar todos os ativos criptografados pelo armazenamento que deseja transmitir do serviço de origem para download progressivo.

## <a name="download"></a>Transferir
Para baixar seu conteúdo em um dispositivo cliente, você deve criar um localizador SAS. O localizador SAS fornece acesso ao contêiner de armazenamento do Azure em que o arquivo está localizado. Para criar a URL de download, você precisa inserir o nome do arquivo entre o host e a assinatura SAS.

O exemplo a seguir mostra a URL com base no localizador SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

As seguintes considerações são aplicáveis:

* Você deve descriptografar todos os ativos criptografados pelo armazenamento que deseja transmitir do serviço de origem para download progressivo.
* Um download que não foi concluído dentro de 12 horas falhará.

## <a name="streaming-endpoints"></a>Pontos finais de transmissões em fluxo

Um ponto de extremidade de streaming representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou a uma CDN (rede de distribuição de conteúdo) para distribuição posterior. O fluxo de saída de um serviço de ponto de extremidade de streaming pode ser uma transmissão ao vivo ou um ativo de vídeo sob demanda em sua conta de serviços de mídia. Há dois tipos de pontos de extremidade de streaming, **Standard** e **Premium**. Para obter mais informações, veja [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Descrição geral dos pontos finais de transmissão em fluxo).

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

## <a name="known-issues"></a>Problemas conhecidos
### <a name="changes-to-smooth-streaming-manifest-version"></a>Alterações na versão Smooth Streaming do manifesto
Antes da versão de serviço de julho de 2016--quando os ativos produzidos por Media Encoder Standard, Media Encoder Premium Workflow ou o codificador de mídia do Azure anterior foram transmitidos usando o empacotamento dinâmico – o manifesto de Smooth Streaming retornado estaria de acordo com a versão 2,0. Na versão 2,0, as durações do fragmento não usam as chamadas de marcas de repetição (' r '). Por exemplo:


    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

Na versão de serviço de julho de 2016, o manifesto Smooth Streaming gerado está em conformidade com a versão 2,2, com durações de fragmento usando marcas de repetição. Por exemplo:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Alguns dos clientes herdados do Smooth Streaming podem não dar suporte às marcas de repetição e não poderão carregar o manifesto. Para atenuar esse problema, você pode usar o parâmetro de formato de manifesto herdado **(Format = fmp4-V20)** ou atualizar o cliente para a versão mais recente, que dá suporte a marcas de repetição. Para obter mais informações, consulte [Smooth Streaming 2,0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Atualizar localizadores de serviços de mídia após as chaves de armazenamento sem interrupção](media-services-roll-storage-access-keys.md)

