---
title: Filtrar os seus manifestos usando o Dynamic Packager
description: Aprenda a criar filtros utilizando o Dynamic Packager para filtrar e transmitir seletivamente os seus manifestos.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3ffdb41752630e0e5e22303ff58ecd798595a890
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897667"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrar os seus manifestos usando o Dynamic Packager

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Quando está a fornecer conteúdo de streaming de bitrate adaptativo aos dispositivos, por vezes é necessário publicar várias versões de um manifesto para direcionar capacidades específicas do dispositivo ou largura de banda de rede disponível. O [Dynamic Packager](dynamic-packaging-overview.md) permite especificar filtros que podem filtrar códigos específicos, resoluções, bitrates e combinações de faixas de áudio no voo. Esta filtragem elimina a necessidade de criar várias cópias. Basta publicar um novo URL com um conjunto específico de filtros configurados para os seus dispositivos-alvo (iOS, Android, SmartTV ou navegadores) e as capacidades de rede (cenários de largura de banda alta, móveis ou de baixa largura de banda). Neste caso, os clientes podem manipular o streaming do seu conteúdo através da cadeia de consulta (especificando filtros de ativos disponíveis [ou filtros de conta)](filters-concept.md)e usar filtros para transmitir secções específicas de um fluxo.

Alguns cenários de entrega requerem que você certifique-se de que um cliente não pode aceder a faixas específicas. Por exemplo, talvez não queira publicar um manifesto que contenha faixas HD para um nível de subscrição específico. Ou, talvez queira remover pistas específicas de bitrate adaptativo (ABR) para reduzir o custo de entrega a um dispositivo específico que não beneficiaria das faixas adicionais. Neste caso, pode associar uma lista de filtros pré-criados com o seu [Localizador de Streaming](streaming-locators-concept.md) na criação. Os clientes não podem então manipular a forma como o conteúdo é transmitido porque é definido pelo **Localizador de Streaming.**

