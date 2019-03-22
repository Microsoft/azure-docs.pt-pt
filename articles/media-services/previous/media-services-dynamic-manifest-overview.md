---
title: Filtros e dos manifestos dinâmicos | Documentos da Microsoft
description: Este tópico descreve como criar filtros para que o cliente possa utilizar secções específicas do fluxo de um fluxo. Serviços de multimédia cria os manifestos dinâmicos para arquivar este seletiva de transmissão em fluxo.
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
ms.author: cenkd;juliako
ms.openlocfilehash: 229f89a8803f089c24981f56e00e36efe96de3aa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316599"
---
# <a name="filters-and-dynamic-manifests"></a>Filtros e dos manifestos dinâmicos

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versão 2](media-services-dynamic-manifest-overview.md)
> * [Versão 3](../latest/filters-dynamic-manifest-overview.md)

A partir da versão 2.17, serviços de multimédia permite-lhe definir os filtros para os seus ativos. Estes filtros são regras de lado do servidor que irão permitir que os clientes podem optar por fazer coisas como: reproduzir apenas uma secção de um vídeo (em vez de reproduzir o vídeo inteiro) ou especificar apenas um subconjunto de representações de áudio e vídeos que o dispositivo do seu cliente pode manipular ( em vez de todas as representações que estão associados com o elemento). Esta filtragem de seus ativos é assegurado através da **dinâmica manifestar**s que são criados mediante solicitação do seu cliente para transmitir um vídeo com base nos filtros especificados.

Este tópico aborda cenários comuns nos quais usando filtros de representaria uma vantagem para os seus clientes e ligações para tópicos que demonstram como criar filtros por meio de programação.

## <a name="overview"></a>Descrição geral
Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido) o seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para alcançar este proceda de objetivo do seguinte:

