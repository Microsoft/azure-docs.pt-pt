---
title: Manifestos dinâmicos, filtros e dos serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico descreve como criar filtros para que o cliente possa utilizar secções específicas do fluxo de um fluxo. Serviços de multimédia cria os manifestos dinâmicos para alcançar este seletiva de transmissão em fluxo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: 7d40a29a0d9d708deb5485b18327d809ded0022d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959549"
---
# <a name="dynamic-manifests"></a>Manifestos dinâmicos

Serviços de multimédia oferecem **manifestos dinâmica** com base nos filtros predefinidos. Depois de definir filtros (consulte [definir filtros](filters-concept.md)), os clientes poderá usá-las para transmitir uma representação em específica ou de clips secundárias do seu vídeo. Eles deve especificar filtros no URL de transmissão em fluxo. Filtros podem ser aplicados a velocidade de transmissão adaptável, protocolos de transmissão em fluxo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming. 

A tabela seguinte mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|


> [!NOTE]
> Os manifestos dinâmicos não altere o elemento e o manifesto de padrão para esse recurso. O cliente pode optar por solicitar um fluxo com ou sem filtros. 
> 

Este tópico explica os conceitos relacionados com **manifestos dinâmica** e fornece exemplos de cenários em que pode querer utilizar esta funcionalidade.

## <a name="manifest-files-overview"></a>Descrição geral de ficheiros de manifesto

R **manifesto** (lista de reprodução) ficheiro (baseado em texto ou baseado em XML) inclui a transmissão em fluxo de metadados, tais como: controlar o tipo (áudio, vídeo ou texto), controlar o nome, hora de início e fim, a velocidade de transmissão (qualidades), linguagens de controle, janela de apresentação (deslizante codec de vídeo de janela de duração fixa), (FourCC). Também instrui o player para recuperar o fragmento seguinte, fornecendo informações sobre os seguintes pode ser reproduzidos vídeo fragmentos disponíveis e a respetiva localização. Fragmentos de (ou segmentos) são os reais "segmentos" de um conteúdo de vídeo.

Eis um exemplo de um ficheiro de manifesto HLS: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>Obter e examinar os arquivos de manifesto

Especifique uma lista de condições de propriedade de controle de filtro com base no qual as faixas de sua transmissão em fluxo (Live ou vídeo a pedido) devem ser incluídas no manifesto criado dinamicamente. Para obter e examinar as propriedades dos roteiros, terá de carregar o manifesto de transmissão em fluxo uniforme primeiro.

