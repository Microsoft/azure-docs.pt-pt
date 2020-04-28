---
title: Filtre os seus manifestos usando o Pacote Dinâmico
titleSuffix: Azure Media Services
description: Aprenda a criar filtros utilizando o Dynamic Packager para filtrar e transmitir seletivamente os seus manifestos.
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
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74186219"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtre os seus manifestos usando o Pacote Dinâmico

Quando está a fornecer conteúdo de streaming de bitrate adaptativo aos dispositivos, às vezes é necessário publicar várias versões de um manifesto para direcionar capacidades específicas do dispositivo ou largura de banda de rede disponível. O [Pacote Dinâmico](dynamic-packaging-overview.md) permite especificar filtros que podem filtrar codificações específicas, resoluções, bitrates e combinações de faixas áudio no voo. Esta filtragem remove a necessidade de criar várias cópias. Basta publicar um novo URL com um conjunto específico de filtros configurados para os seus dispositivos-alvo (iOS, Android, SmartTV ou navegadores) e as capacidades de rede (cenários de largura de banda alta, móvel ou de baixa largura de banda). Neste caso, os clientes podem manipular o streaming do seu conteúdo através da cadeia de consulta (especificando filtros de ativos disponíveis [ou filtros de conta)](filters-concept.md)e usar filtros para transmitir secções específicas de um fluxo.

Alguns cenários de entrega requerem que se certifique de que um cliente não pode aceder a faixas específicas. Por exemplo, talvez não queira publicar um manifesto que contenha faixas HD para um nível específico de subscritores. Ou, talvez queira remover faixas específicas de bitrate adaptativo (ABR) para reduzir o custo de entrega a um dispositivo específico que não beneficiaria das faixas adicionais. Neste caso, pode associar uma lista de filtros pré-criados ao seu Localizador de [Streaming](streaming-locators-concept.md) na criação. Os clientes não podem então manipular a forma como o conteúdo é transmitido porque é definido pelo Localizador de **Streaming.**