* codifique a transmissão de velocidade de transmissão ([velocidade de transmissão adaptável](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (isso se encarregará de condições de qualidade e de rede) de fluxo de vídeo e 
* utilizar os Media Services [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) para dinamicamente novamente o pacote sua transmissão em fluxo para protocolos diferentes (isso se encarregará de transmissão em fluxo em diferentes dispositivos). Serviços de multimédia suportam a distribuição das seguinte velocidade de transmissão adaptável tecnologias de transmissão em fluxo: HTTP Live Streaming (HLS), Smooth Streaming e MPEG DASH. 

### <a name="manifest-files"></a>Ficheiros de manifesto
Quando codifica um recurso para transmissão em fluxo de velocidade de transmissão adaptável, um **manifesto** é criado (lista de reprodução) ficheiro (o arquivo é baseado em texto ou baseado em XML). O **manifesto** ficheiro inclui a transmissão em fluxo de metadados, tais como: controlar o tipo (áudio, vídeo ou texto), controlar o nome, hora de início e fim, a velocidade de transmissão (qualidades), linguagens de controle, a janela de apresentação (janela deslizante de duração fixa), (de codec de vídeo FourCC). Também instrui o player para recuperar o fragmento seguinte, fornecendo informações sobre os seguintes pode ser reproduzidos vídeo fragmentos disponíveis e a respetiva localização. Fragmentos de (ou segmentos) são os reais "segmentos" de um conteúdo de vídeo.

Eis um exemplo de um arquivo de manifesto: 

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
Existem [cenários](media-services-dynamic-manifest-overview.md#scenarios) quando o cliente precise de mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Por exemplo:

* Específico do dispositivo: entregar apenas as representações especificadas e/ou faixas de idioma especificado que são suportadas pelo dispositivo que é utilizado para reproduzir o conteúdo ("filtragem de representação"). 
* Reduza o manifesto para mostrar um subclips de um evento em direto ("subclips filtragem").
* Corte o início de um vídeo ("recortar um vídeo").
* Ajuste a janela de apresentação (DVR) a fim de fornecer uma duração limitada da janela DVR no player de ("Ajuste janela apresentação").

Para atingir esta flexibilidade, serviços de multimédia oferecem **manifestos dinâmica** com base no predefinidas [filtros](media-services-dynamic-manifest-overview.md#filters).  Depois de definir os filtros, os seus clientes poderiam usá-los para transmitir uma representação em específica ou de clips secundárias do seu vídeo. Eles deve especificar filtros no URL de transmissão em fluxo. Filtros podem ser aplicados a velocidade de transmissão adaptável, transmissão em fluxo protocolos suportados pelo [empacotamento dinâmico](media-services-dynamic-packaging-overview.md): HLS, MPEG-DASH e Smooth Streaming. Por exemplo:

MPEG DASH URL com um filtro

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)

URL de transmissão em fluxo uniforme com um filtro

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)


Para obter mais informações sobre como fornecer o seu conteúdo e a compilação URLs de transmissão em fluxo, consulte [descrição geral do conteúdo de entrega](media-services-deliver-content-overview.md).

> [!NOTE]
> Tenha em atenção que manifestos dinâmicos não altere o elemento e o manifesto de padrão para esse recurso. O cliente pode optar por solicitar um fluxo com ou sem filtros. 
> 
> 

### <a id="filters"></a>Filtros
Existem dois tipos de filtros ativos: 

* Filtros globais (podem ser aplicados a qualquer recurso na conta de Media Services do Azure, têm uma duração da conta) e 
* Filtros de locais (apenas pode ser aplicado a um recurso com a qual o filtro foi associado após a criação, têm uma duração de elemento). 

Tipos de filtro globais e locais têm exatamente as mesmas propriedades. A principal diferença entre os dois é para os cenários que tipo de um filtro é mais adequado. Filtros globais são geralmente adequados para perfis de dispositivo (filtragem de representação) onde os filtros de locais poderiam ser usados para cortar um recurso específico.

## <a id="scenarios"></a>Cenários comuns
Como foi mencionado antes, quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido) o seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Além disso, poderá ter outros requisitos que envolvem a filtragem de seus ativos e através de **dinâmica manifestar**s. As secções seguintes fornecem uma breve visão geral dos diferentes cenários de filtragem.

* Especifique apenas um subconjunto de representações de áudio e vídeos que certos dispositivos podem processar (em vez de todas as representações que estão associadas a com elemento). 
* Reprodução de apenas uma secção de um vídeo (em vez de reproduzir o vídeo todo).
* Ajuste a janela de apresentação de DVR.

## <a name="rendition-filtering"></a>Filtragem de representação
Pode optar por codificar o seu elemento para vários perfis de codificação (linha de base H.264, H.264 elevada, AACL, AACH, Dolby Digital mais) e múltiplas velocidades de transmissão de qualidade. No entanto, nem todos os dispositivos de cliente irão suportar perfis de todos os seus ativos e velocidades de transmissão. Por exemplo, os dispositivos Android mais antigos só suportam H.264 da linha de base + AACL. Enviar velocidades de transmissão superior a um dispositivo que não é possível obter os benefícios, desperdiça largura de banda e o dispositivo de computação. Tais dispositivos preciso decodificar todas as informações de determinado, apenas para a dimensionar para baixo para exibição.

Com um manifesto dinâmico, pode criar perfis de dispositivo, como dispositivos móveis, consola, HD/SD, etc. e incluem os roteiros e qualidades que pretende fazer parte de cada perfil.

![Exemplo de filtragem de representação][renditions2]

No exemplo a seguir, um codificador foi utilizado para codificar um recurso de mezanino em sete representações de vídeo de MP4s de ISO (a partir de 180p para 1080p). O elemento codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de transmissão em fluxo: HLS, Smooth e MPEG DASH.  Na parte superior do diagrama, é mostrado o manifesto HLS para o elemento com sem filtros (que contém todas as representações de sete).  Na parte inferior esquerda, é mostrado o manifesto HLS ao qual foi aplicado um filtro com o nome "ott". Especifica o filtro de "ott" para remover todas as velocidades de transmissão abaixo 1 Mbps, o que resultou nos níveis de qualidade dois na parte inferior a ser eliminados na resposta. Na parte inferior direita, é mostrado o manifesto HLS ao qual foi aplicado um filtro com o nome "móvel". O filtro "móvel" Especifica a remover representações em que a resolução é maior do que 720p, o que resultou em duas representações de 1080p a ser suprimidas.

![Filtragem de representação][renditions1]

## <a name="removing-language-tracks"></a>Remover faixas de idioma
Os recursos podem incluir vários idiomas de áudio, como inglês, espanhol, francês, etc. Normalmente, os gerentes de Player SDK predefinido a seleção de faixa de áudio e áudio disponível controla por seleção do usuário. É um desafio para desenvolver tais Player SDKs, ele exige implementações diferentes em estruturas de player específicos do dispositivo. Além disso, em algumas plataformas, APIs de leitor são limitadas e não incluem o recurso de seleção de áudio em que os utilizadores não é possível selecionar ou alterar a faixa de áudio padrão. Com os filtros de recurso, pode controlar o comportamento através da criação de filtros que incluem apenas os idiomas de áudio desejados.

![Filtragem de faixas de idioma][language_filter]

## <a name="trimming-start-of-an-asset"></a>Remoção de início de um ativo
Na maioria dos eventos de transmissão em fluxo em direto, os operadores de executam alguns testes antes do evento real. Por exemplo, eles podem incluir uma ficha, assim, antes do início do evento: "Programa vai começar momentaneamente". Se o programa é arquivar, o teste e os dados de imagem fixa são também arquivados e incluídos na apresentação. No entanto, estas informações não devem ser mostradas aos clientes. Com o manifesto dinâmico, pode criar um filtro de tempo de início e remove os dados indesejados de manifesto.

![Remoção de início][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criação de subclips (exibições) a partir de um arquivo live
Número de eventos em direto é de longa e arquivo live pode incluir vários eventos. Após o evento ao vivo terminar, emissoras talvez queira dividir este arquivo live no início do programa de lógica e parar sequências. Em seguida, publica esses programas virtual separadamente sem postagem processar este arquivo live e não crie recursos separados (o que não é o benefício dos fragmentos em cache existentes nas CDNs). Exemplos de tais programas virtual são os trimestres de um football ou jogo basquete, innings no beisebol ou eventos individuais de qualquer programa do desporto.

Com o manifesto dinâmico, pode criar filtros com horas de início/fim e criar vistas virtual ao longo da parte superior do seu arquivo live. 

![Filtro do subclip][subclip_filter]

Filtrado Asset:

![Esqui][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Ajustar a janela de apresentação (DVR)
Atualmente, os serviços de multimédia do Azure oferece arquivo circular, onde a duração pode ser configurada entre 5 minutos - 25 horas. Filtragem de manifesto pode ser utilizado para criar uma janela DVR sem interrupção ao longo da parte superior do arquivo, sem eliminar o suporte de dados. Existem muitos cenários em que desejam fornecer uma janela DVR limitada a mover-se com o edge em direto e ao mesmo tempo, manter uma janela de arquivo maior emissoras. A emissora poderá querer utilizar os dados que está fora da janela de DVR para realçar clips ou he\she talvez queira fornecer windows DVR diferentes para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não lidar com grandes windows DVR (pode ter uma janela DVR de 2 minutos, para dispositivos móveis e uma hora para clientes de ambiente de trabalho).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar LiveBackoff (posição em direto)
Filtragem de manifesto pode ser utilizado para remover vários segundos do edge em direto de um programa em direto. Filtragem permite emissoras Assista à apresentação no ponto de publicação de pré-visualização e criar pontos de inserção de anúncio antes dos visualizadores de recebem o fluxo (de segurança-off por 30 segundos). Emissoras, em seguida, podem enviar estes anúncios para suas estruturas de cliente no tempo para serem recebidos e processar as informações antes da oportunidade de anúncio.

Além do suporte de anúncio, a definição de LiveBackoff pode ser utilizada para ajustar a posição de visualizadores para que quando os clientes de inconsistências e atingir o limite ao vivo ainda podem obter fragmentos do servidor em vez de obter um erro HTTP 404 ou erro 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>A combinação de várias regras num único filtro
Pode combinar várias regras de filtragem num único filtro. Por exemplo, pode definir uma "regra de intervalo" para remover slates de um arquivo em direto e filtrar também disponíveis velocidades de transmissão. Ao aplicar várias regras de filtragem, o resultado final é a interseção de todas as regras.

![várias regras][multiple-rules]

## <a name="create-filters-programmatically"></a>Criar filtros de forma programática
O seguinte artigo aborda as entidades de serviços de multimédia relacionados com filtros. O artigo também mostra como criar programaticamente os filtros.  

[Criar filtros com as APIs REST](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>A combinação de vários filtros (composição de filtro)
Também pode combinar vários filtros numa única URL. 

O cenário a seguir demonstra por que pode querer combinar os filtros:

1. Tem de filtrar as qualidades de vídeo para dispositivos móveis, como Android ou iPAD (para limitar as qualidades de vídeo). Para remover as qualidades indesejadas, deverá criar um filtro global adequada para os perfis de dispositivo. Como mencionado anteriormente neste artigo, filtros global podem ser utilizados para todos os seus ativos na mesma conta de serviços de multimédia sem qualquer outra associação. 
2. Também queira limitar a hora de início e fim de um ativo. Para conseguir isso, teria de criar um filtro de local e definir a hora de início/fim. 
3. Pode querer combinar ambos estes filtros (sem combinação, precisa adicionar filtragem de qualidade para o filtro de remoção que torna o uso de filtro mais difícil).

Para combinar os filtros, tem de definir os nomes de filtro para a manifesto/lista de reprodução URL com ponto e vírgula delimitada. Vamos supor que tem um filtro com o nome *MyMobileDevice* que filtra qualidades e tiver outro denominado *MyStartTime* definir específicos de um hora de início. Pode combiná-los como este:

    http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)

Pode combinar os filtros de até três. 

Para obter mais informações, consulte [isso](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="know-issues-and-limitations"></a>Saber as limitações e problemas
* Manifesto dinâmico funciona em GOP limites (quadros chave), por conseguinte, corte tem precisão GOP. 
* Pode usar o mesmo nome de filtro para os filtros de locais e globais. Filtros locais tem precedência e irão substituir filtros globais.
* Se atualizar um filtro, pode demorar até 2 minutos para o ponto final de transmissão em fluxo atualizar as regras. Se o conteúdo tiver sido servido com alguns filtros (e armazenado em cache em proxies e CDN caches), a atualizar estes filtros pode resultar em falhas de player. Recomenda-se para limpar a cache depois de atualizar o filtro. Se esta opção não for possível, considere utilizar um filtro de diferente.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Entrega de conteúdos para os clientes descrição-geral](media-services-deliver-content-overview.md)

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