Pode combinar filtragem através da especificação de [filtros no Localizador de Streaming](filters-concept.md#associating-filters-with-streaming-locator) + filtros adicionais específicos do dispositivo que o seu cliente especifica no URL. Esta combinação é útil para restringir faixas adicionais como metadados ou streams de eventos, linguagens áudio ou faixas de áudio descritivas.

Esta capacidade de especificar filtros diferentes no seu fluxo fornece uma poderosa solução de manipulação **Do Manifesto Dinâmico** para direcionar múltiplos cenários de caso de utilização para os seus dispositivos-alvo. Este tópico explica conceitos relacionados com **Manifestos Dinâmicos** e dá exemplos de cenários em que pode utilizar esta funcionalidade.

> [!NOTE]
> Os Manifestos Dinâmicos não alteram o ativo e o manifesto padrão para esse ativo.

## <a name="overview-of-manifests"></a>Visão geral dos manifestos

A Azure Media Services suporta protocolos HLS, MPEG DASH e Smooth Streaming. Como parte da [Dynamic Packaging,](dynamic-packaging-overview.md)os manifestos do cliente de streaming (HLS Master Playlist, DASH Media Presentation Description [MPD], e Smooth Streaming) são gerados dinamicamente com base no seletor de formato no URL. Para obter mais informações, consulte os protocolos de entrega em [fluxo de trabalho comum a pedido.](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)

### <a name="get-and-examine-manifest-files"></a>Obter e examinar ficheiros manifestos

Você especifica uma lista de condições de propriedade de pista de filtro com base em quais faixas do seu stream (live ou video on demand [VOD]) devem ser incluídas num manifesto criado dinamicamente. Para obter e examinar as propriedades das faixas, você tem que carregar o manifesto de Streaming Suave primeiro.

Os [ficheiros upload, codificação e streaming com](stream-files-tutorial-with-api.md#get-streaming-urls) tutorial .NET mostram-lhe como construir os URLs de streaming com .NET. Se executar a aplicação, um dos URLs aponta para o manifesto de Streaming Suave: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest` .<br/> Copie e cole o URL na barra de endereço de um browser. O ficheiro será descarregado. Pode abri-la em qualquer editor de texto.

Para um exemplo DE REST, consulte [upload, codificar e transmitir ficheiros com REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorize o bitrate de um fluxo de vídeo

Pode utilizar a [página de demonstração do Azure Media Player](https://aka.ms/azuremediaplayer) para monitorizar o bitrate de um stream de vídeo. A página de demonstração exibe informações de diagnóstico no separador **Diagnósticos:**

![Diagnósticos do Azure Media Player][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Exemplos: URLs com filtros em cadeia de consulta

Pode aplicar filtros nos protocolos de streaming ABR: HLS, MPEG-DASH e Smooth Streaming. A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtragem de rendição

Pode optar por codificar o seu ativo para vários perfis de codificação (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) e vários bitrates de qualidade. No entanto, nem todos os dispositivos do cliente suportam todos os perfis e bitrates do seu ativo. Por exemplo, os dispositivos Android mais antigos suportam apenas H.264 Baseline+AACL. Enviar bitrates mais altos para um dispositivo que não consegue obter os benefícios desperdiça largura de banda e computação de dispositivos. Tal dispositivo deve descodificar todas as informações dadas, apenas para a reduzir para visualização.

Com o Dynamic Manifest, pode criar perfis de dispositivos (como mobile, consola ou HD/SD) e incluir as faixas e qualidades que pretende fazer parte de cada perfil. Chama-se filtragem de rendição. O diagrama que se segue mostra um exemplo disso.

![Exemplo de filtragem de rendição com Manifesto Dinâmico][renditions2]

No exemplo seguinte, um codificante foi usado para codificar um ativo mezanino em sete representações de vídeo ISO MP4s (de 180p a 1080p). O ativo codificado pode ser [embalado dinamicamente](dynamic-packaging-overview.md) em qualquer um dos seguintes protocolos de streaming: HLS, MPEG DASH e Smooth.

A parte superior do diagrama seguinte mostra o manifesto HLS para o ativo sem filtros. (Contém todas as sete representações.)  Na parte inferior esquerda, o diagrama mostra um manifesto HLS ao qual foi aplicado um filtro chamado "ott". O filtro "ott" especifica a remoção de todos os bitrates abaixo de 1 Mbps, pelo que os dois níveis de qualidade inferiores foram retirados na resposta. No inferior direito, o diagrama mostra o manifesto HLS ao qual foi aplicado um filtro chamado "mobile". O filtro "móvel" especifica a remoção de rendições onde a resolução é superior a 720p, pelo que as duas representações de 1080p foram retiradas.

![Filtragem de rendição com Manifesto Dinâmico][renditions1]

## <a name="removing-language-tracks"></a>Remoção de faixas linguísticas
Os seus ativos podem incluir várias línguas áudio, como inglês, espanhol, francês, e assim por diante. Normalmente, o Player SDK gere a seleção padrão da faixa de áudio e faixas de áudio disponíveis por seleção do utilizador.

O desenvolvimento destes SDKs do Player é um desafio porque requer diferentes implementações em diferentes quadros de jogadores específicos do dispositivo. Além disso, em algumas plataformas, as APIs do Jogador são limitadas e não incluem a funcionalidade de seleção de áudio onde os utilizadores não podem selecionar ou alterar a faixa de áudio padrão. Com filtros de ativos, pode controlar o comportamento criando filtros que incluem apenas as línguas de áudio desejadas.

![Filtragem de faixas linguísticas com Manifesto Dinâmico][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Aparar o início de um ativo

Na maioria dos eventos de streaming ao vivo, os operadores executam alguns testes antes do evento real. Por exemplo, podem incluir uma ardósia como esta antes do início do evento: "O programa começará momentaneamente."

Se o programa estiver a arquivar, os dados de teste e ardósia também são arquivados e incluídos na apresentação. No entanto, esta informação não deve ser mostrada aos clientes. Com o Dynamic Manifest, pode criar um filtro de tempo de início e remover os dados indesejados do manifesto.

![Início de corte de um ativo com Manifesto Dinâmico][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criando subclips (vistas) a partir de um arquivo ao vivo

Muitos eventos ao vivo são longos e o arquivo ao vivo pode incluir vários eventos. Após o fim do evento ao vivo, os emissores podem querer dividir o arquivo ao vivo em sequências lógicas de início do programa e parar sequências.

Pode publicar estes programas virtuais separadamente sem pós-processamento do arquivo ao vivo e não criar ativos separados (que não obtém o benefício dos fragmentos em cache existentes nos CDNs). Exemplos de tais programas virtuais são os quartos de um jogo de futebol ou basquetebol, innings no basebol, ou eventos individuais de qualquer programa desportivo.

Com o Dynamic Manifest, pode criar filtros utilizando os tempos de início/fim e criar vistas virtuais sobre o topo do seu arquivo ao vivo.

![Filtro subclip com Manifesto Dinâmico][subclip_filter]

Aqui está o ativo filtrado:

![Ativo filtrado com Manifesto Dinâmico][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Ajuste da janela de apresentação (DVR)

Atualmente, a Azure Media Services oferece um arquivo circular onde a duração pode ser configurada entre 1 minuto e 25 horas. A filtragem manifesta pode ser usada para criar uma janela de DVR rolante por cima do arquivo, sem eliminar os meios de comunicação. Há muitos cenários em que os emissores querem fornecer uma janela de DVR limitada para mover-se com a borda ao vivo e, ao mesmo tempo, manter uma janela de arquivo maior. Um emissor pode querer usar os dados que estão fora da janela do DVR para realçar clips, ou podem querer fornecer diferentes janelas de DVR para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não lida com grandes janelas de DVR (pode ter uma janela de DVR de 2 minutos para dispositivos móveis e uma hora para clientes de desktop).

![Janela DVR com Manifesto Dinâmico][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar o LiveBackoff (posição ao vivo)

A filtragem manifesta pode ser usada para remover vários segundos da borda viva de um programa ao vivo. A filtragem permite que os emissores assistam à apresentação no ponto de publicação de pré-visualização e criem pontos de inserção de publicidade antes que os espectadores recebam o stream (recuado por 30 segundos). Os emissores podem então empurrar estes anúncios para os seus quadros de clientes a tempo de receberem e processarem a informação antes da oportunidade de publicidade.

Além do suporte de publicidade, a definição de back-off ao vivo pode ser usada para ajustar a posição dos espectadores de modo a que, quando os clientes derivam e atingem a borda ao vivo, ainda possam obter fragmentos do servidor. Desta forma, os clientes não terão um erro HTTP 404 ou 412.

![Filtro para back-off ao vivo com Manifesto Dinâmico][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinando várias regras num único filtro

Pode combinar várias regras de filtragem num único filtro. Por exemplo, pode definir uma "regra de alcance" para remover ardósias de um arquivo vivo e também filtrar os bitrates disponíveis. Quando se aplicam várias regras de filtragem, o resultado final é a intersecção de todas as regras.

![Regras de filtragem múltiplas com Manifesto Dinâmico][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinação de vários filtros (composição do filtro)

Também pode combinar vários filtros num único URL. O seguinte cenário demonstra por que pode querer combinar filtros:

1. É necessário filtrar as suas qualidades de vídeo para dispositivos móveis, como Android ou iPad (de forma a limitar as qualidades de vídeo). Para remover as qualidades indesejadas, criará um filtro de conta adequado para os perfis do dispositivo. Pode utilizar filtros de conta para todos os seus ativos na mesma conta dos Serviços de Comunicação Social sem qualquer outra associação.
1. Também pretende cortar o tempo de início e fim de um ativo. Para fazer o corte, criará um filtro de ativos e definirá o tempo de início/fim.
1. Quer combinar estes dois filtros. Sem combinação, seria necessário adicionar filtro de qualidade ao filtro de aparar, o que dificultaria a utilização do filtro.

Para combinar filtros, deite os nomes do filtro ao URL manifesto/playlist em formato delimitado em ponto de referência. Vamos supor que tem um filtro chamado *MyMobileDevice* que filtra qualidades, e tem outro chamado *MyStartTime* para definir uma hora de início específica. Pode combinar até três filtros.

Para mais informações, consulte [este post de blog.](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)

## <a name="considerations-and-limitations"></a>Considerações e limitações

- Os valores para **forceEndTimestamp**, **presentationWindowDuration** e **liveBackoffDuration** não devem ser definidos para um filtro VOD. São usados apenas para cenários de filtros vivos.
- Um manifesto dinâmico opera nos limites do GOP (quadros-chave), pelo que o corte tem precisão gop.
- Pode utilizar o mesmo nome de filtro para filtros de conta e de ativos. Os filtros de ativos têm uma precedência mais elevada e sobrepõem-se aos filtros de conta.
- Se atualizar um filtro, pode demorar até 2 minutos para o ponto final de streaming atualizar as regras. Se utilizar filtros para servir o conteúdo (e em cache do conteúdo em proxies e caches CDN), atualizar estes filtros pode resultar em falhas do leitor. Recomendamos que limpe a cache depois de atualizar o filtro. Se esta opção não for possível, considere usar um filtro diferente.
- Os clientes precisam de descarregar manualmente o manifesto e analisar o carimbo exato da hora de início e a escala de tempo.

    - Para determinar as propriedades das faixas num ativo, [obtenha e examine o ficheiro manifesto.](#get-and-examine-manifest-files)
    - A fórmula para definir as propriedades do carimbo de tempo do filtro do ativo é: <br/>startTimestamp = &lt; tempo de início no manifesto tempo de início do filtro esperado em &gt;  +   &lt; segundos &gt; * escala de tempo

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos mostram como criar filtros programáticamente:  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com CLI](filters-dynamic-manifest-cli-howto.md)

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
