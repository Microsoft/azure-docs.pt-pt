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
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: ee0200f7c007b437a27b8e9d0f36becc13b8f611
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835824"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Pré-filtrar manifestos através de Packager dinâmico

Quando estiver a proporcionar velocidade de transmissão adaptável, transmissão em fluxo conteúdo de dispositivos, muitas vezes, tem de publicar várias versões de um manifesto para capacidades específicas de dispositivo de destino ou de largura de banda de rede disponível. O [Packager dinâmica](dynamic-packaging-overview.md) permite-lhe especificar filtros que podem filtrar os codecs específico, resoluções e velocidades de transmissão e áudio controlam combinações no momento eliminando a necessidade de criar várias cópias. Só precisa publicar um novo URL com um conjunto específico de filtros configurados para os dispositivos de destino (iOS, Android, SmartTV ou navegadores) e as capacidades de rede (cenários de largura de banda alta, móveis ou largura de banda baixa). Neste caso, os clientes podem manipular a transmissão em fluxo do seu conteúdo através da seqüência de caracteres de consulta (especificando disponíveis [filtros de elemento ou filtros de conta](filters-concept.md)) e utilizar filtros para secções específicas do fluxo de um fluxo.

Alguns cenários de entrega requerem que faça-se de que um cliente não conseguir aceder roteiros específicos. Por exemplo, talvez não queira publicar um manifesto que contém as faixas de HD para um escalão de assinante específico. Em alternativa, pode querer remover roteiros específico velocidade de transmissão adaptável (ABR) para reduzir o custo da entrega de um dispositivo específico que não se beneficiaria de roteiros adicionais. Neste caso podia associar uma lista dos filtros previamente criadas com o seu [localizador de transmissão em fluxo](streaming-locators-concept.md) após a criação. Nesse caso, os clientes não podem manipular como o conteúdo é transmitido, é definido pelos **localizador de transmissão em fluxo**.

