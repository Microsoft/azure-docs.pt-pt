---
title: Advanced Media Encoder Premium Workflow tutorials (Tutoriais avançados do Media Encoder Premium Workflow)
description: Este documento contém avanços que mostram como executar tarefas avançadas com o Fluxo de Trabalho Premium Media Encoder e também como criar fluxos de trabalho complexos com o Workflow Designer.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc
ms.reviewer: xpouyat; juliako
ms.openlocfilehash: 55d9d9191dc62d7397c5de944985d5653eff4e13
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269833"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Advanced Media Encoder Premium Workflow tutorials (Tutoriais avançados do Media Encoder Premium Workflow)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Descrição geral
Este documento contém imagens que mostram como personalizar fluxos de trabalho com **o Workflow Designer.** Pode encontrar [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)os ficheiros de fluxo de trabalho reais.  

## <a name="toc"></a>TOC
Os seguintes tópicos são abordados:

* [Codificar a MXF num único MP4 bitrate](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Iniciar um novo fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Utilização da entrada de ficheiros de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspeção dos fluxos de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Adicionar um codificader de vídeo para . Geração de ficheiros MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Codificação do fluxo de áudio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexing Audio e Vídeo transmite para um recipiente MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Escrever o ficheiro MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Criação de um Ativo de Serviços de Mídia a partir do ficheiro de saída](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Teste o fluxo de trabalho acabado localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Codificação MXF em MP4s multibitráveis - embalagem dinâmica ativada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Adicionar uma ou mais saídas mp4 adicionais](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Configurar os nomes de saída do ficheiro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Adicionar uma faixa de áudio separada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Adicionar o Ficheiro SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Codificação MXF em MP4 multibitrrado - planta melhorada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Visão geral do fluxo de trabalho para melhorar
  * [Convenções de nomeação de ficheiros](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publicar propriedades de componentes na raiz do fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Geraram nomes de ficheiros de saída baseados em valores de propriedade publicados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Adicionar miniaturas à saída MP4 multibitrária](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Visão geral do fluxo de trabalho para adicionar miniaturas a
  * [Adicionar a codificação JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Lidar com a conversão do Espaço De Cor](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Escrever as miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Deteção de erros num fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Fluxo de trabalho acabado](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Aparar tempo de saída mp4 multibitrária](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Visão geral do fluxo de trabalho para começar a adicionar aparas a](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Utilizando o aparador de fluxo](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Fluxo de trabalho acabado](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introduzindo o Componente Scripted](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Scripting dentro de um fluxo de trabalho: olá mundo](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Corte baseado em quadros de saída mp4 multibitrária](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Visão geral da planta para começar a adicionar aparas para](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Utilização da Lista de Clip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modificação da lista de clipes de um componente scripted](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Adicionar uma propriedade de conveniência ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Codificar a MXF num único MP4 bitrate
Esta secção demonstra como criar um único bitrate . Ficheiro MP4 com áudio codificado AAC-HE de um . Ficheiro de entrada MXF.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Iniciar um novo fluxo de trabalho
Open Workflow Designer e selecione Arquivo > Novo Espaço de Trabalho > Transcode Blueprint

O novo fluxo de trabalho mostra três elementos:

* Ficheiro de Origem Primária
* Lista de clipes XML
* Ficheiro de saída/Ativo  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Novo fluxo de trabalho de codificação*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>Utilização da entrada de ficheiros de mídia
Para aceitar o ficheiro de meios de entrada, começa-se por adicionar um componente de Entrada de Ficheiros de Media. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa do Repositório e arraste a entrada desejada para o painel de design. Repita a ação para a entrada de ficheiros de mídia e ligue o componente ficheiro de origem primária ao pin de entrada de nome de ficheiro da entrada de ficheiros de mídia.

![Entrada de ficheiros de mídia conectada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada de ficheiros de mídia conectada*

Inicialmente, identifique um ficheiro de amostra apropriado para utilizar ao conceber um fluxo de trabalho personalizado. Para tal, clique no fundo do painel de design e procure a propriedade Primary Source File no painel de propriedade da direita. Clique no ícone da pasta e selecione o ficheiro pretendido para testar o fluxo de trabalho. O componente de entrada de ficheiros de mídia inspeciona o ficheiro e povoa os seus pinos de saída para refletir os detalhes do ficheiro de amostra que inspecionou.

![Entrada de ficheiros de mídia povoada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada de ficheiros de mídia povoada*

Agora que a entrada é povoada, o próximo passo é configurar as definições de codificação de saída. Semelhante à configuração do Ficheiro De Origem Primária, configurar agora a propriedade variável de pasta de saída, logo abaixo.

![Propriedades de entrada e saída configuradas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriedades de entrada e saída configuradas*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Inspeção dos fluxos de mídia
Muitas vezes é desejado saber como o fluxo se parece à medida que flui através do fluxo de trabalho. Para inspecionar um fluxo em qualquer ponto do fluxo de trabalho, basta clicar numa saída ou num pino de entrada em qualquer um dos componentes. Neste caso, tente clicar no pino de saída de vídeo não comprimido da Entrada de Ficheiros de Mídia. Abre-se um diálogo que permite inspecionar o vídeo de saída.

![Inspecionando o pino de saída de vídeo descomprimido](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspecionando o pino de saída de vídeo descomprimido*

Neste caso, mostra que o vídeo contém uma entrada de 1920x1080 a 24 fotogramas por segundo em 4:2:2 amostras para um vídeo de quase 2 minutos.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Adicionar um codificader de vídeo para . Geração de ficheiros MP4
Agora, um vídeo descomprimido e vários pinos de saída de áudio não comprimido estão disponíveis para utilização na entrada de ficheiros de mídia. Para codificar o vídeo de entrada, é necessário adicionar um componente de codificação ao fluxo de trabalho - neste caso para gerar . Ficheiros MP4.

Para codificar o fluxo de vídeo para H.264, adicione o componente AVC Video Encoder à superfície do designer. Este componente toma uma transmissão de vídeo descompressão como entrada e fornece um fluxo de vídeo comprimido AVC no seu pino de saída.

![Codificação AVC não ligada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codificação AVC não ligada*

As suas propriedades determinam como a codificação acontece exatamente. Vamos ver algumas das configurações mais importantes:

* Largura de saída e altura de saída: determina a resolução do vídeo codificado. Neste caso, 640x360 é um bom cenário.
* Taxa de fotogramas: quando definido para passar, apenas adotará a taxa de fotogramas de origem, é possível anular isto no entanto. Esta conversão de framerate não é compensada por movimentos.
* Perfil e Nível: determina o perfil e o nível do AVC. Para obter convenientemente mais informações sobre os diferentes níveis e perfis, clique no ícone de ponto de interrogação no componente AVC Video Encoder e a página de ajuda mostrará mais detalhes sobre cada um dos níveis. Para este exemplo, utilize o Perfil Principal no nível 3.2 (o padrão).
* Modo de Controlo de Tarifas e Bitrate (kbps): neste cenário, opte por uma saída de bitrate constante (CBR) a 1200 kbps
* Formato de vídeo: fornece informações sobre o VUI (Video Usability Information) que é escrito no fluxo H.264 (informações laterais que podem ser usadas por um descodificador para melhorar o ecrã, mas não essenciais para descodificar corretamente):
* NTSC (típico para EUA ou Japão, usando 30 fps)
* PAL (típico para a Europa, usando 25 fps)
* Modo tamanho GOP: desemote o tamanho do GOG fixo para os nossos propósitos com um intervalo de 2 segundos com GOPs fechados. A regulação de 2 segundos garante compatibilidade com a embalagem dinâmica que a Azure Media Services proporciona.

Para alimentar o codificador AVC, ligue o pino de saída de vídeo não comprimido do componente De entrada de ficheiros de mídia ao pin de entrada de vídeo não comprimido do codificador AVC.

![Codificação AVC conectada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Codificação principal AVC conectada*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Codificação do fluxo de áudio
Neste ponto, o fluxo de áudio original não comprimido ainda precisa de ser comprimido. Para compressão do fluxo de áudio adicione um componente AAC Encoder (Dolby) ao fluxo de trabalho.

![Codificação AVC não ligada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificação AAC não ligada*

Agora há uma incompatibilidade: há apenas um único pino de entrada de áudio não comprimido do Encoder AAC, enquanto mais do que provável que a Entrada de Ficheiros de Media tenha dois fluxos de áudio diferentes não comprimidos disponíveis: um para o canal de áudio esquerdo e outro para a direita. (Se está a lidar com som surround, são seis canais.) Portanto, não é possível ligar diretamente o áudio da fonte de entrada de ficheiros de mídia ao codificar de áudio AAC. A componente AAC espera um fluxo de áudio "intercalado": um único fluxo que tem os canais esquerdo e direito intercalados entre si. Assim que soubermos pelo nosso ficheiro de origem que as faixas de áudio estão em que posição na fonte, podemos gerar tal fluxo de áudio intercalado com as posições de altifalantes corretamente atribuídas para a esquerda e direita.

Primeiro, quer-se gerar um fluxo intercalado a partir dos canais de áudio de origem necessários. O componente Audio Stream Interleaver trata disto para nós. Adicione-o ao fluxo de trabalho e ligue as saídas de áudio da Entrada de Ficheiros de Mídia nele.

![Interleaver de fluxo de áudio conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Interleaver de fluxo de áudio conectado*

Agora que temos um fluxo de áudio intercalado, ainda não especificámos para onde atribuir as posições dos altifalantes esquerdo ou direito. Para especificar isto, podemos aproveitar o designador de posição do altifalante.

![Adicionar um atribuiçãodor de posição de altifalante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Adicionar um atribuiçãodor de posição de altifalante*

Configure o Designador de Posição do Altifalante para utilização com um fluxo de entrada estéreo através de um filtro predefinido encoder de "Custom" e da Predefinição do Canal chamada "2.0 (L,R)." (Isto atribui a posição do altifalante esquerdo ao canal 1 e a posição do altifalante direito ao canal 2.)

Ligue a saída do Designador de Posição do Altifalante à entrada do Codificador AAC. Em seguida, diga ao AAC Encoder para trabalhar com uma Predefinição do Canal "2.0 (L,R)", para que saiba lidar com o áudio estéreo como entrada.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexing Audio e Vídeo transmite para um recipiente MP4
Dado o nosso fluxo de vídeo codificado AVC e o nosso fluxo de áudio codificado AAC, podemos capturar ambos em um . Contentor MP4. O processo de mistura de diferentes fluxos num único é chamado de "multiplexing" (ou "muxing"). Neste caso, estamos a intercalar o áudio e os streams de vídeo num único coerente. Pacote MP4. O componente que coordena isto para um . O contentor MP4 é chamado de ISO MPEG-4 Multiplexer. Adicione um à superfície do designer e ligue tanto o Codificar de vídeo AVC como o Codificadores AAC às suas entradas.

![Multiplexer MPEG4 ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Multiplexer MPEG4 ligado*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Escrever o ficheiro MP4
Ao escrever um ficheiro de saída, é utilizado o componente de saída de ficheiro. Podemos ligá-lo à saída do MULTIPLEXer ISO MPEG-4 para que a sua saída seja escrita para o disco. Para isso, ligue o pino de saída do Recipiente (MPEG-4) ao pino de entrada write da saída de ficheiro.

![Saída de ficheiros conectados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Saída de ficheiros conectados*

O nome de ficheiro que é utilizado é determinado pela propriedade File. Embora essa propriedade possa ser codificada a um dado valor, o mais provável é que se queira defini-la através de uma expressão.

Para ter o fluxo de trabalho determinar automaticamente a propriedade do nome de ficheiro de saída a partir de uma expressão, clique no botão ao lado do nome 'Ficheiro' (ao lado do ícone da pasta). A partir do menu suspenso, em seguida, selecione "Expression". Isto traz-me o editor de expressão. Limpe o conteúdo do editor primeiro.

![Editor de Expressão Vazio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de Expressão Vazio*

O editor de expressão permite-lhe introduzir qualquer valor literal, misturado com uma ou mais variáveis. As variáveis começam com um sinal de dólar. Ao acertar na chave $, o editor mostra uma caixa de entrega com uma escolha de variáveis disponíveis. No nosso caso, usaremos uma combinação da variável do diretório de saída e da variável de nome do ficheiro de entrada base:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4`

![Preenchido Editor de Expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Preenchido Editor de Expressão*

> [!NOTE]
> Para ver um ficheiro de saída do seu trabalho de codificação em Azure, deve fornecer um valor no editor de expressão.
>
>

Quando confirma a expressão batendo ok, a janela da propriedade pré-visualiza o valor que a propriedade Do Arquivo resolve neste momento.

![A expressão de ficheiro resolve a saída dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*A expressão de ficheiro resolve a saída dir*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Criação de um Ativo de Serviços de Mídia a partir do ficheiro de saída
Embora tenhamos escrito um ficheiro de saída MP4, ainda precisamos de indicar que este ficheiro pertence ao ativo de saída que os serviços de comunicação geram como resultado da execução deste fluxo de trabalho. Para o efeito, é utilizado o nó de Ficheiro de Saída/Ativo na tela de fluxo de trabalho. Todos os ficheiros que entram neste nó fazem parte do ativo Azure Media Services resultante.

Ligue o componente de saída de ficheiro ao componente Desector de Saída/Ativo para terminar o fluxo de trabalho.

![Fluxo de trabalho acabado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fluxo de trabalho acabado*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>Teste o fluxo de trabalho acabado localmente
Para testar o fluxo de trabalho localmente, acerte no botão de reprodução na barra de ferramentas na parte superior. Quando o fluxo de trabalho terminar de executar, inspecione a saída gerada na pasta de saída configurada. Verá o ficheiro de saída MP4 acabado que foi codificado a partir do ficheiro de origem de entrada MXF.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Codificação MXF em MP4 - embalagem dinâmica multibitrária habilitada
Este walkthrough cria um conjunto de múltiplos ficheiros MP4 bitrate com áudio codificado AAC a partir de um único . Ficheiro de entrada MXF.

Quando uma saída de ativo multi-bitrate é desejada para ser usada em combinação com as funcionalidades de Embalagem Dinâmica oferecidas pela Azure Media Services, vários ficheiros MP4 alinhados com GOP de cada bitrate e resolução diferentes terão de ser gerados. Para tal, o [Encoding MXF numa única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) passagem por MP4 de bitrate proporciona-nos um bom ponto de partida.

![Fluxo de trabalho inicial](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Fluxo de trabalho inicial*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Adicionar uma ou mais saídas mp4 adicionais
Cada ficheiro MP4 no nosso ativo Azure Media Services suporta uma bitrate e resolução diferentes. Vamos adicionar um ou mais ficheiros de saída MP4 ao fluxo de trabalho.

Para garantir que temos todos os nossos codificadores de vídeo criados com as mesmas configurações, é mais conveniente duplicar o já existente AVC Video Encoder e configurar outra combinação de resolução e bitrate (vamos adicionar um de 960 x 540 a 25 fotogramas por segundo a 2,5 Mbps). Para duplicar o codificador existente, copie-o na superfície do designer.

Ligue o pino de saída de vídeo não comprimido da entrada de ficheiros de mídia no nosso novo componente AVC.

![Segundo codificação AVC conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Segundo codificação AVC conectado*

Agora adapte a configuração do nosso novo codificadores AVC à saída 960x540 a 2,5 Mbps. (Utilize as suas propriedades "Largura de saída", "altura de saída" e "Bitrate (kbps)" para isto.)

Dado que queremos utilizar o ativo resultante juntamente com a embalagem dinâmica da Azure Media Services, o ponto final de streaming precisa de ser capaz de gerar a partir destes ficheiros MP4 fragmentos de MPS/FRAGMENTado MP4/DASH que estão exatamente alinhados uns com os outros de forma a que os clientes que estão a alternar entre diferentes bitrates obtenham uma única experiência de vídeo e áudio contínua suave. Para que isso aconteça, precisamos de garantir que, nas propriedades de ambos os codificadores AVC, o tamanho do GOP ("grupo de imagens") para ambos os ficheiros MP4 é definido para 2 segundos, o que pode ser feito por:

* definindo o modo de tamanho GOP para o tamanho de GOP fixo e
* o intervalo de quadro de chave para dois segundos.
* também definir o Controlo de IDR gop para o GOP fechado para garantir que todos os GOPs estão em pé por conta própria sem dependências

Para tornar este fluxo de trabalho mais fácil de entender, mude o nome do primeiro codificadora AVC para "AVC Video Encoder 640x360 1200 kbps" e o segundo codificadora AVC "AVC Video Encoder 960x540 2500 kbps".

Adicione agora um segundo ISO MPEG-4 Multiplexer e uma segunda saída de ficheiro. Ligue o multiplexer ao novo codificador AVC e certifique-se de que a sua saída está direcionada para a saída do ficheiro. Em seguida, ligue também a saída do codificador de áudio AAC à entrada do novo multiplexer. A Saída de Ficheiro, por sua vez, pode ser ligada ao nó Desabo de Saída/Ativo para adicioná-lo ao Ativo de Serviços de Mídia que será criado.

![Segunda saída de Muxer e Ficheiro ligada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Segunda saída de Muxer e Ficheiro ligada*

Para compatibilidade com a embalagem dinâmica da Azure Media Services, configuure o modo de pedaço do multiplexer para a contagem ou duração do MULTIPLEXer e coloque os GOPs por pedaço para 1. (Este deve ser o padrão.)

![Modos de pedaço de muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos de pedaço de muxer*

Nota: pode querer repetir este processo para quaisquer outras combinações de bitrate e resolução que pretenda ter adicionado à saída do ativo.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurar os nomes de saída do ficheiro
Temos mais de um único ficheiro adicionado ao ativo de saída. Isto fornece a necessidade de garantir que os nomes de ficheiros de cada um dos ficheiros de saída são diferentes uns dos outros e talvez até apliquem uma convenção de nomeação de ficheiros para que fique claro do nome do ficheiro com o que está a lidar.

O nome de saída de ficheiro pode ser controlado através de expressões no designer. Abra o painel de propriedade para um dos componentes da saída de ficheiros e abra o editor de expressão para a propriedade File. O nosso primeiro ficheiro de saída foi configurado através da seguinte expressão (ver tutorial para ir de [MXF a uma única produção de MP4 bitrate](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4`

Isto significa que o nosso nome de ficheiro é determinado por duas variáveis: o diretório de saída para escrever e o nome base do ficheiro de origem. O primeiro é exposto como uma propriedade na raiz do fluxo de trabalho e este último é determinado pelo ficheiro de entrada. O diretório de saída é o que se usa para testes locais; esta propriedade será ultrapassada pelo motor de fluxo de trabalho quando o fluxo de trabalho for executado pelo processador de mídia baseado na nuvem em Azure Media Services.
Para dar a ambos os nossos ficheiros de saída um nome de saída consistente, altere a expressão de nomeação do primeiro ficheiro para:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4`

e a segunda para:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4`

Execute um teste intermédio para se certificar de que ambos os ficheiros de saída MP4 são corretamente gerados.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Adicionar uma faixa de áudio separada
Como veremos mais tarde quando gerarmos um ficheiro .ism para acompanhar os nossos ficheiros de saída MP4, também exigiremos um ficheiro MP4 apenas áudio como a faixa de áudio para o nosso streaming adaptativo. Para criar este ficheiro, adicione um muxer adicional ao fluxo de trabalho (ISO-MPEG-4 Multiplexer) e ligue o pino de saída do codificador AAC com o seu pino de entrada para a Faixa 1.

![Muxer de áudio adicionado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Muxer de áudio adicionado*

Crie um terceiro componente de saída de ficheiro para desausar o fluxo de saída do muxer e configurar a expressão de nomeação de ficheiro como:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4`

![Muxer de áudio criando saída de ficheiro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Muxer de áudio criando saída de ficheiro*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Adicionando o . Ficheiro SMIL ISM
Para que a embalagem dinâmica funcione em combinação com ambos os ficheiros MP4 (e o MP4 apenas áudio) no nosso ativo Media Services, precisamos também de um ficheiro manifesto (também chamado de ficheiro "SMIL": Linguagem de Integração Multimédia Sincronizada). Este ficheiro indica à Azure Media Services quais os ficheiros MP4 disponíveis para embalagem dinâmica e quais os que devem considerar para o streaming de áudio. Um ficheiro manifesto típico para um conjunto de MP4's com um único fluxo de áudio é assim:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

O ficheiro .ism contém dentro de uma declaração de comutação, uma referência a cada um dos ficheiros de vídeo MP4 individuais e além daqueles também uma (ou mais) referências de ficheiros áudio a um MP4 que contém apenas o áudio.

Gerar o ficheiro manifesto para o nosso conjunto de MP4 pode ser feito através de um componente chamado "AMS Manifesto Escritor". Para a utilizar, arraste-a para a superfície e ligue os pinos de saída "Write Complete" dos três componentes de saída de ficheiros à entrada AMS Manifest Writer. Em seguida, certifique-se de ligar a saída do Escritor Manifesto AMS ao Ficheiro/Ativo de Saída.

Tal como acontece com os nossos outros componentes de saída de ficheiros, configurar o nome de saída de ficheiro .ism com uma expressão:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism`

O nosso fluxo de trabalho acabado parece o seguinte:

![MXF acabado para fluxo de trabalho mp4 multibitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF acabado para fluxo de trabalho mp4 multibitrate*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>Codificação MXF em MP4 multibitrrado - planta melhorada
Na anterior passagem de [fluxo de trabalho, vimos](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) como um único ativo de entrada MXF pode ser convertido num ativo de saída com ficheiros MP4 multi-bitrate, um ficheiro MP4 apenas áudio e um ficheiro manifesto para utilização em conjunto com a embalagem dinâmica da Azure Media Services.

Este walkthrough mostra como alguns dos aspetos podem ser melhorados e tornados mais convenientes.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para melhorar
![Fluxo de trabalho multibitrático MP4 para melhorar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Fluxo de trabalho multibitrático MP4 para melhorar*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenções de nomeação de ficheiros
No fluxo de trabalho anterior, especificamos uma expressão simples como base para gerar nomes de ficheiros de saída. No entanto, temos algumas duplicações: todos os componentes individuais do ficheiro de saída especificaram tal expressão.

Por exemplo, o nosso componente de saída de ficheiro para o primeiro ficheiro de vídeo está configurado com esta expressão:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4`

Enquanto para o segundo vídeo de saída, temos uma expressão como:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4`

Não seria mais limpo, menos propenso a erros, e mais conveniente se pudéssemos remover alguma desta duplicação e tornar as coisas mais configuráveis? Felizmente podemos: as capacidades de expressão do designer em combinação com a capacidade de criar propriedades personalizadas na nossa raiz de fluxo de trabalho fornecerão uma camada adicional de conveniência.

Vamos supor que vamos conduzir a configuração do nome de ficheiros a partir dos bitrates dos ficheiros MP4 individuais. Estes bitrates que vamos procurar configurar num lugar central (na raiz do nosso gráfico), de onde serão acedidos à configuração e geração de nomes de ficheiros. Para isso, começamos por publicar a propriedade bitrate de ambos os codificadores AVC para a raiz do nosso fluxo de trabalho, de modo que se torne acessível tanto a partir da raiz como dos codificadores AVC. (Mesmo que exibido em dois pontos diferentes, há apenas um valor subjacente.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Publicar propriedades de componentes na raiz do fluxo de trabalho
Abra o primeiro codificação AVC, vá para a propriedade Bitrate (kbps) e da escolha dropdown Publicar.

![Publicação da propriedade bitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicação da propriedade bitrate*

Configure o diálogo de publicação para publicar na raiz do nosso gráfico de fluxo de trabalho, com um nome publicado de "video1bitrate" e um nome de exibição legível de "Video 1 Bitrate". Configure um nome de grupo personalizado chamado "Streaming Bitrates" e acerte a Publicação.

![Publicação da propriedade bitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Diálogo de publicação para propriedade bitrate*

Repita o mesmo para a propriedade bitrate do segundo codificador AVC e nomeie-o como "video2bitrate" com o nome de exibição de "Video 2 Bitrate", no mesmo grupo personalizado "Streaming Bitrates".

Se agora inspecionarmos as propriedades da raiz do fluxo de trabalho, veremos o nosso grupo personalizado com as duas propriedades publicadas aparecerem. Ambos refletem o valor do respetivo bitrate codificante AVC.

![Adereços bitrate publicados na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Sempre que quisermos aceder a estas propriedades a partir de código ou de uma expressão, podemos fazê-lo assim:

* do código inline a partir de um componente logo abaixo da raiz: node.getPropertyAsString('. /video1bitrate', nulo)
* dentro de uma expressão: ${ROOT_video1bitrate}

Vamos completar o grupo "Streaming Bitrates", publicando também o nosso bitrate de faixa sonora. Dentro das propriedades do Codificado AAC, procure a definição bitrate e selecione Publicar a partir do dropdown ao lado. Publique na raiz do gráfico com o nome "audio1bitrate" e apresente o nome "Audio 1 Bitrate" dentro do nosso grupo personalizado "Streaming Bitrates".

![Diálogo de publicação para bitrate de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Diálogo de publicação para bitrate de áudio*

![Adereços de vídeo e áudio resultantes na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Adereços de vídeo e áudio resultantes na raiz*

Alterar qualquer um destes três valores também reconfigura e altera os valores nos respetivos componentes com os quais estão ligados (e de onde são publicados).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Geraram nomes de ficheiros de saída baseados em valores de propriedade publicados
Em vez de codificar os nossos nomes de ficheiros gerados, podemos agora alterar a nossa expressão de nome de ficheiro em cada um dos componentes da Saída de Ficheiros para confiar nas propriedades bitrate que publicámos na raiz do gráfico. Começando com a nossa primeira saída de ficheiro, encontre a propriedade File e edite a expressão desta forma:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4`

Os diferentes parâmetros desta expressão podem ser acedidos e introduzidos, atingindo o sinal de dólar no teclado enquanto na janela de expressão. Um dos parâmetros disponíveis é a nossa propriedade de video1bitrate que publicámos anteriormente.

![Aceder a parâmetros dentro de uma expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Aceder a parâmetros dentro de uma expressão*

Faça o mesmo para a saída do ficheiro para o nosso segundo vídeo:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4`

e para a saída de ficheiro apenas áudio:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4`

Se agora alterarmos o bitrate para qualquer um dos ficheiros de vídeo ou áudio, o respetivo codificadora será reconfigurado e a convenção de nome de ficheiro baseada em bitrate será honrada todas automaticamente.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Adicionar miniaturas à saída MP4 multibitrária
Partindo de um fluxo de trabalho que gera [uma saída MP4 multibitrária a partir de uma entrada MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)vamos agora procurar adicionar miniaturas à saída.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para adicionar miniaturas a
![Fluxo de trabalho multibitrático MP4 para começar a partir de](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Fluxo de trabalho multibitrático MP4 para começar a partir de*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Adicionar a codificação JPG
O coração da nossa geração de miniaturas será o componente JPG Encoder, capaz de obter ficheiros JPG.

![Codificação JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codificação JPG*

No entanto, não podemos ligar diretamente o nosso fluxo de vídeo não comprimido da entrada de ficheiros de mídia ao codificador JPG. Em vez disso, espera receber quadros individuais. Isto, podemos fazer através do componente do Portão de Quadro de Vídeo.

![Ligação de um portão de moldura ao codificadora JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ligação de um portão de moldura ao codificadora JPG*

O portão de moldura uma vez a cada tantos segundos ou quadros permite que uma moldura de vídeo passe. O intervalo e o tempo com que isto acontece são configuráveis nas propriedades.

![Propriedades do Portão de Quadro de Vídeo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriedades do Portão de Quadro de Vídeo*

Vamos criar uma miniatura a cada minuto definindo o modo para Tempo (segundos) e o Intervalo a 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Lidar com a conversão do Espaço De Cor
Embora pareça lógico que tanto os pinos de vídeo descomprimidos do portão de fotogramas como a entrada de ficheiros de mídia possam agora ser conectados, receberíamos um aviso se o fazéssemos.

![Erro de espaço de cor de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erro de espaço de cor de entrada*

Isto porque a forma como a informação de cor é representada no nosso fluxo de vídeo cru e não comprimido original, vindo do nosso MXF, é diferente do que o JPG Encoder espera. Mais especificamente, espera-se que um chamado "espaço de cores" de "RGB" ou "Escala de Cinza" flua. Isto significa que o fluxo de vídeo do Video Frame Gate precisa de ter uma conversão aplicada primeiro em relação ao seu espaço de cores.

Arraste para o fluxo de trabalho o Color Space Converter - Intel e conecte-o ao nosso portão de moldura.

![Ligação de um conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Ligação de um conversor de espaço de cor*

Na janela de propriedades, escolha a entrada BGR 24 da lista Predefinida.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Escrever as miniaturas
Diferente dos nossos vídeos MP4, o componente JPG Encoder produz mais do que um ficheiro. Para lidar com isto, pode ser utilizado um componente de Writer de Ficheiro JPG de Pesquisa de Cena: pega nas miniaturas jpg recebidas e escreve-as, sendo cada nome de ficheiro sufixado por um número diferente. (O número normalmente indica o número de segundos/unidades no fluxo de onde a miniatura foi extraída.)

![Apresentando o escritor de arquivos JPG de pesquisa de cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Apresentando o escritor de arquivos JPG de pesquisa de cena*

Configure a propriedade Do Caminho da Pasta de Saída com a expressão: `${ROOT_outputWriteDirectory}`

e a propriedade Filename Prefix com:

`${ROOT_sourceFileBaseName}_thumb_`

O prefixo determina como os ficheiros das miniaturas estão a ser nomeados. Estão sufixados com um número que indica a posição do polegar no fluxo.

![Pesquisa de cena JPG File Writer propriedades](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Pesquisa de cena JPG File Writer propriedades*

Ligue o escritor de ficheiros JPG de pesquisa de cena ao nó de ficheiro de saída/ativo.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Deteção de erros num fluxo de trabalho
Ligue a entrada do conversor de espaço de cor à saída de vídeo cru e descomprimido. Agora faça um teste local para o fluxo de trabalho. Há uma boa hipótese de o fluxo de trabalho parar de executar e indicar com um contorno vermelho no componente que encontrou um erro:

![Erro do Conversor de Espaço De Cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erro do Conversor de Espaço De Cor*

Clique no pequeno ícone vermelho "E" no canto superior direito do componente Color Space Converter para ver qual é a razão pela qual a tentativa de codificação falhou.

![Diálogo de erro do conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Diálogo de erro do conversor de espaço de cor*

Acontece, como pode ver, que o padrão de espaço de cores para o conversor de espaço de cores tem de ser rec601 para a nossa conversão solicitada de YUV para RGB. Aparentemente, o nosso fluxo não indica o seu rec601. (Rec 601 é um padrão para codificar sinais de vídeo analógicos entrelaçados em formato de vídeo digital. Especifica uma região ativa que abrange 720 amostras de luminância e 360 amostras de crominagem por linha. O sistema de codificação de cores é conhecido como YCbCr 4:2:2.)

Para corrigir isto, vamos indicar nos metadados do nosso fluxo que estamos a lidar com o conteúdo rec601. Para tal, usaremos um componente de Atualização do Tipo de Dados de Vídeo, que colocaremos entre a nossa fonte bruta e o componente de conversão do espaço de cores. Este atualizador de tipo de dados permite a atualização manual de determinadas propriedades do tipo de dados de vídeo. Configuure-o para indicar um Padrão de Espaço Colorido de "Rec 601". Isto faz com que o Atualizador do Tipo de Dados de Vídeo marque o stream com o espaço de cores "Rec 601" se ainda não houver espaço de cor definido. (Não substituirá quaisquer metadados existentes, a menos que a caixa de verificação do Override tenha sido verificada.)

![Atualizar a norma do espaço de cor no atualização do tipo de dados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Atualizar a norma do espaço de cor no atualização do tipo de dados*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Fluxo de trabalho acabado
Agora que o nosso fluxo de trabalho está terminado, faça outro teste para vê-lo passar.

![Fluxo de trabalho acabado para saída multi-mp4 com miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fluxo de trabalho acabado para saída multi-mp4 com miniaturas*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Aparar tempo de saída mp4 multibitrária
Partindo de um fluxo de trabalho que gera [uma saída MP4 multibitrária a partir de uma entrada MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)vamos agora analisar o vídeo de origem com base em selos temporais.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Visão geral do fluxo de trabalho para começar a adicionar aparas a
![Início do fluxo de trabalho para adicionar aparas a](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Início do fluxo de trabalho para adicionar aparas a*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Utilizando o aparador de fluxo
O componente de aparador de fluxo permite-lhe aparar o início e o fim de uma base de fluxo de entrada na informação de tempo (segundos, minutos, ...). O aparador não suporta aparas baseadas em quadros.

![Aparador de fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Aparador de fluxo*

Em vez de ligar os codificadores AVC e o designador de posição de altifalantes diretamente à Entrada de Ficheiros de Mídia, colocaremos entre eles o aparador de fluxo. (Um para o sinal de vídeo e outro para o sinal de áudio intercalado.)

![Coloque o aparador de fluxo no meio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Coloque o aparador de fluxo no meio*

Vamos configurar o aparador para que só processemos vídeo e áudio entre 15 segundos e 60 segundos no vídeo.

Aceda às propriedades do aparador de fluxo de vídeo e configuure as propriedades de Hora de Início (15 s) e Fim (60 s). Para garantir que tanto o nosso aparador de áudio como de vídeo estão sempre configurados para os mesmos valores de início e fim, publicamos os que estão na raiz do fluxo de trabalho.

![Publicar propriedade de tempo de início a partir de Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicar propriedade de tempo de início a partir de Stream Trimmer*

![Publique o diálogo de propriedade para a hora de início](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publique o diálogo de propriedade para a hora de início*

![Publique o diálogo de propriedade para o fim do tempo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publique o diálogo de propriedade para o fim do tempo*

Se agora inspecionarmos a raiz do nosso fluxo de trabalho, ambas as propriedades são cuidadosamente exibidas e configuráveis a partir daí.

![Propriedades publicadas disponíveis na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Propriedades publicadas disponíveis na raiz*

Agora abra as propriedades de aparar a partir do aparador de áudio e configuure os tempos de início e de fim com uma expressão que se refira às propriedades publicadas na raiz do nosso fluxo de trabalho.

Para a hora de início do corte de áudio:

`${ROOT_TrimmingStartTime}`

e para o seu fim de tempo:

`${ROOT_TrimmingEndTime}`

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Fluxo de trabalho acabado
![Fluxo de trabalho acabado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fluxo de trabalho acabado*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>Introduzindo o Componente Scripted
Os Componentes Scripted podem executar scripts arbitrários durante as fases de execução do nosso fluxo de trabalho. Existem quatro scripts diferentes que podem ser executados, cada um com características específicas, e o seu próprio lugar no ciclo de vida do fluxo de trabalho:

* **comandoSDescão**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

A documentação do Componente Scripted vai mais detalhadamente para cada um dos acima. Na [secção seguinte,](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)o componente de **scripts realizeScript** é utilizado para construir um xml de cliplist na mosca quando o fluxo de trabalho começar. Este script é chamado durante a configuração do componente, o que acontece apenas uma vez no seu ciclo de vida.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Scripting dentro de um fluxo de trabalho: olá mundo
Arraste um componente scripted para a superfície do designer e rebatize-o (por exemplo, "SetClipListXML").

![Adicionar um componente scripted](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente scripted*

Quando inspecionar as propriedades do Componente Scripted, serão mostrados os quatro tipos de scripts diferentes, cada um configurável a um script diferente.

![Propriedades do Componente Scripted](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do Componente Scripted*

Limpe o processoInputScript e abra o editor para o realizeScript. Agora estamos prontos para começar a escrever.

Scripts são escritos em Groovy, uma linguagem de scripts compilada dinamicamente para a plataforma Java que mantém a compatibilidade com Java. Na verdade, a maioria do código Java é código Groovy válido.

Vamos escrever um simples script de olá mundo groovy no contexto do nosso realizeScript. Insira o seguinte no editor:

`node.log("hello world");`

Agora execute um teste local. Após esta execução, inspecione (através do separador Sistema no Componente Scripted) a propriedade Logs.

![Olá produção de log mundial](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Olá produção de log mundial*

O objeto de nó que chamamos o método de registo, refere-se ao nosso "nó" atual ou ao componente que estamos a escrever dentro. Cada componente como tal tem a capacidade de obter dados de registo de resultados, disponíveis através do separador do sistema. Neste caso, nós dermos a corda literal "olá mundo". Importante entender aqui é que esta pode revelar-se uma ferramenta de depuragem inestimável, fornecendo-lhe uma visão sobre o que o script está realmente fazendo.

A partir do nosso ambiente de scripts, também temos acesso a propriedades em outros componentes. Experimente o seguinte:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

A nossa janela de registo mostra-nos o seguinte:

![Início de saída para aceder a caminhos de nó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Início de saída para aceder a caminhos de nó*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Corte baseado em quadros de saída mp4 multibitrária
Partindo de um fluxo de trabalho que gera [uma saída MP4 multibitrária a partir de uma entrada MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)vamos agora analisar o aparar o vídeo de origem com base nas contagens de quadros.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Visão geral da planta para começar a adicionar aparas para
![Fluxo de trabalho para começar a adicionar aparas para](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Fluxo de trabalho para começar a adicionar aparas para*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>Utilização da Lista de Clip XML
Em todos os tutoriais de fluxo de trabalho anteriores, utilizamos o componente de Entrada de Ficheiros de Mídia como fonte de entrada de vídeo. Para este cenário específico, no entanto, vamos usar o componente De Origem da Lista de Clip. Esta não deve ser a forma preferida de trabalhar; utilize apenas a Origem da Lista de Clipes quando houver uma razão real para o fazer (como no caso seguinte, onde estamos a utilizar as capacidades de aparar da lista de clipes).

Para mudar da nossa entrada de ficheiro de mídia para a Origem da Lista de Clipe, arraste o componente De Origem da Lista de Clipe para a superfície de conceção e ligue o pino XML da Lista de Clip e ao nó XML da lista de clipe do designer de fluxo de trabalho. Isto povoa a Origem da Lista de Clip com pinos de saída, de acordo com o nosso vídeo de entrada. Agora ligue os pinos de vídeo descomprimido e áudio não comprimido da Origem da Lista de Clips aos respetivos Codificadores AVC e Audio Stream Interleaver. Agora remova a entrada de ficheiros de mídia.

![Substituiu a entrada de ficheiro de mídia pela Origem da Lista de Clipe](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Substituiu a entrada de ficheiro de mídia pela Origem da Lista de Clipe*

O componente De Origem da Lista de Clipes toma como entrada uma "Lista de Clip XML". Ao selecionar o ficheiro de origem para testar localmente, esta lista de clipes xml é auto-povoada para si.

![Propriedade XML da Lista de Clipes auto-povoada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriedade XML da Lista de Clipes auto-povoada*

Olhando um pouco mais perto do xml, é assim que se parece:

![Editar o diálogo da lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Editar o diálogo da lista de clipes*

Isto, no entanto, não reflete as capacidades da lista de clipes xml. Uma opção que temos é adicionar um elemento "Trim" sob a fonte de vídeo e áudio, como este:

![Adicionando um elemento de corte à lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Adicionando um elemento de corte à lista de clipes*

Se modificar a lista de clipes xml desta forma acima e realizar um teste local, verá o vídeo corretamente aparado entre 10 e 20 segundos no vídeo.

No entanto, ao contrário do que acontece quando se faz uma corrida local, este mesmo xml de cliplist não teria o mesmo efeito quando aplicado num fluxo de trabalho que funciona nos Serviços de Media Azure. Quando o Azure Premium Encoder começa, o cliplist xml é gerado de novo, com base no ficheiro de entrada que o trabalho de codificação foi dado. Isto significa que quaisquer alterações que fizermos no xml seriam, infelizmente, ultrapassadas.

Para contrariar o cliplist xml sendo limpo quando um trabalho de codificação é iniciado, podemos regenerar em movimento logo após o início do nosso fluxo de trabalho. Tais ações personalizadas podem ser tomadas através do que é chamado de "Componente Scripted". Para obter mais informações, consulte [a introdução do componente scripted](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Arraste um componente scripted para a superfície do designer e rebatize-o para "SetClipListXML".

![Adicionar um componente scripted](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente scripted*

Quando inspeciona as propriedades do Componente Scripted, são apresentados os quatro tipos de scripts diferentes, cada um configurável a um script diferente.

![Propriedades do Componente Scripted](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do Componente Scripted*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>Modificação da lista de clipes de um componente scripted
Antes de podermos reescrever o xml da cliplist que é gerado durante o arranque do workflow, teremos de ter acesso à propriedade e conteúdo xml da cliplist. Podemos fazê-lo assim:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Lista de clipes de entrada a ser registada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clipes de entrada a ser registada*

Primeiro, precisamos de uma maneira de determinar até que ponto queremos aparar o vídeo. Para tornar isto conveniente para o utilizador menos técnico do fluxo de trabalho, publique duas propriedades na raiz do gráfico. Para isso, clique com o botão direito na superfície do designer e selecione "Adicionar Propriedade":

* Primeira propriedade: "ClippingTimeStart" do tipo: "TIMECODE"
* Segunda propriedade: "ClippingTimeEnd" do tipo: "TIMECODE"

![Adicione o diálogo de propriedade para a hora de início do recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adicione o diálogo de propriedade para a hora de início do recorte*

![Adereços de tempo de clipping publicados na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Adereços de tempo de clipping publicados na raiz do fluxo de trabalho*

Configure ambas as propriedades a um valor adequado:

![Configure as propriedades de arranque e fim de clipping](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configure as propriedades de arranque e fim de clipping*

Agora, de dentro do nosso script, podemos aceder a ambas as propriedades, assim:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Janela de log mostrando início e fim do recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Janela de log mostrando início e fim do recorte*

Vamos analisar as cordas do código de tempo num formulário mais conveniente para usar, usando uma expressão regular simples:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Janela de log com saída de código de tempo parsed](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Janela de log com saída de código de tempo parsed*

Com esta informação em mãos, podemos agora modificar o xml da cliplist para refletir os tempos de início e fim para o recorte de moldura pretendido do filme.

![Código de script para adicionar elementos de corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código de script para adicionar elementos de corte*

Isto foi feito através de operações normais de manipulação de cordas. A lista de clipe modificada resultante xml é escrita de volta para a propriedade clipListXML na raiz do fluxo de trabalho através do método "setProperty". A janela de registo após outro teste mostrar-nos-ia o seguinte:

![Registar a lista de clipes resultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registar a lista de clipes resultante*

Faça um teste para ver como os streams de vídeo e áudio foram cortados. Como fará mais do que um teste com valores diferentes para os pontos de corte, vai notar que esses não serão levados em conta no entanto! A razão para isso é que o designer, ao contrário do tempo de execução do Azure, NÃO substitui a lista de cliplist xml a cada corrida. Isto significa que só a primeira vez que definir os pontos de entrada e saída, fará com que o xml transforme, todas as outras vezes, a nossa cláusula de guarda `clipListXML.indexOf("<trim>") == -1` (se) impedirá o fluxo de trabalho de adicionar outro elemento de corte quando já há um presente.

Para tornar o nosso fluxo de trabalho conveniente para testar localmente, é melhor adicionarmos algum código de manutenção da casa que inspecione se um elemento de corte já estava presente. Em caso afirmativo, podemos removê-lo antes de continuar modificando o xml com os novos valores. Em vez de usar manipulações simples de cordas, é provavelmente mais seguro fazê-lo através de análise de modelo de objetos xml reais.

Antes de podermos adicionar este código, teremos de adicionar uma série de declarações de importação no início do nosso script primeiro:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Depois disso, podemos adicionar o código de limpeza necessário:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Este código vai logo acima do ponto em que adicionamos os elementos de corte ao xml cliplist.

Neste momento, podemos correr e modificar o nosso fluxo de trabalho o quanto quisermos, tendo as alterações aplicadas sempre.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Adicionar uma propriedade de conveniência ClippingEnabled
Como pode nem sempre querer que o corte aconteça, vamos terminar o nosso fluxo de trabalho adicionando uma conveniente bandeira booleana que indica se queremos ou não permitir aparar/cortar.

Como antes, publique uma nova propriedade na raiz do nosso fluxo de trabalho chamado "ClippingEnabled" do tipo "BOOLEAN".

![Publicado uma propriedade para permitir o clipping](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicado uma propriedade para permitir o clipping*

Com a cláusula de guarda abaixo simples, podemos verificar se é necessário aparar e decidir se a nossa lista de clipes como tal precisa de ser modificada ou não.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a name="complete-code"></a><a id="code"></a>Código completo

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Também ver
[Introdução da Codificação Premium nos Serviços Azure Media](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como utilizar a codificação premium nos serviços de mídia Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificação de conteúdo sonoro com serviço de mídia Azure](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formatos e Codecs de Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-premium-workflow-encoder-formats.md)

[Ficheiros de fluxo de trabalho de amostra](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
