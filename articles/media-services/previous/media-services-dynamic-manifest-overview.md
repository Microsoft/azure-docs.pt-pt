---
title: Filtros e manifestos dinâmicos Microsoft Docs
description: Este tópico descreve como criar filtros para que o seu cliente possa usá-los para transmitir secções específicas de um fluxo. A Media Services cria manifestos dinâmicos para arquivar este streaming seletivo.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 1234263fa800a17d0a5c235df54ca2751e3094bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69015855"
---
# <a name="filters-and-dynamic-manifests"></a>Filters and dynamic manifests (Filtros e manifestos dinâmicos)

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 2](media-services-dynamic-manifest-overview.md)
> * [Versão 3](../latest/filters-dynamic-manifest-overview.md)

A partir do lançamento de 2.17, o Media Services permite-lhe definir filtros para os seus ativos. Estes filtros são regras do lado do servidor que permitirão que os seus clientes escolham fazer coisas como: reproduzir apenas uma secção de um vídeo (em vez de reproduzir todo o vídeo), ou especificar apenas um subconjunto de representações de áudio e vídeo que o dispositivo do seu cliente pode lidar ( em vez de reproduzir todo o vídeo), ou especificar apenas um subconjunto de representações de áudio e vídeo que o dispositivo do seu cliente pode lidar ( em vez de todas as representações que estão associadas ao ativo). Esta filtragem dos seus ativos é conseguida através do **Dynamic Manifest**s que são criados a pedido do seu cliente para transmitir um vídeo com base em filtros especificados.

Este tópico discute cenários comuns em que o uso de filtros seria benéfico para os seus clientes e ligações a tópicos que demonstram como criar filtros programáticamente.

## <a name="overview"></a>Descrição geral
Ao entregar o seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo-on-demand) o seu objetivo é entregar um vídeo de alta qualidade a vários dispositivos em diferentes condições de rede. Para atingir este objetivo faça o seguinte:

