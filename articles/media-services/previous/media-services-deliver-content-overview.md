---
title: Entrega de conteúdo aos clientes
description: Este tópico dá uma visão geral do que está envolvido na entrega do seu conteúdo com a Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1ad89345a2779766fde4559758e61dff92023741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016616"
---
# <a name="deliver-content-to-customers"></a>Entregar conteúdo aos clientes

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Quando está a entregar o seu conteúdo de streaming ou vídeo-on-demand aos clientes, o seu objetivo é entregar vídeos de alta qualidade a vários dispositivos em diferentes condições de rede.

Para atingir este objetivo, pode:

* Codificar o seu fluxo para um fluxo de vídeo multi-bitrate (bitrate adaptativo). Isto cuidará das condições de qualidade e de rede.
* Utilize [a embalagem dinâmica](media-services-dynamic-packaging-overview.md) do Microsoft Azure Media Services para reacondicidá-lo dinamicamente em diferentes protocolos. Isto irá cuidar do streaming em diferentes dispositivos. Os Serviços de Comunicação Social suportam a entrega das seguintes tecnologias de streaming de bitrate adaptativas: <br/>
    * **HTTP Live Streaming** (HLS) - adicione o caminho "(formato=m3u8-aapl)" à parte "/Manifesto" do URL para dizer ao servidor de origem de streaming para devolver o conteúdo HLS para consumo em dispositivos nativos **Apple iOS** (para mais detalhes, ver [localizadores](#locators) e [URLs),](#URLs)
    * **MPEG-DASH** - adicione o caminho "(formato=mpd-time-csf)" à parte "/Manifesto" do URL para dizer ao servidor de origem de streaming para devolver MPEG-DASH (para mais detalhes, ver [localizadores](#locators) e [URLs),](#URLs)
    * **Streaming Suave**.

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

Este artigo apresenta uma visão geral de conceitos importantes de entrega de conteúdos.

Para verificar questões conhecidas, consulte [questões conhecidas.](media-services-deliver-content-overview.md#known-issues)

## <a name="dynamic-packaging"></a>Empacotamento dinâmico
Com a embalagem dinâmica que a Media Services fornece, pode fornecer o seu conteúdo codificado por bitrate adaptável MP4 ou Smooth Streaming em formatos de streaming suportados por Media Services (MPEG-DASH, HLS, Smooth Streaming,) sem ter de voltar a embalar estes formatos de streaming. Recomendamos que entregue o seu conteúdo com embalagens dinâmicas.

Para tirar partido da embalagem dinâmica, é necessário codificar o seu ficheiro mezanino (fonte) num conjunto de ficheiros MP4 de bitragem adaptativa ou ficheiros de streaming suave de bitrato adaptativo.

Com embalagens dinâmicas, armazena e paga os ficheiros em formato único de armazenamento. Os Serviços de Comunicação Social irão construir e servir a resposta adequada com base nos seus pedidos.

A embalagem dinâmica está disponível para pontos finais de streaming standard e premium. 

Para mais informações, consulte [a embalagem Dynamic.](media-services-dynamic-packaging-overview.md)

## <a name="filters-and-dynamic-manifests"></a>Filters and dynamic manifests (Filtros e manifestos dinâmicos)
Pode definir filtros para os seus ativos com Serviços de Media. Estes filtros são regras do lado do servidor que ajudam os seus clientes a fazer coisas como reproduzir uma secção específica de um vídeo ou especificar um subconjunto de representações áudio e vídeo que o dispositivo do seu cliente pode manusear (em vez de todas as representações que estão associadas ao ativo). Esta filtragem é conseguida através de *manifestos dinâmicos* que são criados quando o seu cliente solicita para transmitir um vídeo com base num ou mais filtros especificados.

Para mais informações, consulte [Filtros e manifestos dinâmicos.](media-services-dynamic-manifest-overview.md)

## <a name="locators"></a><a name="locators"></a>Localizadores
Para fornecer ao seu utilizador um URL que pode ser usado para transmitir ou descarregar o seu conteúdo, primeiro tem de publicar o seu ativo criando um localizador. Um localizador fornece um ponto de entrada para aceder aos ficheiros contidos num ativo. Os Media Services suportam dois tipos de localizadores:

* Localizadores OnDemandOrigin. Estes são usados para transmitir meios de transmissão (por exemplo, MPEG-DASH, HLS ou Smooth Streaming) ou descarregar progressivamente ficheiros.
* Localizadores de URL de assinatura de acesso compartilhado (SAS). Estes são usados para transferir ficheiros de mídia para o seu computador local.

Uma *política de acesso* é usada para definir as permissões (como ler, escrever e lista) e duração para a qual um cliente tem acesso a um determinado ativo. Note que a permissão da lista (AccessPermissions.List) não deve ser utilizada na criação de um localizador OnDemandOrigin.

Os localizadores têm datas de validade. O portal Azure estabelece uma data de validade de 100 anos no futuro para os localizadores.

> [!NOTE]
> Se usou o portal Azure para criar localizadores antes de março de 2015, esses localizadores estavam previstos expirar ao fim de dois anos.
> 
> 

Para atualizar uma data de expiração num localizador, utilize as APIs [REST](/rest/api/media/operations/locator#update_a_locator) ou [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator). Observe que, ao atualizar a data de expiração de um localizador SAS, o URL é alterado.

Os localizadores não foram concebidos para gerir o controlo de acesso por utilizador. Pode dar direitos de acesso diferentes a utilizadores individuais utilizando soluções de Gestão de Direitos Digitais (DRM). Para mais informações, consulte [a Secureing Media](/previous-versions/azure/dn282272(v=azure.100)).

Quando se cria um localizador, pode haver um atraso de 30 segundos devido aos processos de armazenamento e propagação necessários no Azure Storage.

## <a name="adaptive-streaming"></a>Streaming adaptativo
As tecnologias bitrate adaptativas permitem que as aplicações dos jogadores de vídeo determinem as condições da rede e selecionem a partir de vários bitrates. Quando a comunicação de rede se degrada, o cliente pode selecionar uma bitrate inferior para que a reprodução possa continuar com menor qualidade de vídeo. À medida que as condições de rede melhoram, o cliente pode mudar para um bitrate mais elevado com uma melhor qualidade de vídeo. A Azure Media Services suporta as seguintes tecnologias de bitrate adaptativas: HTTP Live Streaming (HLS), Smooth Streaming e MPEG-DASH.

Para fornecer urls de streaming aos utilizadores, primeiro tem de criar um localizador OnDemandOrigin. A criação do localizador dá-lhe o caminho base para o ativo que contém o conteúdo que pretende transmitir. No entanto, para poder transmitir este conteúdo, é necessário modificar ainda mais este caminho. Para construir um URL completo no ficheiro manifesto de streaming, tem de concatenar o valor do percurso do localizador e o nome do ficheiro manifesto (filename.ism). Em seguida, apêndice **/Manifesto** e um formato apropriado (se necessário) para o caminho do localizador.

> [!NOTE]
> Também pode transmitir o seu conteúdo através de uma ligação TLS. Para isso, certifique-se de que os seus URLs de streaming começam com HTTPS. Note que, atualmente, a AMS não suporta TLS com domínios personalizados.  
> 

Só é possível transmitir através do TLS se o ponto final de streaming a partir do qual entrega o seu conteúdo foi criado após o dia 10 de setembro de 2014. Se os urLs de streaming forem baseados nos pontos finais de streaming criados após 10 de setembro de 2014, o URL contém "streaming.mediaservices.windows.net". Os URLs de streaming que contenham "origin.mediaservices.windows.net" (o formato antigo) não suportam TLS. Se o seu URL estiver no formato antigo e pretender ser capaz de transmitir através do TLS, crie um novo ponto final de streaming. Utilize URLs com base no novo ponto final de streaming para transmitir o seu conteúdo através de TLS.

## <a name="streaming-url-formats"></a><a name="URLs"></a>Formatos URL de streaming

### <a name="mpeg-dash-format"></a>Formato MPEG-DASH
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) Formato V4
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Formato V3 de streaming ao vivo (HLS) da Apple HTTP
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest (formato=m3u8-aapl-v3)

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Formato Apple HTTP Live Streaming (HLS) com filtro apenas para áudio
Por predefinição, as faixas áudio-only estão incluídas no manifesto HLS. Isto é necessário para a certificação Apple Store para redes celulares. Neste caso, se um cliente não tiver largura de banda suficiente ou estiver ligado a uma ligação 2G, a reprodução muda para áudio-only. Isto ajuda a manter o streaming de conteúdos sem tampão, mas não há vídeo. Em alguns cenários, o tampão dos jogadores pode ser preferido apenas em áudio. Se pretender remover a faixa apenas áudio, adicione **apenas áudio=falso** ao URL.

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest (formato=m3u8-aapl-v3,audio-only=falso)

Para obter mais informações, consulte [o suporte à composição do Manifesto Dinâmico e as funcionalidades adicionais de saída HLS](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Formato de streaming suave
{nome da conta do nome dos serviços multimédia do ponto final de transmissão em fluxo}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Exemplo:

http: \/ /testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Smooth Streaming 2.0 manifesto (manifesto legado)
Por predefinição, o formato manifesto de streaming suave contém a etiqueta de repetição (r-tag). No entanto, alguns jogadores não apoiam a marca r-tag. Os clientes com estes jogadores podem utilizar um formato que desativa a marca r:00:

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest (formato=fmp4-v20)

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)`

## <a name="progressive-download"></a>Download progressivo
Com o download progressivo, pode começar a reproduzir meios de comunicação antes de todo o ficheiro ser descarregado. Não é possível descarregar progressivamente ficheiros .ismv* (ismv, isma, isma ou ismc).

Para descarregar progressivamente o conteúdo, utilize o tipo de localizador OnDemandOrigin. O exemplo a seguir mostra o URL que é baseado no tipo de localizador OnDemandOrigin:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

Você deve desencriptar quaisquer ativos encriptados de armazenamento que você deseja transmitir a partir do serviço de origem para download progressivo.

## <a name="download"></a>Download
Para baixar o seu conteúdo para um dispositivo cliente, tem de criar um Localizador SAS. O localizador SAS dá-lhe acesso ao recipiente de armazenamento Azure onde o seu ficheiro está localizado. Para construir o URL de descarregamento, tem de incorporar o nome do ficheiro entre o anfitrião e a assinatura SAS.

O exemplo a seguir mostra o URL que se baseia no localizador SAS:

`https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D`

As seguintes considerações são aplicáveis:

* Você deve desencriptar quaisquer ativos encriptados de armazenamento que você deseja transmitir a partir do serviço de origem para download progressivo.
* Um download que não tenha terminado dentro de 12 horas falhará.

## <a name="streaming-endpoints"></a>Pontos finais de transmissões em fluxo

Um ponto final de streaming representa um serviço de streaming que pode entregar conteúdo diretamente a uma aplicação do leitor cliente ou a uma rede de entrega de conteúdos (CDN) para posterior distribuição. O fluxo de saída de um serviço de ponto final de streaming pode ser um stream ao vivo ou um ativo vídeo-on-demand na sua conta de Media Services. Existem dois tipos de pontos finais de streaming, **standard** e **premium.** Para obter mais informações, veja [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Descrição geral dos pontos finais de transmissão em fluxo).

>[!NOTE]
>Quando a sua conta AMS é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à sua conta no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. 

## <a name="known-issues"></a>Problemas conhecidos
### <a name="changes-to-smooth-streaming-manifest-version"></a>Alterações na versão manifesta de streaming suave
Antes do lançamento do serviço de julho de 2016,quando os ativos produzidos pela Media Encoder Standard, Media Encoder Premium Workflow, ou o anterior Azure Media Encoder foram transmitidos através de embalagens dinâmicas -- o manifesto de streaming suave devolvido estaria em conformidade com a versão 2.0. Na versão 2.0, as durações dos fragmentos não utilizam as chamadas tags repeat ('r'). Por exemplo:

```xml
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
```

No lançamento do serviço julho de 2016, o manifesto de streaming suave gerado está em conformidade com a versão 2.2, com durações de fragmentos utilizando etiquetas repetidas. Por exemplo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" r="4" />
    </StreamIndex>
</SmoothStreamingMedia>
```

Alguns dos clientes de Streaming Smooth podem não suportar as etiquetas de repetição e não carregarão o manifesto. Para mitigar este problema, pode utilizar o parâmetro de formato manifesto legado **(formato=fmp4-v20)** ou atualizar o seu cliente para a versão mais recente, que suporta marcas repetidas. Para mais informações, consulte [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Atualizar localizadores de Serviços de Mídia após as chaves de armazenamento rolantes](media-services-roll-storage-access-keys.md)