O [carregar, codificar e transmitir ficheiros com .NET](stream-files-tutorial-with-api.md) tutorial mostra-lhe como criar os URLs de transmissão em fluxo com o .NET (ver, o [criação URLs](stream-files-tutorial-with-api.md#get-streaming-urls) secção). Se executar a aplicação, um dos URLs de aponta para o manifesto de transmissão em fluxo uniforme: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Copie e cole o URL na barra de endereço do browser. O ficheiro será transferido. Pode abri-lo num editor de texto à sua escolha.

No exemplo do REST, veja [carregar, codificar e transmitir ficheiros com REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorizar a velocidade de transmissão de um fluxo de vídeo

Pode utilizar o [página de demonstração do leitor de multimédia do Azure](http://aka.ms/amp) para monitorizar a velocidade de transmissão de um fluxo de vídeo. A página de demonstração exibe as informações de diagnóstico do **diagnóstico** separador:

![Diagnóstico do leitor de multimédia do Azure][amp_diagnostics]

## <a name="rendition-filtering"></a>Filtragem de representação

Pode optar por codificar o seu elemento para vários perfis de codificação (linha de base H.264, H.264 elevada, AACL, AACH, Dolby Digital mais) e múltiplas velocidades de transmissão de qualidade. No entanto, nem todos os dispositivos de cliente irão suportar perfis de todos os seus ativos e velocidades de transmissão. Por exemplo, os dispositivos Android mais antigos só suportam H.264 da linha de base + AACL. Enviando velocidades de transmissão superior a um dispositivo, que não é possível obter os benefícios, desperdiça largura de banda e a computação de dispositivo. Tais dispositivos preciso decodificar todas as informações de determinado, apenas para a dimensionar para baixo para exibição.

Com um manifesto dinâmico, pode criar perfis de dispositivo, como dispositivos móveis, consola, HD/SD, etc. e incluem os roteiros e qualidades, o que deseja fazer parte de cada perfil.

![Exemplo de filtragem de representação][renditions2]

No exemplo a seguir, um codificador foi utilizado para codificar um recurso de mezanino em sete representações de vídeo de MP4s de ISO (a partir de 180p para 1080p). O elemento codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de transmissão em fluxo: HLS, MPEG DASH e Smooth.  Na parte superior do diagrama, é mostrado o manifesto HLS para o elemento com sem filtros (que contém todas as representações de sete).  Na parte inferior esquerda, é mostrado o manifesto HLS ao qual foi aplicado um filtro com o nome "ott". Especifica o filtro de "ott" para remover todas as velocidades de transmissão abaixo 1 Mbps, o que resultou nos níveis de qualidade dois na parte inferior a ser eliminados na resposta. Na parte inferior direita, é mostrado o manifesto HLS ao qual foi aplicado um filtro com o nome "móvel". O filtro "móvel" Especifica a remover representações em que a resolução é maior do que 720p, o que resultou em duas representações de 1080p a ser suprimidas.

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
Atualmente, os serviços de multimédia do Azure oferece arquivo circular, onde a duração pode ser configurada entre 5 minutos - 25 horas. Filtragem de manifesto pode ser utilizado para criar uma janela DVR sem interrupção ao longo da parte superior do arquivo, sem eliminar o suporte de dados. Existem muitos cenários em que desejam fornecer uma janela DVR limitada a mover-se com o edge em direto e ao mesmo tempo, manter uma janela de arquivo maior emissoras. A emissora poderá querer utilizar os dados que está fora da janela de DVR para realçar clips ou podem optar por fornecer windows DVR diferentes para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não lidar com grandes windows DVR (pode ter uma janela DVR de 2 minutos, para dispositivos móveis e uma hora para clientes de ambiente de trabalho).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar LiveBackoff (posição em direto)
Filtragem de manifesto pode ser utilizado para remover vários segundos do edge em direto de um programa em direto. Filtragem permite emissoras Assista à apresentação no ponto de publicação de pré-visualização e criar pontos de inserção de anúncio antes dos visualizadores de recebem o fluxo (de segurança-off por 30 segundos). Emissoras, em seguida, podem enviar estes anúncios para suas estruturas de cliente no tempo para serem recebidos e processar as informações antes da oportunidade de anúncio.

Além do suporte de anúncio, a definição de LiveBackoff pode ser utilizada para ajustar a posição de visualizadores para que quando os clientes de inconsistências e atingir o limite ao vivo ainda podem obter fragmentos do servidor em vez de obter um erro HTTP 404 ou erro 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>A combinação de várias regras num único filtro
Pode combinar várias regras de filtragem num único filtro. Por exemplo, pode definir uma "regra de intervalo" para remover slates de um arquivo em direto e filtrar também disponíveis velocidades de transmissão. Ao aplicar várias regras de filtragem, o resultado final é a interseção de todas as regras.

![várias regras][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>A combinação de vários filtros (composição de filtro)

Também pode combinar vários filtros numa única URL. 

O cenário a seguir demonstra por que pode querer combinar os filtros:

1. Tem de filtrar as qualidades de vídeo para dispositivos móveis, como Android ou iPAD (para limitar as qualidades de vídeo). Para remover as qualidades indesejadas, deverá criar um filtro da conta adequada para os perfis de dispositivo. Filtros de conta podem ser utilizados para todos os seus ativos na mesma conta de serviços de multimédia sem qualquer outra associação. 
2. Também queira limitar a hora de início e fim de um ativo. Para conseguir isso, teria de criar um filtro de elemento e definir a hora de início/fim. 
3. Pode querer combinar ambos estes filtros (sem combinação, precisa adicionar filtragem de qualidade para o filtro de remoção, o que dificulta a utilização de filtro).

Para combinar os filtros, tem de definir os nomes de filtro para a manifesto/lista de reprodução URL com ponto e vírgula delimitada. Vamos supor que tem um filtro com o nome *MyMobileDevice* que filtra qualidades e tiver outro denominado *MyStartTime* definir específicos de um hora de início. Pode combiná-los como este:

Pode combinar os filtros de até três. 

Para obter mais informações, consulte [isso](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="considerations-and-limitations"></a>Considerações e limitações

- Os valores para **forceEndTimestamp**, **presentationWindowDuration**, e **liveBackoffDuration** não deve ser definida para um filtro de VoD. Só são utilizadas para cenários de filtro em direto. 
- Manifesto dinâmico funciona em GOP limites (quadros chave), por conseguinte, corte tem precisão GOP. 
- Pode utilizar o mesmo nome de filtro para os filtros de conta e recurso. Filtros de elemento tem precedência e irão substituir os filtros de conta.
- Se atualizar um filtro, pode demorar até 2 minutos para o ponto final de transmissão em fluxo atualizar as regras. Se o conteúdo tiver sido servido com alguns filtros (e armazenado em cache em proxies e CDN caches), a atualizar estes filtros pode resultar em falhas de player. Recomenda-se para limpar a cache depois de atualizar o filtro. Se esta opção não for possível, considere utilizar um filtro de diferente.
- Os clientes têm de manualmente, transfira o manifesto e analisar o startTimestamp exato e a escala de tempo.
    
    - Para determinar as propriedades dos roteiros num recurso [obter e examinar o arquivo de manifesto](#get-and-examine-manifest-files).
    - A fórmula para definir o filtro de elemento timestamp propriedades: <br/>startTimestamp = &lt;hora de início no manifesto&gt; +  &lt;esperado hora de início de filtro em segundos&gt;* escala temporal


## <a name="next-steps"></a>Passos Seguintes

Os artigos seguintes mostram como criar filtros de forma programática.  

- [Criar filtros com as APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
