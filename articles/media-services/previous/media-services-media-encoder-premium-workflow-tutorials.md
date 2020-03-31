---
title: Advanced Media Encoder Premium Workflow tutorials (Tutoriais avançados do Media Encoder Premium Workflow)
description: Este documento contém walkthroughs que mostram como executar tarefas avançadas com media Encoder Premium Workflow e também como criar fluxos de trabalho complexos com o Workflow Designer.
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
ms.openlocfilehash: 1ab70d56bd3def58d0e814035070cf027a88cd3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251014"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Advanced Media Encoder Premium Workflow tutorials (Tutoriais avançados do Media Encoder Premium Workflow)
## <a name="overview"></a>Descrição geral
Este documento contém walkthroughs que mostram como personalizar fluxos de trabalho com **Workflow Designer**. Pode encontrar os ficheiros de fluxo de trabalho [aqui.](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)  

## <a name="toc"></a>TOC
Os seguintes tópicos são abordados:

* [Codificar mXF em um único BITRATE MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Iniciar um novo fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Utilização da entrada de ficheiros de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspecionando os fluxos de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Adicionar um codificador de vídeo para . Geração de ficheiros MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Codificar o fluxo de áudio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexing Audio e Vídeo streams em um recipiente MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Escrever o ficheiro MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Criação de um Ativo de Serviços de Media a partir do ficheiro de saída](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Teste o fluxo de trabalho acabado localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Codificar mXF em MP4s multibitrate - embalagem dinâmica ativada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Adicionar uma ou mais saídas de MP4 adicionais](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Configurar os nomes de saída do ficheiro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Adicionar uma faixa de áudio separada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Adicionar o ficheiro SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Codificar mXF em MP4 multibitrate - planta melhorada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Visão geral do fluxo de trabalho para melhorar
  * [Convenções de nomeação de ficheiros](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Propriedades dos componentes de publicação na raiz do fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Ter gerado nomes de ficheiros de saída dependem de valores de propriedade publicados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Adicionar miniaturas à saída mp4 multibitrate](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Visão geral do fluxo de trabalho para adicionar miniaturas a
  * [Adicionar codificação JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Lidar com a conversão do espaço de cor](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Escrevendo as miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Detetar erros num fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Fluxo de Trabalho Acabado](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Aparação baseada no tempo da saída de MP4 multibitrate](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Visão geral do fluxo de trabalho para começar a adicionar aparas a](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Usando o aparador de corrente](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Fluxo de Trabalho Acabado](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introdução do Componente Scripted](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Scripting dentro de um fluxo de trabalho: olá mundo](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Aparação baseada em quadros da saída mp4 multibitrate](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Visão geral da planta para começar a adicionar aparas a](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Usando a Lista de Clipe XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modificação da lista de clipes de um Componente Scripted](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Adicionar uma propriedade de conveniência ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Codificar mXF em um único BITRATE MP4
Esta secção demonstra como criar uma única bitrate . Ficheiro MP4 com áudio codificado AAC-HE a partir de um . Ficheiro de entrada MXF.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Iniciar um novo fluxo de trabalho
Open Workflow Designer e selecione File > New Workspace > Transcode Blueprint

O novo fluxo de trabalho mostra três elementos:

* Arquivo fonte primário
* Lista de Clipes XML
* Ficheiro/Ativo de Saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Novo fluxo de trabalho de codificação*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>Utilização da entrada de ficheiros de mídia
Para aceitar o ficheiro de entrada, começa-se a adicionar um componente de entrada de ficheiros de mídia. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa repositório e arraste a entrada desejada no painel do designer. Repita a ação para a entrada de ficheiros de mídia e ligue o componente de ficheiro fonte primário ao pino de entrada do Ficheiro da entrada do Ficheiro de Media.

![Entrada de ficheiros de mídia conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada de ficheiros de mídia conectado*

Inicialmente, identifique um ficheiro de amostra adequado para utilizar ao conceber um fluxo de trabalho personalizado. Para isso, clique no fundo do painel do designer e procure a propriedade Primary Source File no painel de propriedade da mão direita. Clique no ícone da pasta e selecione o ficheiro desejado para testar o fluxo de trabalho. O componente de entrada de ficheiros de mídia inspeciona o ficheiro e povoa os seus pinos de saída para refletir os detalhes do ficheiro de amostra que inspecionou.

![Entrada de ficheiros de mídia povoado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada de ficheiros de mídia povoado*

Agora que a entrada é povoada, o próximo passo é configurar as definições de codificação de saída. Semelhante à configuração do Ficheiro Fonte Primária, configurar agora a propriedade Variável da Pasta de Saída, logo abaixo.

![Propriedades de entrada e saída configuradas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriedades de entrada e saída configuradas*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Inspecionando os fluxos de mídia
Muitas vezes é desejado saber como o fluxo se parece à medida que flui através do fluxo de trabalho. Para inspecionar um fluxo em qualquer ponto do fluxo de trabalho, basta clicar num pino de saída ou de entrada em qualquer um dos componentes. Neste caso, tente clicar no pino de saída de vídeo não comprimido a partir da entrada de ficheiros de mídia. Abre-se um diálogo que permite inspecionar o vídeo de saída.

![Inspecionando o pino de saída de vídeo não comprimido](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspecionando o pino de saída de vídeo não comprimido*

Neste caso, mostra que o vídeo contém uma entrada de 1920x1080 a 24 fotogramas por segundo em 4:2:2 amostragem para um vídeo de quase 2 minutos.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Adicionar um codificador de vídeo para . Geração de ficheiros MP4
Agora, um Vídeo Não Comprimido e vários pinos de saída de áudio não comprimidos estão disponíveis para utilização na entrada de ficheiros de mídia. Para codificar o vídeo de entrada, é necessário adicionar um componente de codificação ao fluxo de trabalho - neste caso, para gerar . Ficheiros MP4.

Para codificar o fluxo de vídeo para H.264, adicione o componente AVC Video Encoder à superfície do designer. Este componente pega num fluxo de vídeo descompressivo como entrada e fornece um fluxo de vídeo comprimido AVC no seu pino de saída.

![Codificador AVC não ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codificador AVC não ligado*

As suas propriedades determinam como a codificação acontece exatamente. Vamos ver algumas das definições mais importantes:

* Largura de saída e altura de saída: determina a resolução do vídeo codificado. Neste caso, 640x360 é um bom cenário.
* Taxa de fotogramas: quando definida para passar, apenas adotará a taxa de fotogramas de origem, é possível anular isso. Esta conversão de framerate não é compensada pelo movimento.
* Perfil e Nível: determina o perfil e o nível aavc. Para obter convenientemente mais informações sobre os diferentes níveis e perfis, clique no ícone do ponto de interrogação no componente AVC Video Encoder e a página de ajuda mostrará mais detalhes sobre cada um dos níveis. Para este exemplo, utilize o Perfil Principal no nível 3.2 (o padrão).
* Modo de Controlo de Tarifas e Bitrate (kbps): neste cenário, opte por uma produção de bitrate constante (CBR) a 1200 kbps
* Formato de vídeo: fornece informações sobre o VUI (Video Usability Information) que é escrita no fluxo H.264 (informação lateral que pode ser usada por um descodificador para melhorar o ecrã, mas não essencial para descodificar corretamente):
* NTSC (típico dos EUA ou do Japão, utilizando 30 fps)
* PAL (típico para a Europa, usando 25 fps)
* Modo de tamanho GOP: definir tamanho de GOP fixo para os nossos propósitos com um intervalo de 2 segundos com GOPs fechados. A regulação de 2 segundos garante compatibilidade com a embalagem dinâmica que a Azure Media Services fornece.

Para alimentar o codificador AVC, ligue o pino de saída de vídeo não comprimido do componente de entrada de ficheiros de mídia ao pino de entrada de vídeo não comprimido do codificador AVC.

![Codificador AVC conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Codificador principal AVC ligado*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Codificar o fluxo de áudio
Neste ponto, o fluxo de áudio original não comprimido ainda precisa de ser comprimido. Para compressão da corrente de áudio, adicione um componente AAC Encoder (Dolby) ao fluxo de trabalho.

![Codificador AVC não ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificador AAC não ligado*

Agora há uma incompatibilidade: há apenas um único pino de entrada de áudio não comprimido do Codificador AAC, enquanto mais do que provável que a Entrada de Ficheiros de Mídia tenha duas diferentes correntes de áudio não comprimidos disponíveis: uma para o canal de áudio esquerdo e outra para a direita. (Se estáa lidar com som surround, são seis canais.) Portanto, não é possível ligar diretamente o áudio da fonte de entrada de ficheiros de mídia ao codificador de áudio AAC. O componente AAC espera um fluxo de áudio chamado "interleaved": um único fluxo que tem os canais esquerdo e direito interpartidos entre si. Assim que soubermos pelo nosso ficheiro de mídia de origem que as faixas de áudio estão em que posição na fonte, podemos gerar tal fluxo de áudio interleaved com as posições de altifalante saqueadas corretamente para esquerda e direita.

Primeiro, pretende-se gerar um fluxo inter-leaved a partir dos canais de áudio de origem necessários. O componente Interleaver Audio Stream trata-o para nós. Adicione-o ao fluxo de trabalho e ligue as saídas de áudio da entrada de ficheiros de mídia.

![Interleaver de fluxo de áudio conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Interleaver de fluxo de áudio conectado*

Agora que temos um fluxo de áudio interleaved, ainda não especificamos onde atribuir as posições do altifalante esquerdo ou direito. Para especificar isto, podemos alavancar o Designador de Posição do Orador.

![Adicionar um designador de posição de altifalante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Adicionar um designador de posição de altifalante*

Configure o Designador de Posição do Altifalante para utilização com um fluxo de entrada estéreo através de um filtro predefinido encoder de "Custom" e do Preset do Canal chamado "2.0 (L,R)". (Isto atribui a posição do altifalante esquerdo ao canal 1 e à posição do altifalante direito para o canal 2.)

Ligue a saída do Designador de Posição do Altifalante à entrada do Codificador AAC. Em seguida, diga ao AAC Encoder para trabalhar com um Preset canal "2.0 (L,R)," para que saiba lidar com o áudio estéreo como entrada.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexing Audio e Vídeo streams em um recipiente MP4
Dado o nosso fluxo de vídeo codificado AVC e o nosso fluxo de áudio codificado AAC, podemos capturar ambos num . Contentor MP4. O processo de mistura de diferentes fluxos num único é chamado de "multiplexing" (ou "muxing"). Neste caso, estamos interinando o áudio e os streams de vídeo num único coerente. Pacote MP4. O componente que coordena isto para um . O recipiente MP4 é chamado de ISO MPEG-4 Multiplexer. Adicione um à superfície do designer e ligue tanto o AVC Video Encoder como o AAC Encoder às suas inputs.

![MPEG4 Multiplexer conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*MPEG4 Multiplexer conectado*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Escrever o ficheiro MP4
Ao escrever um ficheiro de saída, o componente de saída de ficheiros é utilizado. Podemos ligá-lo à saída do Multiplexer ISO MPEG-4 para que a sua saída seja escrita em disco. Para tal, ligue o pino de saída do recipiente (MPEG-4) ao pino de entrada Write da saída de ficheiros.

![Saída de ficheiroconectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Saída de ficheiroconectado*

O nome de ficheiro que é usado é determinado pela propriedade File. Embora essa propriedade possa ser codificada a um determinado valor, muito provavelmente se quer defini-la através de uma expressão em vez disso.

Para que o fluxo de trabalho determine automaticamente a propriedade do nome do ficheiro de saída a partir de uma expressão, clique no botão ao lado do nome Ficheiro (ao lado do ícone da pasta). A partir do menu suspenso, então selecione "Expression". Isto traz ao editor de expressão. Limpe primeiro o conteúdo do editor.

![Editor de Expressão Vazia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de Expressão Vazia*

O editor de expressão permite-lhe introduzir qualquer valor literal, misturado com uma, ou mais variáveis. As variáveis começam com um sinal de dólar. Ao acertar na chave $, o editor mostra uma caixa de entrega com uma escolha de variáveis disponíveis. No nosso caso, usaremos uma combinação da variável de diretório de saída e a variável de nome de ficheiro de entrada base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Editor de Expressão Preenchido](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Editor de Expressão Preenchido*

> [!NOTE]
> Para ver um ficheiro de saída do seu trabalho de codificação em Azure, deve fornecer um valor no editor de expressão.
>
>

Quando confirma a expressão ao bater ok, a janela da propriedade antevê o valor que a propriedade do Arquivo resolve neste momento.

![Expressão de ficheiro resolve dir de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Expressão de ficheiro resolve dir de saída*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Criação de um Ativo de Serviços de Media a partir do ficheiro de saída
Embora tenhamos escrito um ficheiro de saída MP4, ainda precisamos de indicar que este ficheiro pertence ao ativo de saída que os serviços de media geram como resultado da execução deste fluxo de trabalho. Para o efeito, é utilizado o nó de ficheiro/ativo de saída na tela de fluxo de trabalho. Todos os ficheiros que entram neste nó fazem parte do ativo resultante da Azure Media Services.

Ligue o componente de saída de ficheiros ao componente 'Ficheiro/Activo' de saída para terminar o fluxo de trabalho.

![Fluxo de Trabalho Acabado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fluxo de Trabalho Acabado*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>Teste o fluxo de trabalho acabado localmente
Para testar o fluxo de trabalho localmente, aperte o botão de reprodução na barra de ferramentas na parte superior. Quando o fluxo de trabalho terminar a execução, inspecione a saída gerada na pasta de saída configurada. Verá o ficheiro de saída MP4 acabado que foi codificado a partir do ficheiro fonte de entrada MXF.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Codificar mXF em MP4 - embalagem dinâmica multibitrate ativada
Este walkthrough cria um conjunto de ficheiros MP4 bitrate múltiplos com áudio codificado AAC a partir de um único . Ficheiro de entrada MXF.

Quando uma saída de ativos multibitáveis é desejada para utilização em combinação com as funcionalidades de Embalagem Dinâmica oferecidas pela Azure Media Services, vários ficheiros MP4 alinhados com GOP de cada um bitrate diferente e resolução terão de ser gerados. Para tal, o [MXF codificador numa única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) passagem de MP4 bitrate proporciona-nos um bom ponto de partida.

![Fluxo de Trabalho Inicial](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Fluxo de Trabalho Inicial*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Adicionar uma ou mais saídas de MP4 adicionais
Cada ficheiro MP4 do nosso ativo resultante da Azure Media Services suporta uma bitrate e resolução diferentes. Vamos adicionar um ou mais ficheiros de saída MP4 ao fluxo de trabalho.

Para garantir que todos os nossos codificadores de vídeo criados com as mesmas configurações, é mais conveniente duplicar o já existente AVC Video Encoder e configurar outra combinação de resolução e bitrate (vamos adicionar um de 960 x 540 a 25 fotogramas por segundo a 2,5 Mbps ). Para duplicar o codificador existente, copie-o na superfície do designer.

Ligue o pino de saída de vídeo não comprimido da entrada de ficheiros de mídia no nosso novo componente AVC.

![Segundo Codificador AVC ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Segundo Codificador AVC ligado*

Agora adapte a configuração para o nosso novo codificador AVC para a saída 960x540 a 2,5 Mbps. (Utilize as suas propriedades "Largura de saída", "Altura de saída" e "Bitrate (kbps)" para isto.)

Dado que queremos utilizar o ativo resultante juntamente com a embalagem dinâmica da Azure Media Services, o ponto final de streaming precisa de ser capaz de gerar a partir destes ficheiros MP4 HLS/Fragmentados fragmentos MP4/DASH que estão exatamente alinhados entre si de forma a que os clientes que estão a alternar entre bitrates diferentes obtêm uma única experiência contínua de vídeo e áudio. Para que isso aconteça, temos de garantir que, nas propriedades de ambos os AVC codificar o tamanho gop ("grupo de imagens") para ambos os ficheiros MP4 está definido para 2 segundos, o que pode ser feito por:

* definindo o modo de tamanho GOP para o tamanho gop fixo e
* o intervalo do quadro de chaves para dois segundos.
* também definir o controlo gop IDR para gop fechado para garantir que todos os GOPs estão por conta própria sem dependências

Para facilitar a compreensão deste fluxo de trabalho, mude o nome do primeiro codificador AVC para "AVC Video Encoder 640x360 1200 kbps" e o segundo codificador AVC "AVC Video Encoder 960x540 2500 kbps".

Adicione agora um segundo ISO MPEG-4 Multiplexer e uma segunda saída de ficheiro. Ligue o multiplexer ao novo codificador AVC e certifique-se de que a sua saída é direcionada para a Saída de Ficheiros. Em seguida, ligue também a saída do codificador de áudio AAC à entrada do novo multiplexer. A saída de ficheiros, por sua vez, pode ser ligada ao nó de Ficheiro/Ativo de Saída para adicioná-lo ao Ativo de Serviços de Media que será criado.

![Segunda saída de Muxer e Arquivo ligada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Segunda saída de Muxer e Arquivo ligada*

Para compatibilidade com a embalagem dinâmica da Azure Media Services, configure o Modo Chunk do multiplexer para contagem ou duração gop e coloque os GOPs por pedaço para 1. (Este deve ser o padrão.)

![Modos muxer chunk](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos muxer chunk*

Nota: pode querer repetir este processo para quaisquer outras combinações de bitrate e resolução que pretenda adicionar à saída do ativo.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurar os nomes de saída do ficheiro
Temos mais de um único ficheiro adicionado ao ativo de saída. Isto fornece a necessidade de garantir que os nomes de ficheiros de cada um dos ficheiros de saída são diferentes uns dos outros e talvez até aplicar uma convenção de nomeação de ficheiros para que fique claro a partir do nome do ficheiro com o que está a lidar.

O nome da saída de ficheiros pode ser controlado através de expressões no designer. Abra o painel de propriedades para um dos componentes de Saída de Ficheiros e abra o editor de expressão para a propriedade File. O nosso primeiro ficheiro de saída foi configurado através da seguinte expressão (ver o tutorial para ir de [MXF para uma única saída de MP4 bitrate):](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Isto significa que o nosso nome de ficheiro é determinado por duas variáveis: o diretório de saída para escrever e o nome base de ficheiros de origem. A primeira é exposta como uma propriedade na raiz do fluxo de trabalho e esta última é determinada pelo ficheiro de entrada. O diretório de saída é o que se usa para testes locais; esta propriedade será ultrapassada pelo motor de fluxo de trabalho quando o fluxo de trabalho for executado pelo processador de mídia baseado na nuvem na Azure Media Services.
Para dar a ambos os nossos ficheiros de saída um nome de saída consistente, altere a expressão de nomeação do primeiro ficheiro para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e o segundo para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Execute um ensaio intermédio para se certificar de que ambos os ficheiros de saída MP4 são corretamente gerados.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Adicionar uma faixa de áudio separada
Como veremos mais tarde quando gerarmos um ficheiro .ism para acompanhar os nossos ficheiros de saída MP4, também exigiremos um ficheiro MP4 apenas áudio como a faixa de áudio para o nosso streaming adaptativo. Para criar este ficheiro, adicione um muxer adicional ao fluxo de trabalho (ISO-MPEG-4 Multiplexer) e ligue o pino de saída do codificador AAC com o pino de entrada para a Faixa 1.

![Muxer áudio adicionado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Muxer áudio adicionado*

Crie um terceiro componente de saída de ficheiros para obter o fluxo de saída a partir do muxer e configure a expressão de nomeação do ficheiro como:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Muxer áudio criando saída de ficheiro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Muxer áudio criando saída de ficheiro*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Adicionando o . Ficheiro ISM SMIL
Para que a embalagem dinâmica funcione em combinação com ambos os ficheiros MP4 (e o MP4 apenas áudio) no nosso ativo de Media Services, precisamos também de um ficheiro manifesto (também chamado de ficheiro "SMIL": Linguagem de Integração Multimédia Sincronizada). Este ficheiro indica à Azure Media Services quais os ficheiros MP4 disponíveis para embalagem dinâmica e quais os que devem considerar para o streaming de áudio. Um arquivo manifesto típico para um conjunto de MP4 com um único fluxo de áudio parece este:

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

O ficheiro .ism contém uma declaração de comutação, uma referência a cada um dos ficheiros de vídeo MP4 individuais e além daqueles que também uma (ou mais) referências de ficheiros áudio a um MP4 que contém apenas o áudio.

Gerar o manifesto arquivo para o nosso conjunto de MP4 pode ser feito através de um componente chamado "AMS Manifest Writer". Para usá-lo, arraste-o para a superfície e ligue os pinos de saída "Write Complete" dos três componentes de saída de ficheiros à entrada do Escritor Manifesto DA AMS. Em seguida, certifique-se de ligar a saída do AmS Manifest Writer ao Ficheiro/Ativo de Saída.

Tal como acontece com os outros componentes de saída de ficheiros, configure o nome de saída do ficheiro .ism com uma expressão:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Nosso fluxo de trabalho acabado parece com o abaixo:

![MXF acabado para fluxo de trabalho MP4 multibitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF acabado para fluxo de trabalho MP4 multibitrate*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>Codificar mXF em MP4 multibitrate - planta melhorada
No anterior fluxo de [trabalho,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) vimos como um único ativo de entrada MXF pode ser convertido num ativo de saída com ficheiros MP4 multibitáveis, um ficheiro MP4 apenas áudio e um ficheiro manifesto para uso em conjunto com a embalagem dinâmica da Azure Media Services.

Este walkthrough mostra como alguns dos aspetos podem ser melhorados e tornados mais convenientes.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para melhorar
![Fluxo de trabalho mp4 multibitrate para melhorar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Fluxo de trabalho mp4 multibitrate para melhorar*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenções de nomeação de ficheiros
No fluxo de trabalho anterior, especificámos uma expressão simples como base para gerar nomes de ficheiros de saída. No entanto, temos alguma duplicação: todos os componentes individuais do ficheiro de saída especificaram tal expressão.

Por exemplo, o nosso componente de saída de ficheiros para o primeiro ficheiro de vídeo está configurado com esta expressão:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Enquanto para o segundo vídeo de saída, temos uma expressão como:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Não seria mais limpo, menos propenso a erros, e mais conveniente se pudéssemos remover alguma desta duplicação e tornar as coisas mais configuráveis em vez disso? Felizmente podemos: as capacidades de expressão do designer em combinação com a capacidade de criar propriedades personalizadas na nossa raiz de fluxo de trabalho fornecerão uma camada adicional de conveniência.

Vamos assumir que vamos conduzir a configuração do nome de ficheiro a partir das bitrates dos ficheiros MP4 individuais. Estes bitrates que pretendemos configurar num local central (na raiz do nosso gráfico), de onde serão acedidos para configurar e impulsionar a geração de nomes de ficheiros. Para isso, começamos por publicar a propriedade bitrate de ambos os codificadores AVC para a raiz do nosso fluxo de trabalho, para que se torne acessível tanto a partir da raiz como dos codificadores AVC. (Mesmo que exibido em dois pontos diferentes, há apenas um valor subjacente.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Propriedades dos componentes de publicação na raiz do fluxo de trabalho
Abra o primeiro codificador AVC, vá para a propriedade Bitrate (kbps) e a partir do dropdown escolha Publicar.

![Publicação da propriedade bitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicação da propriedade bitrate*

Configure o diálogo de publicação para publicar na raiz do nosso gráfico de fluxo de trabalho, com um nome publicado de "video1bitrate" e um nome de exibição legível de "Video 1 Bitrate". Configure um nome de grupo personalizado chamado "Streaming Bitrates" e atinja a Publish.

![Publicação da propriedade bitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Diálogo de publicação para propriedade bitrate*

Repita o mesmo para a propriedade bitrate do segundo codificador AVC e nomeie-o "video2bitrate" com o nome de exibição de "Video 2 Bitrate", no mesmo grupo personalizado "Streaming Bitrates".

Se agora inspecionarmos as propriedades da raiz do fluxo de trabalho, veremos o nosso grupo personalizado com as duas propriedades publicadas aparecerem. Ambos estão a refletir o valor do respetivo bitrate codificador aAVC.

![Adereços bitrate publicados na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Sempre que quisermos aceder a estas propriedades a partir de código ou de expressão, podemos fazê-lo assim:

* a partir do código inline de um componente logo abaixo da raiz: nó.getPropertyAsString('.. /video1bitrate', nulo)
* dentro de uma expressão: ${ROOT_video1bitrate}

Vamos completar o grupo "Streaming Bitrates" publicando também a nossa bitrate de áudio. Dentro das propriedades do Codificador AAC, procure a definição bitrate e selecione Publicar a partir da queda ao lado. Publique na raiz do gráfico com o nome "audio1bitrate" e exiba o nome "Audio 1 Bitrate" dentro do nosso grupo personalizado "Streaming Bitrates".

![Diálogo de publicação para bitrate áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Diálogo de publicação para bitrate áudio*

![Adereços de vídeo e áudio resultantes na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Adereços de vídeo e áudio resultantes na raiz*

A alteração de qualquer um destes três valores também reconfigura e altera os valores dos respetivos componentes a que estão ligados (e de onde são publicados).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Ter gerado nomes de ficheiros de saída dependem de valores de propriedade publicados
Em vez de codificar os nossos nomes de ficheiros gerados, podemos agora alterar a expressão do nome de ficheiro em cada um dos componentes da Saída de Ficheiros para confiar nas propriedades bitrate que publicamos na raiz do gráfico. Começando com a nossa primeira saída de ficheiros, encontre a propriedade do Arquivo e edite a expressão assim:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Os diferentes parâmetros desta expressão podem ser acedidos e introduzidos atingindo o sinal de dólar no teclado enquanto estão na janela de expressão. Um dos parâmetros disponíveis é a nossa propriedade de video1bitrate que publicámos anteriormente.

![Aceder a parâmetros dentro de uma expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Aceder a parâmetros dentro de uma expressão*

Faça o mesmo para a saída do ficheiro para o nosso segundo vídeo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e para a saída de ficheiros apenas áudio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se agora alterarmos o bitrate para qualquer um dos ficheiros de vídeo ou áudio, o respetivo codificador será reconfigurado e a convenção de nomes de ficheiros baseada em bitrate será honrada todas as automáticas.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Adicionar miniaturas à saída mp4 multibitrate
Partindo de um fluxo de trabalho que gere [uma saída de MP4 multibitrate a partir de uma entrada MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)vamos agora estar a estudar a adição de miniaturas à saída.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para adicionar miniaturas a
![Fluxo de trabalho MP4 multibitrate para começar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Fluxo de trabalho MP4 multibitrate para começar*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Adicionar codificação JPG
O coração da nossa geração de miniaturas será o componente JPG Encoder, capaz de obter ficheiros JPG.

![Codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codificador JPG*

No entanto, não podemos ligar diretamente o nosso fluxo de vídeo não comprimido da entrada de ficheiros de mídia para o codificador JPG. Em vez disso, espera receber quadros individuais. Isto, podemos fazer através do componente do Portão de Moldura de Vídeo.

![Ligar um portão de moldura ao codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ligar um portão de moldura ao codificador JPG*

O portão de moldura uma vez a cada tantos segundos ou quadros permite que uma moldura de vídeo passe. O intervalo e o tempo de compensação com o qual isto acontece é configurável nas propriedades.

![Propriedades do Portão de Moldura de Vídeo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriedades do Portão de Moldura de Vídeo*

Vamos criar uma miniatura a cada minuto, definindo o modo de tempo (segundos) e o Intervalo para 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Lidar com a conversão do espaço de cor
Embora pareça lógico que tanto os pinos de vídeo não comprimidos do portão de moldura como a entrada de ficheiros de mídia possam agora ser ligados, receberíamos um aviso se o fazssemos.

![Erro de espaço de cor de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erro de espaço de cor de entrada*

Isto porque a forma como a informação de cor está representada no nosso fluxo de vídeo original raw uncomprimprim, proveniente do nosso MXF, é diferente do que o JPG Encoder está à espera. Mais especificamente, espera-se que um chamado "espaço de cores" de "RGB" ou "Grayscale" flua. Isto significa que o fluxo de vídeo de entrada do Video Frame Gate precisa de ter uma conversão aplicada em relação ao seu espaço de cores primeiro.

Arraste para o fluxo de trabalho o Conversor de Espaço de Cor - Intel e ligue-o ao nosso portão de moldura.

![Conectando um conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Conectando um conversor de espaço de cor*

Na janela de propriedades, escolha a entrada BGR 24 da lista Predefinida.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Escrevendo as miniaturas
Diferente do nosso vídeo MP4, o componente JPG Encoder produz mais do que um ficheiro. Para lidar com isto, pode ser utilizado um componente de escritor de ficheiros JPG de Pesquisa de Cena: pega nas miniaturas do JPG e escreve-as, sendo cada nome de ficheiro sufixo por um número diferente. (O número tipicamente indicando o número de segundos/unidades no fluxo de que a miniatura foi retirada.)

![Apresentando a cena pesquisa JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Apresentando a cena pesquisa JPG File Writer*

Configure a propriedade 'Caminho da Pasta de Saída' com a expressão: ${ROOT_outputWriteDirectory}

e a propriedade Prefix nome de ficheiro com:

    ${ROOT_sourceFileBaseName}_thumb_

O prefixo determina como os ficheiros da miniatura estão a ser nomeados. São sufixos com um número que indica a posição do polegar no fluxo.

![Propriedades do escritor de arquivo sonuantes JPG File](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriedades do escritor de arquivo sonuantes JPG File*

Ligue o escritor de ficheiros JPG de pesquisa de cena ao nó de ficheiro/ativo de saída.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Detetar erros num fluxo de trabalho
Ligue a entrada do conversor de espaço de cor à saída de vídeo não comprimido cru. Agora faça um teste local para o fluxo de trabalho. Há uma boa hipótese de o fluxo de trabalho parar de executar e indicar com um contorno vermelho no componente que encontrou um erro:

![Erro do Conversor de Espaço de Cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erro do Conversor de Espaço de Cor*

Clique no pequeno ícone "E" vermelho no canto superior direito do componente Color Space Converter para ver qual é a razão pela qual a tentativa de codificação falhou.

![Diálogo de erro do conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Diálogo de erro do conversor de espaço de cor*

Acontece que, como pode ver, o padrão de espaço de cores para o conversor de espaço de cor tem de ser rec601 para a nossa conversão solicitada de YUV para RGB. Aparentemente, o nosso riacho não indica o seu rec601. (Rec 601 é um padrão para codificar sinais de vídeo analógicos entrelaçados em forma de vídeo digital. Especifica uma região ativa que cobre 720 amostras de luminância e 360 amostras de crominância por linha. O sistema de codificação de cores é conhecido como YCbCr 4:2:2.)

Para corrigir isto, vamos indicar nos metadados do nosso fluxo que estamos a lidar com conteúdo rec601. Para tal, usaremos um componente de atualização do tipo de dados de vídeo, que colocaremos entre a nossa fonte bruta e o componente de conversão de espaço de cor. Este atualizador de tipo de dados permite a atualização manual de determinadas propriedades do tipo de dados de vídeo. Configure-o para indicar um Padrão de Espaço de Cor de "Rec 601". Isto faz com que o Atualização do Tipo de Dados de Vídeo marque o fluxo com o espaço de cores "Rec 601" se ainda não houvesse espaço de cores definido. (Não sobreporá a nenhum metadados existente, a menos que a caixa de verificação de sobreposição tenha sido verificada.)

![Atualizar o Padrão do Espaço de Cor no Atualizador de Tipo de Dados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Atualizar o Padrão do Espaço de Cor no Atualizador de Tipo de Dados*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Fluxo de Trabalho Acabado
Agora que o nosso fluxo de trabalho está terminado, faça outro teste para vê-lo passar.

![Fluxo de trabalho acabado para saída multi-mp4 com miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fluxo de trabalho acabado para saída multi-mp4 com miniaturas*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Aparação baseada no tempo da saída de MP4 multibitrate
Partindo de um fluxo de trabalho que gere [uma saída de MP4 multibitrate a partir de uma entrada MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)vamos agora estar a analisar o vídeo de origem com base em selos de tempo.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Visão geral do fluxo de trabalho para começar a adicionar aparas a
![Iniciar o fluxo de trabalho para adicionar aparas a](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Iniciar o fluxo de trabalho para adicionar aparas a*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Usando o aparador de corrente
O componente Stream Trimmer permite-lhe cortar o início e o fim de uma base de fluxo de entrada em informações de tempo (segundos, minutos, ...). O aparador não suporta aparas à base de armação.

![Aparador de fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Aparador de fluxo*

Em vez de ligar os codificadores AVC e o designador de posição de altifalante à entrada de ficheiros de mídia diretamente, colocaremos entre os aparador de fluxo. (Um para o sinal de vídeo e outro para o sinal de áudio inter-leaved.)

![Coloque o aparador de corrente no meio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Coloque o aparador de corrente no meio*

Vamos configurar o aparador para que só processemos vídeo e áudio entre 15 segundos e 60 segundos no vídeo.

Vá às propriedades do Aparador de Fluxo de Vídeo e configure as propriedades do Tempo de Início (15 s) e do Tempo Final (60 s). Para garantir que tanto o nosso aparador de áudio como de vídeo estão sempre configurados para os mesmos valores de início e fim, publicamo-los na raiz do fluxo de trabalho.

![Publique a propriedade de início do dia do Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publique a propriedade de início do dia do Stream Trimmer*

![Publique o diálogo da propriedade para a hora de início](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publique o diálogo da propriedade para a hora de início*

![Publique o diálogo da propriedade para o fim do tempo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publique o diálogo da propriedade para o fim do tempo*

Se agora inspecionarmos a raiz do nosso fluxo de trabalho, ambas as propriedades são perfeitamente exibidas e configuráveis a partir daí.

![Propriedades publicadas disponíveis na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Propriedades publicadas disponíveis na raiz*

Agora abra as propriedades de corte do aparador de áudio e configure os tempos de início e de fim com uma expressão que se refere às propriedades publicadas na raiz do nosso fluxo de trabalho.

Para a hora de início do corte de áudio:

    ${ROOT_TrimmingStartTime}

e pelo seu fim de tempo:

    ${ROOT_TrimmingEndTime}

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Fluxo de Trabalho Acabado
![Fluxo de Trabalho Acabado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fluxo de Trabalho Acabado*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>Introdução do Componente Scripted
Os Componentes Scripted podem executar scripts arbitrários durante as fases de execução do nosso fluxo de trabalho. Existem quatro scripts diferentes que podem ser executados, cada um com características específicas, e o seu próprio lugar no ciclo de vida do fluxo de trabalho:

* **comandoScript**
* **realizeScript**
* **processoInputScript**
* **lifeCycleScript**

A documentação do Componente Scripted vai mais detalhadamente para cada um dos acima referidos. Na [secção seguinte,](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)o componente de script **do script do script do entende-se** é utilizado para construir uma lista de clipe xml na mosca quando o fluxo de trabalho começar. Este script é chamado durante a configuração do componente, que só acontece uma vez no seu ciclo de vida.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Scripting dentro de um fluxo de trabalho: olá mundo
Arraste um Componente Scripted para a superfície do designer e mude o nome (por exemplo, "SetClipListXML").

![Adicionar um componente scripted](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente scripted*

Quando inspecionar as propriedades do Componente Scripted, serão mostrados os quatro tipos de scriptdiferentes, cada um configurável para um script diferente.

![Propriedades dos componentes scripted](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades dos componentes scripted*

Limpe o processoInputScript e abra o editor para o realizeScript. Agora estamos preparados e prontos para começar a escrever.

Os scripts são escritos em Groovy, uma linguagem de script com compilado dinamicamente para a plataforma Java que mantém a compatibilidade com Java. Na verdade, a maioria do código Java é um código Groovy válido.

Vamos escrever um simples roteiro de hello world groovy no contexto do nosso realizeScript. Insira o seguinte no editor:

    node.log("hello world");

Agora execute um teste local. Depois desta execução, inspecione (através do separador Sistema no Componente Scripted) a propriedade De Registos.

![Olá saída de registo mundial](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Olá saída de registo mundial*

O objeto do nó a que chamamos o método de registo, refere-se ao nosso "nó" atual ou ao componente que estamos a escrever dentro. Todos os componentes como tal têm a capacidade de obter dados de registo, disponíveis através do separador do sistema. Neste caso, nós produzmos a corda literal "olá mundo". Importante entender aqui é que esta pode revelar-se uma ferramenta de depuração inestimável, fornecendo-lhe uma visão sobre o que o script está realmente fazendo.

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

![Saída de log para aceder aos caminhos do nó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Saída de log para aceder aos caminhos do nó*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Aparação baseada em quadros da saída mp4 multibitrate
Partindo de um fluxo de trabalho que gere [uma saída de MP4 multibitrate a partir de uma entrada MXF,](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)vamos agora estar a analisar o vídeo de origem com base nas contagens de fotogramas.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Visão geral da planta para começar a adicionar aparas a
![Fluxo de trabalho para começar a adicionar aparas a](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Fluxo de trabalho para começar a adicionar aparas a*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>Usando a Lista de Clipe XML
Em todos os tutoriais de fluxo de trabalho anteriores, usamos o componente de entrada de ficheiros de mídia como a nossa fonte de entrada de vídeo. Para este cenário específico, em vez disso, estaremos a usar o componente Clip List Source. Esta não deve ser a forma preferida de trabalhar; utilize apenas a Fonte da Lista de Clipes quando houver uma razão real para o fazer (como no caso seguinte, onde estamos a utilizar as capacidades de corte da lista de clipes).

Para mudar da nossa entrada de ficheiros de mídia para a Fonte da Lista de Clipes, arraste o componente Clip List Source para a superfície de design e ligue o pino Clip List XML ao nó XML da Lista de Clipes do designer de fluxo de trabalho. Isto povoa a Fonte da Lista de Clipes com pinos de saída, de acordo com o nosso vídeo de entrada. Agora ligue os pinos de áudio não comprimidos da Fonte de Clip List para os respetivos Codificadores AVC e Interleaver de Fluxo de Áudio. Agora remova a entrada do ficheiro de mídia.

![Substituiu a entrada de ficheiros de mídia com a Fonte da Lista de Clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Substituiu a entrada de ficheiros de mídia com a Fonte da Lista de Clipes*

O componente Clip List Source toma como entrada uma "Clip List XML". Ao selecionar o ficheiro fonte para testar localmente, esta lista de clipes xml é auto-povoada para si.

![Propriedade XML da Clip List auto-povoada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriedade XML da Clip List auto-povoada*

Olhando um pouco mais perto do xml, é assim que parece:

![Editar diálogo da lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Editar diálogo da lista de clipes*

No entanto, isto não reflete as capacidades da lista de clipes xml. Uma opção que temos é adicionar um elemento "Trim" sob a fonte de vídeo e áudio, como esta:

![Adicionar um elemento de corte à lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Adicionar um elemento de corte à lista de clipes*

Se modificar a lista de clipes como esta acima e realizar um teste local, verá que o vídeo foi corretamente aparado entre 10 e 20 segundos no vídeo.

Ao contrário do que acontece quando se faz uma corrida local, no entanto, esta mesma lista de clipe xml não teria o mesmo efeito quando aplicada num fluxo de trabalho que funciona nos Serviços de Mídia Azure. Quando o Azure Premium Encoder começa, a lista de clipe xml é gerada de novo, com base no ficheiro de entrada que o trabalho de codificação foi dado. Isto significa que quaisquer alterações que fizermos no xml seriam, infelizmente, ultrapassadas.

Para contrariar a lista de clipe xml que está a ser limpa quando um trabalho de codificação é iniciado, podemos regenera-lo em voo logo após o início do nosso fluxo de trabalho. Tais ações personalizadas podem ser tomadas através do que é chamado de "Componente Scripted". Para mais informações, consulte [A introdução do Componente Scripted](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Arraste um Componente Scripted para a superfície do designer e mude o nome para "SetClipListXML".

![Adicionar um componente scripted](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente scripted*

Quando inspeciona as propriedades do Componente Scripted, os quatro tipos de script diferentes são mostrados, cada um configurável para um script diferente.

![Propriedades dos componentes scripted](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades dos componentes scripted*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>Modificação da lista de clipes de um Componente Scripted
Antes de reescrevermos a lista de clipe xml que é gerada durante o arranque do fluxo de trabalho, precisamos de ter acesso à propriedade e conteúdo da cliplist xml. Podemos fazê-lo assim:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Lista de clipes de entrada a ser registada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clipes de entrada a ser registada*

Primeiro precisamos de uma forma de determinar a partir de que ponto até que ponto queremos cortar o vídeo. Para tornar isto conveniente para o utilizador menos técnico do fluxo de trabalho, publique duas propriedades na raiz do gráfico. Para isso, clique na superfície do designer e selecione "Adicionar Propriedade":

* Primeira propriedade: "ClippingTimeStart" do tipo: "TIMECODE"
* Segunda propriedade: "ClippingTimeEnd" de tipo: "TIMECODE"

![Adicione o diálogo da propriedade para o início do recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adicione o diálogo da propriedade para o início do recorte*

![Adereços de tempo de recorte publicados na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Adereços de tempo de recorte publicados na raiz do fluxo de trabalho*

Configure ambas as propriedades com um valor adequado:

![Configure as propriedades de arranque e fim de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configure as propriedades de arranque e fim de recorte*

Agora, de dentro do nosso script, podemos aceder a ambas as propriedades, assim:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Janela de log mostrando o início e o fim do recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Janela de log mostrando o início e o fim do recorte*

Vamos analisar as cordas do código de tempo num formulário mais conveniente para usar, usando uma simples expressão regular:

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

![Janela de log com saída de código de tempo analisado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Janela de log com saída de código de tempo analisado*

Com esta informação à mão, podemos agora modificar a lista de clipe xml para refletir os tempos de início e fim para o recorte demoldurado do filme.

![Código script para adicionar elementos de corte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código script para adicionar elementos de corte*

Isto foi feito através de operações normais de manipulação de cordas. A lista de clipes modificada resultante xml é escrita de volta para a propriedade clipListXML na raiz do fluxo de trabalho através do método "setProperty". A janela de registo após outro teste mostrar-nos-ia o seguinte:

![Registando a lista de clipes resultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registando a lista de clipes resultante*

Faça um teste para ver como os fluxos de vídeo e áudio foram cortados. Como fará mais do que um teste com valores diferentes para os pontos de aparação, notará que estes não serão levados em conta no entanto! A razão para isso é que o designer, ao contrário do tempo de funcionação do Azure, NÃO sobrepor-se à lista de clipe xml em cada corrida. Isto significa que apenas a primeira vez que definir os pontos de entrada e saída, fará`clipListXML.indexOf("<trim>") == -1`com que o xml se transforme, todas as outras vezes, a nossa cláusula de guarda (se, )) impedirá que o fluxo de trabalho adicione outro elemento de corte quando já existe um presente.

Para tornar o nosso fluxo de trabalho conveniente para testar localmente, é melhor adicionar mos si mesmo sacana de um código de limpeza que inspeciona se um elemento de guarnição já estava presente. Em caso afirmativo, podemos removê-lo antes de continuar modificando o xml com os novos valores. Em vez de usar manipulações de cordas simples, é provavelmente mais seguro fazê-lo através de uma análise real do modelo de objeto xml.

Antes de podermos adicionar tal código, precisamos adicionar uma série de declarações de importação no início do nosso script primeiro:

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

Este código vai um pouco acima do ponto em que adicionamos os elementos de corte à lista de clipe xml.

Neste momento, podemos executar e modificar o nosso fluxo de trabalho o quanto quisermos, enquanto temos as alterações aplicadas sempre.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Adicionar uma propriedade de conveniência ClippingEnabled
Como nem sempre pretende que aconteça aparar, vamos terminar o nosso fluxo de trabalho adicionando uma conveniente bandeira booleana que indica se queremos ou não permitir o corte/recorte.

Como antes, publique uma nova propriedade na raiz do nosso fluxo de trabalho chamado "ClippingEnabled" do tipo "BOOLEAN".

![Publicado uma propriedade para permitir o recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicado uma propriedade para permitir o recorte*

Com a cláusula de guarda simples abaixo, podemos verificar se é necessário aparar e decidir se a nossa lista de clipes como tal precisa de ser modificada ou não.

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
[Introdução de Codificação Premium nos Serviços De Mídia Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como utilizar a codificação premium nos serviços de mídia azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificação de conteúdos a pedido com o Serviço de Mídia Azure](media-services-encode-asset.md#media-encoder-premium-workflow)

[Media Encoder Premium Workflow Formatos e Codecs](media-services-premium-workflow-encoder-formats.md)

[Ficheiros de fluxo de trabalho de amostra](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
