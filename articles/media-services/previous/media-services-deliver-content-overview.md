---
title: Entrega de conteúdos aos clientes Microsoft Docs
description: Este tema dá uma visão geral do que está envolvido na entrega dos seus conteúdos com a Azure Media Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264683"
---
# <a name="deliver-content-to-customers"></a>Entregar conteúdo aos clientes
Ao entregar os seus conteúdos de streaming ou vídeo-on-demand aos clientes, o seu objetivo é entregar vídeos de alta qualidade a vários dispositivos em diferentes condições de rede.

Para atingir este objetivo, pode:

* Codifique o seu fluxo para um fluxo de vídeo multibitável (bitrate adaptativo). Isto cuidará da qualidade e das condições de rede.
* Utilize [embalagens dinâmicas](media-services-dynamic-packaging-overview.md) do Microsoft Azure Media Services para reembalar dinamicamente o seu fluxo em diferentes protocolos. Isto cuidará do streaming em diferentes dispositivos. A Media Services apoia a entrega das seguintes tecnologias de streaming de bitrate adaptáveis: <br/>
    * **HTTP Live Streaming** (HLS) - adicione o caminho "(formato=m3u8-aapl)" para a parte "/Manifesto" do URL para dizer ao servidor de origem streaming para devolver conteúdo hls para consumo em dispositivos nativos **apple iOS** (para detalhes, ver [localizadores](#locators) e [URLs),](#URLs)
    * **MPEG-DASH** - adicione o caminho "((formato=mpd-time-csf)" para a parte "/Manifesto" do URL para dizer ao servidor de origem de streaming para devolver o MPEG-DASH (para mais detalhes, ver [localizadores](#locators) e [URLs),](#URLs)
    * **Streaming suave.**

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Este artigo dá uma visão geral de conceitos importantes de entrega de conteúdos.

Para verificar questões conhecidas, consulte [questões conhecidas.](media-services-deliver-content-overview.md#known-issues)

## <a name="dynamic-packaging"></a>Empacotamento dinâmico
Com a embalagem dinâmica que a Media Services fornece, pode entregar o seu conteúdo codificado de bitrate adaptativo MP4 ou Smooth Streaming em formatos de streaming suportados por Media Services (MPEG-DASH, HLS, Smooth Streaming,) sem ter de voltar a embalar estes formatos de streaming. Recomendamos que entregue o seu conteúdo com embalagens dinâmicas.

Para tirar partido da embalagem dinâmica, é necessário codificar o seu ficheiro mezanino (fonte) num conjunto de ficheiros MP4 de bitrate adaptativo ou ficheiros de streaming suave de bitrate adaptativo.

Com embalagens dinâmicas, armazena e paga os ficheiros em formato de armazenamento único. A Media Services construirá e servirá a resposta adequada com base nos seus pedidos.

A embalagem dinâmica está disponível para pontos finais de streaming standard e premium. 

Para mais informações, consulte [a embalagem Dynamic](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filters and dynamic manifests (Filtros e manifestos dinâmicos)
Pode definir filtros para os seus ativos com Serviços de Media. Estes filtros são regras do lado do servidor que ajudam os seus clientes a fazer coisas como reproduzir uma secção específica de um vídeo ou especificar um subconjunto de representações de áudio e vídeo que o dispositivo do seu cliente pode lidar (em vez de todas as representações que estão associadas ao ativo). Esta filtragem é conseguida através de *manifestos dinâmicos* que são criados quando o seu cliente pede para transmitir um vídeo com base num ou mais filtros especificados.

Para mais informações, consulte [Filtros e manifestos dinâmicos.](media-services-dynamic-manifest-overview.md)

## <a name="locators"></a><a id="locators"/>Localizadores
Para fornecer ao utilizador um URL que possa ser usado para transmitir ou descarregar o seu conteúdo, primeiro precisa de publicar o seu ativo criando um localizador. Um localizador fornece um ponto de entrada para aceder aos ficheiros contidos num ativo. Os Media Services suportam dois tipos de localizadores:

* Localizadores OnDemandOrigin. Estes são utilizados para transmitir meios de comunicação (por exemplo, MPEG-DASH, HLS ou Smooth Streaming) ou descarregar progressivamente ficheiros.
* Localizadores de URL de assinatura de acesso partilhado (SAS). Estes são usados para descarregar ficheiros de mídia para o seu computador local.

Uma política de *acesso* é usada para definir as permissões (como ler, escrever e lista) e duração para a qual um cliente tem acesso a um determinado ativo. Note que a permissão da lista (AccessPermissions.List) não deve ser utilizada na criação de um localizador OnDemandOrigin.

Os localizadores têm datas de validade. O portal Azure fixa uma data de validade de 100 anos no futuro para os localizadores.

> [!NOTE]
> Se usou o portal Azure para criar localizadores antes de março de 2015, esses localizadores estavam previstos para expirar após dois anos.
> 
> 

Para atualizar uma data de expiração num localizador, utilize as APIs [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) ou [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). Observe que, ao atualizar a data de expiração de um localizador SAS, o URL é alterado.

Os localizadores não foram concebidos para gerir o controlo de acesso por utilizador. Pode dar diferentes direitos de acesso a utilizadores individuais utilizando soluções de Gestão de Direitos Digitais (DRM). Para mais informações, consulte [A Securing Media](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Quando se cria um localizador, pode haver um atraso de 30 segundos devido aos processos de armazenamento e propagação necessários no Armazenamento Azure.

## <a name="adaptive-streaming"></a>Streaming adaptativo
As tecnologias de bitrate adaptável permitem que as aplicações de jogadores de vídeo determinem as condições da rede e selecionem a partir de várias bitrates. Quando a comunicação de rede se degrada, o cliente pode selecionar um bitrate mais baixo para que a reprodução possa continuar com menor qualidade de vídeo. À medida que as condições de rede melhoram, o cliente pode mudar para um bitrate mais elevado com melhor qualidade de vídeo. A Azure Media Services suporta as seguintes tecnologias de bitrate adaptativo: HTTP Live Streaming (HLS), Smooth Streaming e MPEG-DASH.

Para fornecer URLs de streaming aos utilizadores, primeiro deve criar um localizador OnDemandOrigin. A criação do localizador dá-lhe o caminho base para o ativo que contém o conteúdo que pretende transmitir. No entanto, para poder transmitir este conteúdo, precisa de modificar ainda mais este caminho. Para construir um URL completo para o ficheiro manifesto de streaming, deve concatenar o valor do percurso do localizador e o nome de ficheiro manifesto (nome de ficheiro.ism). Em seguida, anexar **/Manifesto** e um formato apropriado (se necessário) para o caminho do localizador.

> [!NOTE]
> Também pode transmitir o seu conteúdo através de uma ligação SSL. Para isso, certifique-se de que os seus URLs de streaming começam com HTTPS. Note que, atualmente, a AMS não suporta a SSL com domínios personalizados.  
> 

Só é possível transmitir através do SSL se o ponto final de streaming a partir do qual entrega o seu conteúdo for criado após 10 de setembro de 2014. Se os seus URLs de streaming forem baseados nos pontos finais de streaming criados após 10 de setembro de 2014, o URL contém "streaming.mediaservices.windows.net". Os URLs de streaming que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam O SSL. Se o seu URL estiver no formato antigo e quiser ser capaz de transmitir sobre o SSL, crie um novo ponto final de streaming. Utilize URLs com base no novo ponto final de streaming para transmitir o seu conteúdo através de SSL.

## <a name="streaming-url-formats"></a><a id="URLs"/>Formatos URL de streaming

### <a name="mpeg-dash-format"></a>Formato MPEG-DASH
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4 formato
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3 formato
{nome da conta de serviços de nome de mídia de ponto final de streaming}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifesto (formato=m3u8-apl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato Apple HTTP Live Streaming (HLS) com filtro só para áudio
Por predefinição, as faixas apenas áudio estão incluídas no manifesto HLS. Isto é necessário para a certificação Apple Store para redes celulares. Neste caso, se um cliente não tiver largura de banda suficiente ou estiver ligado sobre uma ligação 2G, a reprodução muda apenas para áudio. Isto ajuda a manter o streaming de conteúdos sem tampão, mas não há vídeo. Em alguns cenários, o tampão dos jogadores pode ser preferido apenas em áudio. Se pretender remover a faixa só de áudio, adicione **apenas áudio=falso** ao URL.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl-v3,audio-only=falso)

Para obter mais informações, consulte o suporte dinâmico da [Composição Manifesto e as funcionalidades adicionais](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)de saída do HLS .

### <a name="smooth-streaming-format"></a>Formato De Streaming Suave
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemplo:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Manifesto smooth Streaming 2.0 (manifesto legado)
Por predefinição, o formato manifesto Smooth Streaming contém a etiqueta de repetição (r-tag). No entanto, alguns jogadores não suportam a r-tag. Os clientes com estes jogadores podem usar um formato que desativa a r-tag:

{nome da conta de serviços de nome sem fim de ponto final}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifesto(formato=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Download progressivo
Com o download progressivo, pode começar a reproduzir os meios de comunicação antes de todo o ficheiro ter sido descarregado. Não é possível descarregar progressivamente ficheiros .ism* (ismv, isma, ismt, ou ismc).

Para descarregar progressivamente os conteúdos, utilize o tipo de localizador OnDemandOrigin. O exemplo seguinte mostra o URL que é baseado no tipo de localizador OnDemandOrigin:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Deve desencriptar quaisquer ativos encriptados de armazenamento que pretenda transmitir do serviço de origem para download progressivo.

## <a name="download"></a>Transferência
Para descarregar o seu conteúdo para um dispositivo cliente, tem de criar um Localizador SAS. O localizador SAS dá-lhe acesso ao contentor de armazenamento Azure onde está localizado o seu ficheiro. Para construir o URL de descarregamento, tem de incorporar o nome de ficheiro entre o anfitrião e a assinatura SAS.

O exemplo que se segue mostra o URL que é baseado no localizador SAS:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

As seguintes considerações são aplicáveis:

* Deve desencriptar quaisquer ativos encriptados de armazenamento que pretenda transmitir do serviço de origem para download progressivo.
* Um download que não tenha terminado dentro de 12 horas falhará.

## <a name="streaming-endpoints"></a>Pontos finais de transmissões em fluxo

Um ponto final de streaming representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação de jogador de cliente ou a uma rede de entrega de conteúdos (CDN) para posterior distribuição. O fluxo de saída de um serviço de ponto final de streaming pode ser um stream ao vivo ou um ativo de vídeo a pedido na sua conta de Media Services. Existem dois tipos de pontos finais de streaming, **standard** e **premium.** Para obter mais informações, veja [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Descrição geral dos pontos finais de transmissão em fluxo).

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

## <a name="known-issues"></a>Problemas conhecidos
### <a name="changes-to-smooth-streaming-manifest-version"></a>Alterações na versão manifesto smooth streaming
Antes da libertação do serviço de julho de 2016-- quando os ativos produzidos pela Media Encoder Standard, Media Encoder Premium Workflow, ou o anterior Encoder Azure Media foram transmitidos usando embalagens dinâmicas -- o manifesto Smooth Streaming devolvido estaria em conformidade com a versão 2.0. Na versão 2.0, as durações do fragmento não utilizam as chamadas etiquetas de repetição ('r'). Por exemplo:


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

No lançamento do serviço de julho de 2016, o manifesto de Streaming Smooth gerado está em conformidade com a versão 2.2, com durações de fragmentos utilizando tags repetidas. Por exemplo:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Alguns dos clientes do Smooth Streaming do legado podem não suportar as etiquetas de repetição e não irão carregar o manifesto. Para mitigar este problema, pode utilizar o parâmetro de formato manifesto legado **(formato=fmp4-v20)** ou atualizar o seu cliente para a versão mais recente, que suporta tags de repetição. Para mais informações, consulte [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Atualizar os localizadores dos Media Services após as chaves de armazenamento de rolamento](media-services-roll-storage-access-keys.md)