Pode combinar filtragem através da especificação de [filtros no Localizador de Streaming](filters-concept.md#associating-filters-with-streaming-locator) + filtros adicionais específicos do dispositivo que o seu cliente especifica no URL. Esta combinação é útil para restringir faixas adicionais como metadados ou fluxos de eventos, linguagens áudio ou faixas de áudio descritivas.

Esta capacidade de especificar diferentes filtros no seu fluxo fornece uma poderosa solução de manipulação **Dynamic Manifest** para direcionar múltiplos cenários de utilização para os seus dispositivos-alvo. Este tópico explica conceitos relacionados com **Manifestos Dinâmicos** e dá exemplos de cenários em que pode utilizar esta funcionalidade.

> [!NOTE]
> Os Manifestos Dinâmicos não alteram o ativo e o manifesto padrão para esse ativo.

## <a name="overview-of-manifests"></a>Visão geral dos manifestos

A Azure Media Services suporta protocolos HLS, MPEG DASH e Smooth Streaming. Como parte da [Dynamic Packaging,](dynamic-packaging-overview.md)os manifestos do cliente de streaming (HLS Master Playlist, DASH Media Presentation Description [MPD], e Smooth Streaming) são gerados dinamicamente com base no seletor de formato no URL. Para obter mais informações, consulte os protocolos de entrega no [fluxo de trabalho comum](dynamic-packaging-overview.md#delivery-protocols)a pedido.

### <a name="get-and-examine-manifest-files"></a>Obter e examinar ficheiros manifestos

Especifica uma lista de condições de propriedade de filtro com base em quais as faixas do seu fluxo (ao vivo ou vídeo a pedido [VOD]) devem ser incluídas num manifesto criado dinamicamente. Para obter e examinar as propriedades das faixas, você tem que carregar o manifesto de Streaming Suave primeiro.

O [Upload, codificação e ficheiros de streaming com o](stream-files-tutorial-with-api.md#get-streaming-urls) tutorial .NET mostra-lhe como construir os URLs de streaming com .NET. Se executar a aplicação, um dos URLs aponta `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`para o manifesto Smooth Streaming: .<br/> Copie e cole o URL na barra de endereços de um browser. O ficheiro será descarregado. Pode abri-lo em qualquer editor de texto.

Para um exemplo REST, consulte [Upload, codificar e transmitir ficheiros com REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorize a bitrate de um fluxo de vídeo

Pode utilizar a [página de demonstração do Azure Media Player](https://aka.ms/azuremediaplayer) para monitorizar a bitrate de um fluxo de vídeo. A página de demonstração exibe informações de diagnóstico no separador **Diagnósticos:**

![Diagnósticos de Azure Media Player][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Exemplos: URLs com filtros em cadeia de consulta

Pode aplicar filtros aos protocolos de streaming ABR: HLS, MPEG-DASH e Smooth Streaming. A tabela que se segue mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtragem de rendição

Pode optar por codificar o seu ativo a múltiplos perfis de codificação (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) e a várias bitrates de qualidade. No entanto, nem todos os dispositivos de cliente irão suportar todos os perfis e bitrates do seu ativo. Por exemplo, dispositivos Android mais antigos suportam apenas H.264 Baseline+AACL. Enviar bitrates mais elevados para um dispositivo que não consegue obter os benefícios desperdiça largura de banda e computação de dispositivos. Tal dispositivo deve descodificar todas as informações dadas, apenas para a reduzir para visualização.

Com o Dynamic Manifest, pode criar perfis de dispositivo (como mobile, consola ou HD/SD) e incluir as faixas e qualidades que pretende fazer parte de cada perfil. Chama-se filtragem de rendição. O diagrama que se segue mostra um exemplo.

![Exemplo de filtragem de rendição com Manifesto Dinâmico][renditions2]

No exemplo seguinte, um codificador foi usado para codificar um ativo mezanino em sete representações de vídeo ISO MP4s (de 180p a 1080p). O ativo codificado pode ser [embalado dinamicamente](dynamic-packaging-overview.md) em qualquer um dos seguintes protocolos de streaming: HLS, MPEG DASH e Smooth.

A parte superior do diagrama seguinte mostra o manifesto HLS para o ativo sem filtros. (Contém todas as sete representações.)  Na esquerda inferior, o diagrama mostra um manifesto HLS ao qual foi aplicado um filtro chamado "ott". O filtro "ott" especifica a remoção de todas as bitrates abaixo de 1 Mbps, pelo que os dois níveis de qualidade inferiores foram despojados na resposta. Na direita inferior, o diagrama mostra o manifesto HLS ao qual foi aplicado um filtro chamado "mobile". O filtro "móvel" especifica a remoção de rendições em que a resolução é maior do que 720p, pelo que as duas representações de 1080p foram retiradas.

![Filtragem de rendição com Manifesto Dinâmico][renditions1]

## <a name="removing-language-tracks"></a>Remoção de faixas linguísticas
Os seus ativos podem incluir várias línguas áudio, como inglês, espanhol, francês, e assim por diante. Normalmente, o Player SDK gere a seleção de faixas de áudio padrão e as faixas de áudio disponíveis por seleção do utilizador.

Desenvolver tais SDKs player é um desafio porque requer implementações diferentes em diferentes quadros de jogadores específicos do dispositivo. Além disso, em algumas plataformas, as APIs do Jogador são limitadas e não incluem a funcionalidade de seleção de áudio onde os utilizadores não podem selecionar ou alterar a faixa de áudio padrão. Com filtros de ativos, pode controlar o comportamento criando filtros que apenas incluem idiomas áudio desejados.

![Filtragem de faixas linguísticas com Manifesto Dinâmico][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Aparar o início de um ativo

Na maioria dos eventos de streaming ao vivo, os operadores executam alguns testes antes do evento real. Por exemplo, podem incluir uma ardósia como esta antes do início do evento: "O programa começará momentaneamente."

Se o programa estiver a arquivar, os dados de teste e ardósia também são arquivados e incluídos na apresentação. No entanto, esta informação não deve ser mostrada aos clientes. Com o Dynamic Manifest, pode criar um filtro de hora de início e remover os dados indesejados do manifesto.

![Corte de um ativo com Manifesto Dinâmico][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criação de subclips (vistas) a partir de um arquivo ao vivo

Muitos eventos ao vivo são longos e arquivo ao vivo pode incluir vários eventos. Após o fim do evento ao vivo, os emissores podem querer dividir o arquivo ao vivo em sequências de início e paragem lógicas do programa.

Pode publicar estes programas virtuais separadamente sem processar o arquivo ao vivo e não criar ativos separados (que não beneficiam os fragmentos em cache existentes nos CDNs). Exemplos de tais programas virtuais são os quartos de um jogo de futebol ou basquetebol, innings no basebol, ou eventos individuais de qualquer programa desportivo.

Com o Dynamic Manifest, pode criar filtros utilizando tempos de início/fim e criar vistas virtuais sobre o topo do seu arquivo ao vivo.

![Filtro de subclip com Manifesto Dinâmico][subclip_filter]

Aqui está o ativo filtrado:

![Ativo filtrado com Manifesto Dinâmico][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Ajuste da janela de apresentação (DVR)

Atualmente, a Azure Media Services oferece um arquivo circular onde a duração pode ser configurada entre 1 minuto e 25 horas. A filtragem manifesta pode ser usada para criar uma janela DVR rolante sobre a parte superior do arquivo, sem apagar os meios de comunicação. Há muitos cenários em que os emissores querem fornecer uma janela de DVR limitada para mover-se com a borda ao vivo e, ao mesmo tempo, manter uma janela de arquivo maior. Um emissor pode querer usar os dados que estão fora da janela dVR para realçar clips, ou podem querer fornecer diferentes janelas de DVR para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não lida com grandes janelas de DVR (pode ter uma janela dVR de 2 minutos para dispositivos móveis e uma hora para clientes de desktop).

![Janela DVR com Manifesto Dinâmico][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajuste do LiveBackoff (posição ao vivo)

A filtragem manifesta pode ser usada para remover alguns segundos da borda ao vivo de um programa ao vivo. A filtragem permite que os emissores assistam à apresentação no ponto de publicação de pré-visualização e criem pontos de inserção de publicidade antes que os espectadores recebam o fluxo (recuado por 30 segundos). Os organismos de radiodifusão podem então empurrar estes anúncios para os seus quadros de clientes a tempo de receberem e processarem a informação antes da oportunidade de publicidade.

Além do suporte publicitário, o back-off ao vivo pode ser usado para ajustar a posição dos espectadores para que quando os clientes deslizam e batam na borda ao vivo, ainda possam obter fragmentos do servidor. Assim, os clientes não terão um erro HTTP 404 ou 412.

![Filtro para back-off ao vivo com Manifesto Dinâmico][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinando várias regras num único filtro

Pode combinar várias regras de filtragem num único filtro. Por exemplo, pode definir uma "regra de alcance" para remover as ardósias de um arquivo ao vivo e também filtrar as bitrates disponíveis. Quando se aplica várias regras de filtragem, o resultado final é a intersecção de todas as regras.

![Múltiplas regras de filtragem com Manifesto Dinâmico][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinando vários filtros (composição do filtro)

Também pode combinar vários filtros num único URL. O seguinte cenário demonstra por que razão poderá querer combinar filtros:

1. É necessário filtrar as qualidades de vídeo para dispositivos móveis, como Android ou iPad (para limitar as qualidades de vídeo). Para remover as qualidades indesejadas, irá criar um filtro de conta adequado aos perfis do dispositivo. Pode utilizar filtros de conta para todos os seus ativos na mesma conta de Serviços de Media sem qualquer outra associação.
1. Também quer cortar o tempo de início e fim de um ativo. Para fazer o corte, criará um filtro de ativo e definirá o tempo de início/fim.
1. Quer combinar os dois filtros. Sem combinação, é necessário adicionar filtragem de qualidade ao filtro de corte, o que dificultaria o uso do filtro.

Para combinar filtros, delineie os nomes dos filtros para o URL manifesto/lista de reprodução em formato semicólon-delimitado. Vamos supor que tem um filtro chamado *MyMobileDevice* que filtra qualidades, e tem outro chamado *MyStartTime* para definir uma hora de início específica. Pode combinar até três filtros.

Para mais informações, consulte [esta publicação de blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Considerações e limitações

- Os valores para **forceEndTimestamp**, **apresentaçãoJanelaDuração**e **liveBackoffDura** não deve ser definido para um filtro VOD. São usados apenas para cenários de filtros vivos.
- Um manifesto dinâmico funciona nos limites gop (quadros-chave), por isso aparar tem precisão GOP.
- Pode utilizar o mesmo nome de filtro para os filtros de conta e de ativos. Os filtros de ativos têm maior precedência e anularão os filtros da conta.
- Se atualizar um filtro, pode demorar até 2 minutos para o ponto final de streaming atualizar as regras. Se usou filtros para servir o conteúdo (e cacheed o conteúdo em proxies e caches CDN), a atualização destes filtros pode resultar em falhas dos jogadores. Recomendamos que limpe a cache depois de atualizar o filtro. Se esta opção não for possível, considere usar um filtro diferente.
- Os clientes precisam de descarregar manualmente o manifesto e analisar o carimbo exato de hora de início e a escala de tempo.

    - Para determinar as propriedades das faixas num ativo, [obtenha e examine o ficheiro manifesto](#get-and-examine-manifest-files).
    - A fórmula para definir as propriedades do carimbo de tempo do filtro do ativo é: <br/>startTimestamp &lt;= hora de&gt; +  &lt;início no manifesto&gt; esperado tempo de início do filtro em segundos * escala de tempo

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