* codificar o seu fluxo para fluxo de vídeo multibitável[(bitrate adaptativo](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (isto cuidará da qualidade e das condições de rede) e 
* utilizar a Media Services [Dynamic Packaging](media-services-dynamic-packaging-overview.md) para reembalar dinamicamente o seu fluxo em diferentes protocolos (isto cuidará do streaming em diferentes dispositivos). A Media Services suporta a entrega das seguintes tecnologias de streaming adaptativo: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH. 

### <a name="manifest-files"></a>Ficheiros manifestos
Quando codifica um ativo para streaming de bitrate adaptativo, é criado um ficheiro **manifesto** (lista de reprodução) (o ficheiro é baseado em texto ou baseado em XML). O ficheiro **manifesto** inclui metadados de streaming tais como: tipo de faixa (áudio, vídeo ou texto), nome da faixa, tempo de início e fim, bitrate (qualidades), linguagem de faixa, janela de apresentação (janela deslizante de duração fixa), código de vídeo (FourCC). Também instrui o jogador a recuperar o próximo fragmento fornecendo informações sobre os próximos fragmentos de vídeo jogáveis disponíveis e a sua localização. Fragmentos (ou segmentos) são os "pedaços" reais de um conteúdo de vídeo.

Aqui está um exemplo de um ficheiro manifesto: 

    <?xml version="1.0" encoding="UTF-8"?>    
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

    <StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
    <QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
    <QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
    <QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
    <QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
    <QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
    <QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
    <QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

    <c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
    </StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


    <StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
    <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

    <c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

    </SmoothStreamingMedia>

### <a name="dynamic-manifests"></a>Manifestos dinâmicos
Há [cenários](media-services-dynamic-manifest-overview.md#scenarios) em que o seu cliente precisa de mais flexibilidade do que o descrito no ficheiro manifesto do ativo predefinido. Por exemplo:

* Específico do dispositivo: entregar apenas as interpretações especificadas e/ou faixas linguísticas especificadas que são suportadas pelo dispositivo que é utilizado para reproduzir o conteúdo ("filtragem de rendição"). 
* Reduza o manifesto para mostrar um subclip de um evento ao vivo ("filtragem de sub-clip").
* Aparar o início de um vídeo ("aparar um vídeo").
* Ajuste a Janela de Apresentação (DVR) de forma a fornecer um comprimento limitado da janela DVR no leitor ("janela de apresentação de ajuste").

Para alcançar esta flexibilidade, a Media Services oferece **Manifestos Dinâmicos baseados** em filtros pré-definidos. [filters](media-services-dynamic-manifest-overview.md#filters)  Uma vez definidos os filtros, os seus clientes podem usá-los para transmitir uma versão específica ou subclips do seu vídeo. Especificariam os filtros no URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de bitrate adaptáveis suportados pela [Dynamic Packaging](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH e Smooth Streaming. Por exemplo:

URL mpeg DASH com filtro

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de streaming suave com filtro

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Para obter mais informações sobre como entregar o seu conteúdo e construir URLs de streaming, consulte a [visão geral](media-services-deliver-content-overview.md)do conteúdo .

> [!NOTE]
> Note que os Manifestos Dinâmicos não alteram o ativo e o manifesto padrão para esse ativo. O seu cliente pode optar por solicitar um fluxo com ou sem filtros. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filtros
Existem dois tipos de filtros de ativos: 

* Filtros globais (podem ser aplicados a qualquer ativo na conta Azure Media Services, têm uma vida útil da conta) e 
* Os filtros locais (só podem ser aplicados a um ativo com o qual o filtro foi associado à criação, têm uma vida útil do ativo). 

Os tipos de filtros globais e locais têm exatamente as mesmas propriedades. A principal diferença entre os dois é para os cenários que tipo de filer é mais adequado. Os filtros globais são geralmente adequados para perfis de dispositivos (filtragem de rendição) onde os filtros locais podem ser usados para aparar um ativo específico.

## <a name="common-scenarios"></a><a id="scenarios"></a>Cenários comuns
Como já foi referido, ao entregar o seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo-on-demand) o seu objetivo é entregar um vídeo de alta qualidade a vários dispositivos em diferentes condições de rede. Além disso, poderá ter outros requisitos que envolvem filtrar os seus ativos e utilizar o **Dynamic Manifest**s. As seguintes secções dão uma visão geral breve de diferentes cenários de filtragem.

* Especifique apenas um subconjunto de representações áudio e vídeo que certos dispositivos podem manusear (em vez de todas as representações que estão associadas ao ativo). 
* Reproduzindo apenas uma secção de um vídeo (em vez de reproduzir todo o vídeo).
* Ajuste a janela de apresentação do DVR.

## <a name="rendition-filtering"></a>Filtragem de rendição
Pode optar por codificar o seu ativo a múltiplos perfis de codificação (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) e a várias bitrates de qualidade. No entanto, nem todos os dispositivos de cliente irão suportar todos os perfis e bitrates do seu ativo. Por exemplo, dispositivos Android mais antigos suportam apenas H.264 Baseline+AACL. Envio de bitrates mais elevados para um dispositivo que não consegue obter os benefícios, desperdiça largura de banda e computação de dispositivos. Este dispositivo deve descodificar todas as informações dadas, apenas para a reduzir para visualização.

Com o Dynamic Manifest, pode criar perfis de dispositivos como mobile, consola, HD/SD, etc. e incluir as faixas e qualidades que pretende fazer parte de cada perfil.

![Exemplo de filtragem de rendição][renditions2]

No exemplo seguinte, um codificador foi usado para codificar um ativo mezanino em sete representações de vídeo ISO MP4s (de 180p a 1080p). O ativo codificado pode ser embalado dinamicamente em qualquer um dos seguintes protocolos de streaming: HLS, Smooth e MPEG DASH.  Na parte superior do diagrama, mostra-se o manifesto HLS para o ativo sem filtros (contém todas as sete representações).  No canto inferior esquerdo, é mostrado o manifesto HLS ao qual foi aplicado um filtro chamado "ott". O filtro "ott" especifica remover todas as bitrates abaixo de 1Mbps, o que resultou na eliminação dos dois níveis de qualidade inferiores na resposta. No canto inferior direito, é mostrado o manifesto HLS a que foi aplicado um filtro chamado "mobile". O filtro "móvel" especifica remover as representações em que a resolução é superior a 720p, o que resultou na retirada das duas representações de 1080p.

![Filtragem de rendição][renditions1]

## <a name="removing-language-tracks"></a>Remoção de faixas linguísticas
Os seus ativos podem incluir várias línguas áudio, tais como inglês, espanhol, francês, etc. Normalmente, os gestores do Player SDK padrão seleção de faixas de áudio e faixas de áudio disponíveis por seleção do utilizador. É um desafio desenvolver tais SDKs player, requer implementações diferentes em diferentes quadros de jogadores específicos do dispositivo. Além disso, em algumas plataformas, as APIs do Jogador são limitadas e não incluem a funcionalidade de seleção de áudio onde os utilizadores não podem selecionar ou alterar a faixa de áudio padrão. Com filtros de ativos, pode controlar o comportamento criando filtros que apenas incluem idiomas áudio desejados.

![Filtragem de faixas linguísticas][language_filter]

## <a name="trimming-start-of-an-asset"></a>Aparar o início de um ativo
Na maioria dos eventos de streaming ao vivo, os operadores executam alguns testes antes do evento real. Por exemplo, podem incluir uma ardósia como esta antes do início do evento: "O programa começará momentaneamente". Se o programa estiver a arquivar, os dados de teste e ardósia também são arquivados e incluídos na apresentação. No entanto, esta informação não deve ser mostrada aos clientes. Com o Dynamic Manifest, pode criar um filtro de hora de início e remover os dados indesejados do manifesto.

![Início de corte][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criação de subclips (vistas) a partir de um arquivo ao vivo
Muitos eventos ao vivo são longos e arquivo ao vivo pode incluir vários eventos. Após o fim do evento ao vivo, os emissores podem querer dividir o arquivo ao vivo em sequências de início e paragem lógicas do programa. Em seguida, publique estes programas virtuais separadamente sem processar o arquivo ao vivo e não criando ativos separados (que não beneficiam os fragmentos em cache existentes nos CDNs). Exemplos de tais programas virtuais são os quartos de um jogo de futebol ou basquetebol, innings no basebol, ou eventos individuais de qualquer programa desportivo.

Com o Dynamic Manifest, pode criar filtros utilizando tempos de início/fim e criar vistas virtuais sobre o topo do seu arquivo ao vivo. 

![Filtro de subclip][subclip_filter]

Ativo Filtrado:

![Esqui][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Janela de apresentação de ajuste (DVR)
Atualmente, a Azure Media Services oferece um arquivo circular onde a duração pode ser configurada entre 5 minutos e 25 horas. A filtragem manifesta pode ser usada para criar uma janela DVR rolante sobre a parte superior do arquivo, sem apagar os meios de comunicação. Há muitos cenários em que os emissores querem fornecer uma janela de DVR limitada para mover-se com a borda ao vivo e, ao mesmo tempo, manter uma janela de arquivo maior. Um organismo de radiodifusão televisiva pode querer utilizar os dados que estão fora da janela dVR para realçar clips, ou podem querer fornecer diferentes janelas de DVR para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não lida com grandes janelas de DVR (pode ter uma janela dVR de 2 minutos para dispositivos móveis e uma hora para clientes de desktop).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajuste do LiveBackoff (posição ao vivo)
A filtragem manifesta pode ser usada para remover alguns segundos da borda ao vivo de um programa ao vivo. A filtragem permite que os emissores assistam à apresentação no ponto de publicação de pré-visualização e criem pontos de inserção de publicidade antes que os espectadores recebam o fluxo (back-off por 30 segundos). Os organismos de radiodifusão podem então empurrar estes anúncios para os seus quadros de clientes a tempo de receberem e processarem a informação antes da oportunidade de publicidade.

Além do suporte publicitário, o cenário LiveBackoff pode ser usado para ajustar a posição dos espectadores para que quando os clientes deslizam e batam na borda ao vivo ainda possam obter fragmentos do servidor em vez de obter um erro HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinando várias regras num único filtro
Pode combinar várias regras de filtragem num único filtro. Como exemplo, pode definir uma "regra de alcance" para remover as ardósias de um arquivo ao vivo e também filtrar as bitrates disponíveis. Ao aplicar várias regras de filtragem, o resultado final é a intersecção de todas as regras.

![múltiplas regras][multiple-rules]

## <a name="create-filters-programmatically"></a>Criar filtros programáticamente
O seguinte artigo discute entidades dos Serviços de Media que estão relacionadas com filtros. O artigo também mostra como criar filtros programáticamente.  

[Crie filtros com APIs REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Combinando vários filtros (composição do filtro)
Também pode combinar vários filtros num único URL. 

O seguinte cenário demonstra por que razão poderá querer combinar filtros:

1. É necessário filtrar as qualidades de vídeo para dispositivos móveis como android ou iPAD (para limitar as qualidades de vídeo). Para remover as qualidades indesejadas, criaria um filtro global adequado para os perfis do dispositivo. Como mencionado anteriormente neste artigo, filtros globais podem ser usados para todos os seus ativos sob a mesma conta de serviços de mídia sem qualquer outra associação. 
2. Também quer cortar o tempo de início e fim de um ativo. Para tal, criaria um filtro local e definiria o tempo de início/fim. 
3. Pretende combinar ambos os filtros (sem combinação, é necessário adicionar filtragem de qualidade ao filtro de corte, o que dificulta o uso do filtro).

Para combinar filtros, é necessário definir os nomes dos filtros para o URL manifesto/lista de reprodução com semiponto delimitado. Vamos supor que tem um filtro chamado *MyMobileDevice* que filtra qualidades e que tem outro chamado *MyStartTime* para definir uma hora de início específica. Pode combiná-los assim:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Pode combinar até três filtros. 

Para mais informações, consulte [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Conheça questões e limitações
* O manifesto dinâmico funciona nos limites gop (Quadros-chave) pelo que o aparar tem precisão GOP. 
* Pode usar o mesmo nome de filtro para filtros locais e globais. Os filtros locais têm maior precedência e irão sobrepor-se aos filtros globais.
* Se atualizar um filtro, pode demorar até 2 minutos para o ponto final de streaming atualizar as regras. Se o conteúdo foi servido usando alguns filtros (e cached em proxies e caches CDN), a atualização destes filtros pode resultar em falhas dos jogadores. Recomenda-se limpar a cache depois de atualizar o filtro. Se esta opção não for possível, considere utilizar um filtro diferente.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Entrega de Conteúdo à visão geral dos clientes](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
