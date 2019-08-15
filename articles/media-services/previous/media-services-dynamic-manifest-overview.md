---
title: Filtros e manifestos dinâmicos | Microsoft Docs
description: Este tópico descreve como criar filtros para que o cliente possa utilizar secções específicas do fluxo de um fluxo. Os serviços de mídia criam manifestos dinâmicos para arquivar esse streaming seletivo.
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
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "69015855"
---
# <a name="filters-and-dynamic-manifests"></a>Filtros e manifestos dinâmicos

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 2](media-services-dynamic-manifest-overview.md)
> * [Versão 3](../latest/filters-dynamic-manifest-overview.md)

A partir da versão 2,17, os serviços de mídia permitem que você defina filtros para seus ativos. Esses filtros são regras do lado do servidor que permitirão que os clientes escolham tarefas como: reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro) ou especificar apenas um subconjunto de representações de áudio e vídeo que o dispositivo do seu cliente pode manipular ( em vez de todas as representações associadas ao ativo). Essa filtragem de seus ativos é obtida pormeio de manifestos dinâmicos que são criados na solicitação do cliente para transmitir um vídeo com base nos filtros especificados.

Este tópico discute cenários comuns nos quais o uso de filtros seria benéfico para seus clientes e links para tópicos que demonstram como criar filtros programaticamente.

## <a name="overview"></a>Descrição geral
Ao entregar seu conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo sob demanda), seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos sob diferentes condições de rede. Para atingir esse objetivo, faça o seguinte:

* Codifique seu fluxo para o fluxo de vídeo de múltiplas taxas de bits ([taxa de bits adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (Isso cuidará da qualidade e das condições de rede) e 
* Use o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos serviços de mídia para reempacotar dinamicamente seu fluxo em protocolos diferentes (Isso cuidará do streaming em diferentes dispositivos). Os serviços de mídia oferecem suporte à entrega das seguintes tecnologias de streaming de taxa de bits adaptável: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH. 

### <a name="manifest-files"></a>Arquivos de manifesto
Quando você codifica um ativo para streaming de taxa de bits adaptável, um arquivo de **manifesto** (playlist) é criado (o arquivo é baseado em texto ou XML). O arquivo de **manifesto** inclui metadados de streaming como: rastrear tipo (áudio, vídeo ou texto), nome da faixa, hora de início e de término, taxa de bits (qualidades), rastrear idiomas, janela de apresentação (janela deslizante de duração fixa), codec de vídeo (FourCC). Ele também instrui o Player a recuperar o próximo fragmento, fornecendo informações sobre os próximos fragmentos de vídeo reproduzidos disponíveis e sua localização. Fragmentos (ou segmentos) são as "partes" reais de um conteúdo de vídeo.

Aqui está um exemplo de um arquivo de manifesto: 

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
Há [cenários](media-services-dynamic-manifest-overview.md#scenarios) em que o cliente precisa de mais flexibilidade do que o descrito no arquivo de manifesto do ativo padrão. Por exemplo:

* Específico do dispositivo: forneça apenas as representações especificadas e/ou faixas de idioma especificadas com suporte pelo dispositivo que é usado para reproduzir o conteúdo ("filtragem de representação"). 
* Reduza o manifesto para mostrar um subclipe de um evento ao vivo ("filtragem de subclipe").
* Cortar o início de um vídeo ("corte de um vídeo").
* Ajustar a janela de apresentação (DVR) para fornecer um comprimento limitado da janela DVR no Player ("ajustando a janela de apresentação").

Para atingir essa flexibilidade, os serviços de mídia oferecem manifestos dinâmicos com base em [filtros](media-services-dynamic-manifest-overview.md#filters)predefinidos.  Depois de definir os filtros, os clientes podem usá-los para transmitir uma rendição ou subclipes específicos do seu vídeo. Eles especificariam filtro (s) na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável com suporte do [empacotamento dinâmico](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH e Smooth Streaming. Por exemplo:

URL de MPEG DASH com filtro

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de Smooth Streaming com filtro

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Para obter mais informações sobre como entregar seu conteúdo e criar URLs de streaming, consulte [visão geral do fornecimento de conteúdo](media-services-deliver-content-overview.md).

> [!NOTE]
> Observe que os manifestos dinâmicos não alteram o ativo e o manifesto padrão para esse ativo. O cliente pode optar por solicitar um fluxo com ou sem filtros. 
> 
> 

### <a id="filters"></a>Filter
Há dois tipos de filtros de ativos: 

* Filtros globais (podem ser aplicados a qualquer ativo na conta dos serviços de mídia do Azure, ter um tempo de vida da conta) e 
* Filtros locais (só podem ser aplicados a um ativo com o qual o filtro foi associado após a criação, ter um tempo de vida do ativo). 

Os tipos de filtro global e local têm exatamente as mesmas propriedades. A principal diferença entre os dois é para quais cenários o tipo de um Filer é mais adequado. Os filtros globais são geralmente adequados para perfis de dispositivo (filtragem de representação), em que os filtros locais podem ser usados para cortar um ativo específico.

## <a id="scenarios"></a>Cenários comuns
Como foi mencionado anteriormente, ao entregar seu conteúdo aos clientes (eventos dinâmicos de streaming ou vídeo sob demanda), seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos sob diferentes condições de rede. Além disso, você pode ter outros requisitos que envolvem a filtragem de seus ativos e o uso de s de **manifesto dinâmico**. As seções a seguir fornecem uma breve visão geral de diferentes cenários de filtragem.

* Especifique apenas um subconjunto de representações de áudio e vídeo que determinados dispositivos podem manipular (em vez de todas as representações associadas ao ativo). 
* Reproduzir apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro).
* Ajustar a janela de apresentação do DVR.

## <a name="rendition-filtering"></a>Filtragem de representação
Você pode optar por codificar seu ativo para vários perfis de codificação (H. 264 linha de base, H. 264 High, AACL, AACH, Dolby Digital Plus) e várias taxas de bits de qualidade. No entanto, nem todos os dispositivos cliente terão suporte para todos os perfis e taxas de bits do ativo. Por exemplo, dispositivos Android mais antigos dão suporte apenas a H. 264 Baseline + AACL. Enviando taxas de bits mais altas para um dispositivo que não pode obter os benefícios, desperdiça a largura de banda e a computação do dispositivo. Esse dispositivo deve decodificar todas as informações fornecidas, apenas para diminuí-lo para exibição.

Com o manifesto dinâmico, você pode criar perfis de dispositivo, como móvel, console, HD/SD, etc., e incluir as trilhas e qualidades que você deseja que sejam parte de cada perfil.

![Exemplo de filtragem de representação][renditions2]

No exemplo a seguir, um codificador foi usado para codificar um ativo de mezanino em sete representações de vídeo ISO MP4s (de 180p para 1080p). O ativo codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de streaming: HLS, Smooth e MPEG DASH.  Na parte superior do diagrama, o manifesto HLS para o ativo sem filtros é mostrado (ele contém todas as sete representações).  Na parte inferior esquerda, o manifesto HLS ao qual um filtro chamado "Ott" foi aplicado é mostrado. O filtro "Ott" especifica a remoção de todas as taxas de bits abaixo de 1 Mbps, o que resultou na remoção dos dois níveis de qualidade inferiores na resposta. No canto inferior direito, o manifesto HLS ao qual um filtro chamado "Mobile" foi aplicado é mostrado. O filtro "móvel" especifica a remoção de representações em que a resolução é maior que 720p, o que resultou nas duas rendições 1080p sendo eliminadas.

![Filtragem de representação][renditions1]

## <a name="removing-language-tracks"></a>Removendo faixas de idioma
Seus ativos podem incluir vários idiomas de áudio, como Inglês, espanhol, francês, etc. Normalmente, os gerenciadores do SDK do Player são seleção de faixa de áudio padrão e faixas de áudio disponíveis por seleção de usuário. É desafiador desenvolver esses SDKs de Player, que requer implementações diferentes em estruturas de Player específicas de dispositivo. Além disso, em algumas plataformas, as APIs do Player são limitadas e não incluem o recurso de seleção de áudio, em que os usuários não podem selecionar ou alterar a faixa de áudio padrão. Com os filtros de ativos, você pode controlar o comportamento Criando filtros que incluem apenas os idiomas de áudio desejados.

![Filtragem de faixas de idiomas][language_filter]

## <a name="trimming-start-of-an-asset"></a>Corte do início de um ativo
Na maioria dos eventos de transmissão ao vivo, os operadores executam alguns testes antes do evento real. Por exemplo, eles podem incluir um Slate como este antes do início do evento: "O programa será iniciado momentaneamente". Se o programa estiver sendo arquivado, os dados de teste e de Slate também serão arquivados e incluídos na apresentação. No entanto, essas informações não devem ser mostradas para os clientes. Com o manifesto dinâmico, você pode criar um filtro de hora de início e remover os dados indesejados do manifesto.

![Início da corte][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criando subclipes (exibições) de um arquivo ao vivo
Muitos eventos ao vivo são de longa execução e o arquivamento dinâmico pode incluir vários eventos. Após o término do evento ao vivo, os difusores podem querer dividir o arquivo ao vivo em sequências de início e parada do programa lógico. Em seguida, publique esses programas virtuais separadamente sem o pós-processamento do arquivo em tempo real e não criando ativos separados (o que não obtém o benefício dos fragmentos armazenados em cache existentes no CDNs). Exemplos desses programas virtuais são os trimestres de um jogo de futebol ou basquete, entradas em beisebol ou eventos individuais de qualquer programa esportivo.

Com o manifesto dinâmico, você pode criar filtros usando horários de início/término e criar exibições virtuais na parte superior do seu arquivo em tempo real. 

![Filtro de subclipe][subclip_filter]

Ativo filtrado:

![Esqui][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Ajustando a janela de apresentação (DVR)
Atualmente, os serviços de mídia do Azure oferecem um arquivo circular em que a duração pode ser configurada entre 5 minutos e 25 horas. A filtragem de manifesto pode ser usada para criar uma janela DVR de interrupção na parte superior do arquivo morto, sem excluir a mídia. Há muitos cenários em que os difusores desejam fornecer uma janela DVR limitada para mover com o Live Edge e, ao mesmo tempo, manter uma janela de arquivamento maior. Um transmissor pode querer usar os dados que estão fora da janela DVR para realçar clipes ou pode querer fornecer janelas DVR diferentes para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não lida com janelas DVR grandes (você pode ter uma janela DVR de 2 minutos para dispositivos móveis e uma hora para clientes de desktop).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustando LiveBackoff (posição dinâmica)
A filtragem de manifesto pode ser usada para remover vários segundos da borda ao vivo de um programa em tempo real. A filtragem permite que os difusores assistam à apresentação no ponto de publicação de visualização e criem pontos de inserção de anúncio antes que os visualizadores recebam o fluxo (apoiado por 30 segundos). Os difusores podem enviar esses anúncios para suas estruturas de cliente no tempo para que eles recebam e processem as informações antes da oportunidade de anúncio.

Além do suporte a anúncios, a configuração LiveBackoff pode ser usada para ajustar a posição dos visualizadores para que, quando os clientes se descompassom e atingirem a borda ao vivo, ainda possam obter fragmentos do servidor em vez de obter um erro HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinando várias regras em um único filtro
Você pode combinar várias regras de filtragem em um único filtro. Como exemplo, você pode definir uma "regra de intervalo" para remover os slates de um arquivo em tempo real e também filtrar as taxas de bits disponíveis. Ao aplicar várias regras de filtragem, o resultado final é a interseção de todas as regras.

![várias regras][multiple-rules]

## <a name="create-filters-programmatically"></a>Criar filtros programaticamente
O artigo a seguir discute as entidades dos serviços de mídia relacionadas aos filtros. O artigo também mostra como criar filtros programaticamente.  

[Crie filtros com APIs REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Combinando vários filtros (composição de filtro)
Você também pode combinar vários filtros em uma única URL. 

O cenário a seguir demonstra por que você pode querer combinar filtros:

1. Você precisa filtrar suas qualidades de vídeo para dispositivos móveis, como Android ou iPAD (para limitar as qualidades de vídeo). Para remover as qualidades indesejadas, você criaria um filtro global adequado para os perfis de dispositivo. Conforme mencionado anteriormente neste artigo, os filtros globais podem ser usados para todos os seus ativos na mesma conta dos serviços de mídia, sem nenhuma associação adicional. 
2. Você também deseja cortar a hora de início e de término de um ativo. Para conseguir isso, você criaria um filtro local e definiria a hora de início/término. 
3. Você deseja combinar esses dois filtros (sem combinação, você precisa adicionar a filtragem de qualidade ao filtro de corte, o que torna o uso do filtro mais difícil).

Para combinar filtros, você precisa definir os nomes de filtro para a URL de manifesto/playlist com delimitado por ponto e vírgula. Vamos supor que você tenha um filtro chamado *MyMobileDevice* que filtre as qualidades e que você tenha outro chamado mystartime para definir uma hora de início específica. Você pode combiná-los da seguinte maneira:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Você pode combinar até três filtros. 

Para obter mais informações, consulte [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Conheça os problemas e as limitações
* O manifesto dinâmico opera em limites de GOP (quadros-chave), portanto, o corte tem precisão de GOP. 
* Você pode usar o mesmo nome de filtro para filtros locais e globais. Os filtros locais têm maior precedência e substituirão os filtros globais.
* Se você atualizar um filtro, pode levar até 2 minutos para que o ponto de extremidade de streaming atualize as regras. Se o conteúdo foi servido usando alguns filtros (e armazenados em cache em proxies e caches CDN), a atualização desses filtros pode resultar em falhas do Player. É recomendável limpar o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Visão geral do fornecimento de conteúdo aos clientes](media-services-deliver-content-overview.md)

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