Pode combinar a filtragem por meio de especificando [filtros no localizador de transmissão em fluxo](filters-concept.md#associating-filters-with-streaming-locator) + filtros específicos de dispositivo adicionais que o cliente especifica no URL. Isso pode ser útil restringir pistas adicionais, como fluxos de metadados ou eventos, idiomas de áudio ou faixas de áudio descritivas. 

Esta capacidade de especificar filtros diferentes no seu fluxo, fornece um poderoso **dinâmica manifestar** solução de manipulação para vários cenários de casos de utilização para os seus dispositivos de destino de destino. Este tópico explica os conceitos relacionados com **manifestos dinâmica** e fornece exemplos de cenários em que pode querer utilizar esta funcionalidade.

> [!NOTE]
> Os manifestos dinâmicos não altere o elemento e o manifesto de padrão para esse recurso. 
> 

##  <a name="overview-of-manifests"></a>Descrição geral de manifestos

Serviços de multimédia do Azure suporta protocolos HLS, MPEG DASH e Smooth Streaming. Como parte da [empacotamento dinâmico](dynamic-packaging-overview.md), os transmissão em fluxo manifestos de cliente (lista de reprodução do mestre de HLS, descrição de apresentação de suporte de dados do TRAÇO [MPD] e transmissão em fluxo uniforme) são gerados dinamicamente com base no Seletor de formato no URL. Consulte os protocolos de entrega [fluxo de trabalho de demanda comum](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Obter e examinar os arquivos de manifesto

Especifique uma lista de condições de propriedade de controle de filtro com base no qual faixas de sua transmissão em fluxo (ao vivo ou vídeo a pedido [VOD]) devem ser incluídas num manifesto criado dinamicamente. Para obter e examinar as propriedades dos roteiros, terá de carregar o manifesto de transmissão em fluxo uniforme primeiro.

O [carregar, codificar e transmitir ficheiros com o .NET](stream-files-tutorial-with-api.md#get-streaming-urls) tutorial mostra-lhe como criar os URLs de transmissão em fluxo com o .NET. Se executar a aplicação, um dos URLs de aponta para o manifesto de transmissão em fluxo uniforme: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Copie e cole o URL na barra de endereço do browser. O ficheiro será transferido. Pode abri-lo num editor de texto à sua escolha.

Para obter um exemplo do REST, veja [carregar, codificar e transmitir ficheiros com REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorizar a velocidade de transmissão de um fluxo de vídeo

Pode utilizar o [página de demonstração do leitor de multimédia do Azure](http://aka.ms/azuremediaplayer) para monitorizar a velocidade de transmissão de um fluxo de vídeo. A página de demonstração Exibe informações de diagnóstico sobre o **diagnóstico** separador:

![Diagnóstico do leitor de multimédia do Azure][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Exemplos: URLs com filtros na cadeia de consulta

Pode aplicar filtros para ABR protocolos de transmissão em fluxo: HLS, MPEG-DASH e Smooth Streaming. A tabela seguinte mostra alguns exemplos de URLs com filtros:

|Protocol|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtragem de representação

Pode optar por codificar o seu elemento para vários perfis de codificação (linha de base H.264, H.264 elevada, AACL, AACH, Dolby Digital mais) e múltiplas velocidades de transmissão de qualidade. No entanto, nem todos os dispositivos de cliente irão suportar perfis de todos os seus ativos e velocidades de transmissão. Por exemplo, dispositivos Android mais antigos suportam apenas, como H.264 da linha de base + AACL. Enviando velocidades de transmissão superior a um dispositivo que não é possível obter os benefícios desperdiça largura de banda e o dispositivo de computação. Tal um dispositivo tem descodificar todas as informações de determinado, apenas para a dimensionar para baixo para exibição.

Com um manifesto dinâmico, pode criar perfis de dispositivo (como dispositivos móveis, consola ou HD/SD) e incluem os roteiros e qualidades que deseja fazer parte de cada perfil. Que é chamado de filtragem de representação. O diagrama seguinte mostra um exemplo do mesmo.

![Exemplo de filtragem de representação][renditions2]

No exemplo a seguir, um codificador foi utilizado para codificar um recurso de mezanino em sete representações de vídeo de MP4s de ISO (a partir de 180p para 1080p). Pode ser o elemento codificado [dinamicamente em pacote](dynamic-packaging-overview.md) em qualquer um dos seguintes protocolos de transmissão em fluxo: HLS, MPEG DASH e Smooth. 

Parte superior do diagrama seguinte mostra que os HLS manifesto para o elemento com nenhum filtro. (Ele contém todas as representações de sete.)  No canto inferior esquerdo, o diagrama mostra um manifesto HLS ao qual foi aplicado um filtro com o nome "ott". O filtro de "ott" Especifica a remoção de todas as velocidades de transmissão abaixo 1 Mbps, para que os níveis de qualidade dois na parte inferior foram eliminados na resposta. No canto inferior direito, o diagrama mostra o manifesto HLS ao qual foi aplicado um filtro com o nome "móvel". O filtro "móvel" Especifica a remoção de representações em que a resolução é maior do que 720p, para que o duas 1080 representações de p foram suprimidas.

![Filtragem de representação][renditions1]

## <a name="removing-language-tracks"></a>Remover faixas de idioma
Os recursos podem incluir vários idiomas de áudio, como inglês, espanhol, francês, etc. Normalmente, os gerentes de Player SDK predefinido a seleção de faixa de áudio e áudio disponível controla por seleção do usuário.

Desenvolver tais Player SDKs é difícil, porque ela exige implementações diferentes nas estruturas de player específicos do dispositivo. Além disso, em algumas plataformas, APIs de leitor são limitadas e não incluem o recurso de seleção de áudio em que os utilizadores não é possível selecionar ou alterar a faixa de áudio padrão. Com os filtros de recurso, pode controlar o comportamento através da criação de filtros que incluem apenas os idiomas de áudio desejados.

![Filtragem de faixas de idioma][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Corte o início de um ativo

Na maioria dos eventos de transmissão em fluxo em direto, os operadores de executam alguns testes antes do evento real. Por exemplo, eles podem incluir uma ficha, assim, antes do início do evento: "Programa vai começar em instantes." 

Se o programa é arquivar, o teste e os dados de imagem fixa são também arquivados e incluídos na apresentação. No entanto, estas informações não devem ser mostradas aos clientes. Com o manifesto dinâmico, pode criar um filtro de tempo de início e remove os dados indesejados de manifesto.

![Remoção de início][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criação de subclips (exibições) a partir de um arquivo live

Número de eventos em direto é de longa e arquivo live pode incluir vários eventos. Após o evento ao vivo terminar, emissoras talvez queira dividir este arquivo live no início do programa de lógica e parar sequências. 

Pode publicar esses programas virtual separadamente sem postagem processar este arquivo live e não crie recursos separados (o que não é o benefício dos fragmentos em cache existentes nas CDNs). Exemplos de tais programas virtual são os trimestres de um football ou jogo basquete, innings no beisebol ou eventos individuais de qualquer programa do desporto.

Com o manifesto dinâmico, pode criar filtros com horas de início/fim e criar vistas virtual ao longo da parte superior do seu arquivo live. 

![Filtro do subclip][subclip_filter]

Este é o elemento filtrado:

![Esqui][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Ajustar a janela de apresentação (DVR)

Atualmente, os serviços de multimédia do Azure oferece arquivo circular, onde a duração pode ser configurada entre 5 minutos e 25 horas. Filtragem de manifesto pode ser utilizado para criar uma janela DVR sem interrupção ao longo da parte superior do arquivo, sem eliminar o suporte de dados. Existem muitos cenários em que desejam fornecer uma janela DVR limitada a mover-se com o edge em direto e ao mesmo tempo, manter uma janela de arquivo maior emissoras. A emissora poderá querer utilizar os dados que está fora da janela de DVR para realçar clips ou podem optar por fornecer windows DVR diferentes para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não lidar com grandes windows DVR (pode ter uma janela DVR de 2 minutos, para dispositivos móveis e de 1 hora para clientes de ambiente de trabalho).

![Janela DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar LiveBackoff (posição em direto)

Filtragem de manifesto pode ser utilizado para remover vários segundos do edge em direto de um programa em direto. Filtragem permite emissoras Assista à apresentação no ponto de publicação de pré-visualização e criar pontos de inserção de anúncio antes dos visualizadores de recebem o fluxo (alicerçado 30 segundos). Emissoras, em seguida, podem enviar estes anúncios para suas estruturas de cliente no tempo para os mesmos receber e processar as informações antes da oportunidade de anúncio.

Além do suporte de anúncio, a definição de término em direto pode ser utilizada para ajustar a posição dos visualizadores para que quando os clientes de inconsistências e atingir o limite em direto, ainda podem obter fragmentos do servidor. Dessa forma, os clientes não obtêm um erro HTTP 404 ou erro 412.

![Filtro de término em direto][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>A combinação de várias regras num único filtro

Pode combinar várias regras de filtragem num único filtro. Por exemplo, pode definir uma "regra de intervalo" para remover slates de um arquivo em direto e filtrar também disponíveis velocidades de transmissão. Quando está a aplicar várias regras de filtragem, o resultado final é a interseção de todas as regras.

![Várias regras de filtragem][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>A combinação de vários filtros (composição de filtro)

Também pode combinar vários filtros numa única URL. O cenário a seguir demonstra por que pode querer combinar os filtros:

1. Tem de filtrar as qualidades de vídeo para dispositivos móveis, como Android ou iPad (para limitar as qualidades de vídeo). Para remover as qualidades indesejadas, irá criar um filtro da conta adequada para os perfis de dispositivo. Pode utilizar filtros de conta para todos os seus ativos na mesma conta de serviços de multimédia sem qualquer outra associação.
1. Também queira limitar a hora de início e fim de um ativo. Para conseguir isso, irá criar um filtro de elemento e definir a hora de início/fim. 
1. Pode querer combinar ambos estes filtros. Sem combinação, terá de adicionar a filtragem de qualidade para o filtro de remoção, o que tornaria a utilização de filtro mais difícil.


Para combinar os filtros, tem de definir os nomes de filtro para a manifesto/lista de reprodução URL no formato delimitado por ponto e vírgula. Vamos supor que tem um filtro com o nome *MyMobileDevice* que filtra qualidades e tiver outro denominado *MyStartTime* definir específicos de um hora de início. Pode combinar os filtros de até três. 

Para obter mais informações, consulte [nesta mensagem de blogue](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Considerações e limitações

- Os valores para **forceEndTimestamp**, **presentationWindowDuration**, e **liveBackoffDuration** não deve ser definida para um filtro VOD. Eles são usados apenas para cenários de filtro em direto. 
-  Um manifesto dinâmico funciona em limites de GOP (quadros-chave), então, corte tem precisão GOP.
-  Pode utilizar o mesmo nome de filtro para os filtros de conta e recurso. Filtros de elemento tem precedência e irão substituir os filtros de conta.
- Se atualizar um filtro, pode demorar até 2 minutos para o ponto final de transmissão em fluxo atualizar as regras. Se utilizou os filtros para servir o conteúdo (e armazenados em cache o conteúdo de proxies e CDN coloca em cache), a atualizar estes filtros pode resultar em falhas de player. Recomendamos que limpe a cache depois de atualizar o filtro. Se esta opção não for possível, considere utilizar um filtro de diferente.
- Os clientes têm de transferir o manifesto manualmente e analisar o carimbo de hora de início exatas e escala de tempo.
    
    - Para determinar as propriedades dos roteiros num recurso [obter e examinar o arquivo de manifesto](#get-and-examine-manifest-files).
    - A fórmula para definir as propriedades de carimbo de data / hora de filtro de elemento é: <br/>startTimestamp = &lt;hora de início no manifesto&gt; +  &lt;esperado hora de início de filtro em segundos&gt; * escala temporal

## <a name="next-steps"></a>Passos Seguintes

Os artigos seguintes mostram como criar programaticamente os filtros:  

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
