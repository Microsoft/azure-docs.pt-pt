---
title: Tutoriais avançados do Media Encoder Premium Workflow
description: Este documento contém instruções passo a passo mostra como efetuar tarefas avançadas com o Media Encoder Premium Workflow e também como criar fluxos de trabalho complexos com o estruturador de fluxo de trabalho.
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
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 3d51f5328aec66eee0d8382026e8795db45a6a2c
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189789"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Tutoriais avançados do Media Encoder Premium Workflow
## <a name="overview"></a>Descrição geral
Este documento contém instruções passo a passo que mostram como personalizar fluxos de trabalho com **Designer de fluxo de trabalho**. Pode encontrar os ficheiros de fluxo de trabalho real [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>SUMÁRIO
São abordados os seguintes tópicos:

* [Codificação de ficheiros do MXF numa MP4 de velocidade de transmissão única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [A partir de um novo fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Usando a entrada de ficheiro do suporte de dados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspecionar o fluxo de multimédia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Um codificador vídeo para a adicionar. Geração de ficheiros MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [O fluxo de áudio de codificação](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexação de fluxos de áudio e vídeo para um contentor de MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Escrever o ficheiro MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Criação de um elemento de serviços de multimédia a partir do ficheiro de saída](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testar o fluxo de trabalho concluído localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Codificar MXF na multibitrate MP4s - empacotamento dinâmico ativado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Adicionar um ou mais saídas MP4 adicionais](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Configurar os nomes de saída do ficheiro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Adicionar uma faixa de áudio separado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Adicionar o ficheiro de SMIL "ISM".](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Codificar MXF na multibitrate MP4 - esquema aprimorada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Descrição geral de fluxo de trabalho para aprimorar
  * [As convenções de nomenclatura de ficheiros](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Propriedades do componente de publicação para a raiz de fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Tiver gerado o ficheiro de saída nomes utilizam valores de propriedade publicados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Adição de miniaturas para multibitrate saída MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Descrição geral de fluxo de trabalho para adicionar miniaturas para
  * [Adicionar a codificação de JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Lidando com a conversão de espaço de cor](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Escrever as miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Detectar erros num fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Remoção de baseados no tempo de mensagens em fila de saída multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Descrição geral de fluxo de trabalho para começar a adicionar dirá](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Usando o visualizem do Stream](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Apresentando o componente com script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Dentro de um fluxo de trabalho de scripts: Olá, mundo](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Remoção de quadros de mensagens em fila de saída multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Descrição geral do esquema para começar a adicionar dirá](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Usando a lista de Clip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modificar a lista de clip a partir de um componente com script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Adicionar uma propriedade de conveniência ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Codificação de ficheiros do MXF numa MP4 de velocidade de transmissão única
Esta secção demonstra como criar uma velocidade de transmissão única. Ficheiro MP4 com AAC-HE codificado áudio de um. Ficheiro de entrada de ficheiros do MXF.

### <a id="MXF_to_MP4_start_new"></a>A partir de um novo fluxo de trabalho
Abra o Designer de fluxo de trabalho e selecione ficheiro > novo espaço de trabalho > transcodificar esquema

O novo fluxo de trabalho mostra três elementos:

* Ficheiro de origem principal
* Lista de clip XML
* Ficheiro/recurso de saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Novo fluxo de trabalho de codificação*

### <a id="MXF_to_MP4_with_file_input"></a>Usando a entrada de ficheiro do suporte de dados
Para aceitar o ficheiro de multimédia de entrada, um começa com a adição de um componente de entrada de ficheiro do suporte de dados. Para adicionar um componente para o fluxo de trabalho, procurá-lo na caixa de pesquisa de repositório e arraste a entrada pretendida para o painel de designer. Repita a ação para a entrada de ficheiro do suporte de dados e ligar o componente de ficheiro de origem principal para o pin de entrada de nome de ficheiro a partir da entrada de ficheiro do suporte de dados.

![Entrada de ficheiros de multimédia ligados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada de ficheiros de multimédia ligados*

Inicialmente, identifica um ficheiro de exemplo apropriado para usar ao criar um fluxo de trabalho personalizado. Para tal, clique o plano de fundo do painel designer e procure a propriedade de ficheiro de origem principal no painel do lado direito de propriedade. Clique no ícone de pasta e selecionar o arquivo desejado para testar o fluxo de trabalho. O componente de entrada de arquivo de mídia inspeciona o arquivo e preenche a seus pins de saída para refletir os detalhes do ficheiro de exemplo que ele inspecioná-lo.

![Entrada de ficheiro de multimédia preenchida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada de ficheiro de multimédia preenchida*

Agora que a entrada estiver preenchida, a próxima etapa é configurar as definições de codificação de saída. Semelhante a como o principal arquivo de origem foi configurado, agora configurar a propriedade da variável de pasta de saída, logo abaixo.

![Propriedades de entrada e saída configuradas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriedades de entrada e saída configuradas*

### <a id="MXF_to_MP4_streams"></a>Inspecionar o fluxo de multimédia
Muitas vezes tem pretendido saber como o fluxo é semelhante à medida que fluem através do fluxo de trabalho. Para inspecionar um fluxo em qualquer ponto no fluxo de trabalho, basta clicar numa saída ou entrada pin em qualquer um dos componentes. Neste caso, tente clicar no pin de saída de vídeo não comprimida da entrada de ficheiro do suporte de dados. Uma caixa de diálogo abre-se que permite inspecionar o vídeo de saída.

![Inspecionar o pin de saída de vídeo não comprimida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspecionar o pin de saída de vídeo não comprimida*

Neste caso, ela mostra que o vídeo contém uma entrada de 1920 x 1080 em 24 quadros por segundo em 4:2:2 amostragem para ver um vídeo de quase 2 minutos.

### <a id="MXF_to_MP4_file_generation"></a>Um codificador vídeo para a adicionar. Geração de ficheiros MP4
Agora, um vídeo não comprimidos e saída de áudio não compactados vários pins estão disponíveis para utilizam na entrada de ficheiro de suporte de dados. Para codificar o vídeo de entrada, um componente de codificação tem de ser adicionada ao fluxo de trabalho - neste caso para gerar. Ficheiros MP4.

Para codificar o fluxo de vídeo para H.264, adicione o componente do codificador de vídeo AVC para a superfície de desenho. Este componente usa um fluxo de vídeo uncompress como entrada e fornece um AVC vídeo fluxo compactado o PIN de saída.

![Codificador de AVC solução desligada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codificador de AVC solução desligada*

As respetivas propriedades determinam como a codificação exatamente acontece. Vamos dar uma olhada em algumas das configurações mais importantes:

* Largura de saída e a altura de saída: determina a resolução do vídeo codificada. Neste caso, 640 x 360 é uma boa definição.
* Taxa de quadros: Quando definido como pass-through-lo apenas adotem a taxa de quadros de origem, é possível substituí-lo no entanto. Tal conversão framerate não é motion-compensada.
* Perfil e nível: determina o perfil de AVC e nível. Convenientemente, obter mais informações sobre os diferentes níveis e perfis, clique no ícone de ponto de interrogação no componente de codificador de vídeo AVC e a página de ajuda irão mostrar mais detalhes sobre todos os níveis de. Neste exemplo, utilize o perfil de Main no nível 3.2 (predefinição).
* Classificar o modo de controlo e a velocidade de transmissão (kbps): neste cenário, optar por uma constante de velocidade de transmissão (CBR) de saída em 1200 kbps
* Formato de vídeo: fornece informações sobre o VUI (informações de capacidade de utilização do vídeo), que é escrita no fluxo de H.264 (informações de lado que podem ser utilizados por um Decodificador para melhorar a exibição, mas não essenciais para decodificar corretamente):
* NTSC (típico dos Estados Unidos ou no Japão, com 30 fps)
* PAL (típico para a Europa, com 25 fps)
* Modo de tamanho de GOP: defina GOP de tamanho fixo para o nosso objetivo com um intervalo de chave de 2 segundos com GOPs fechado. A definição de 2 segundos garante que fornece compatibilidade com os serviços de multimédia do Azure de empacotamento dinâmico.

Para alimentar o codificador de AVC, ligue o pin de saída de vídeo não comprimidos entrada de ficheiro do suporte de dados do componente para o pin de entrada de vídeo não comprimidos do codificador AVC.

![Codificador de AVC ligados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Codificador de AVC Main ligado*

### <a id="MXF_to_MP4_audio"></a>O fluxo de áudio de codificação
Neste momento, o fluxo de áudio descompactado original ainda precisa de ser comprimidos. Para compressão do fluxo de áudio, adicione um componente do codificador de AAC (Dolby) para o fluxo de trabalho.

![Codificador de AVC solução desligada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificador AAC solução desligada*

Agora, há uma incompatibilidade: há apenas um único descomprimido áudio entrado pin do codificador AAC enquanto o mais provável é que a entrada de ficheiro do suporte de dados terão duas diferentes descomprimidas sequências de áudio em disponíveis: um para o canal de áudio à esquerda e outro para a direita. (Se estiver lidando com o som de surround, é seis canais.) Portanto, não é possível ligar diretamente o áudio a partir da origem de entrada de ficheiro do suporte de dados para o codificador de áudio AAC. O componente AAC espera um fluxo de áudio de "intercalado" chamado: um único fluxo que tenha canais certos intercalados entre si e à esquerda. Uma vez que sabemos do nosso arquivo de mídia de origem que faixas de áudio são em qual posição na origem, podemos gerar esse fluxo de áudio intercalado com as posições de orador corretamente atribuído para a esquerda e direita.

Em primeiro lugar, quer gerar um fluxo de intercalado a partir dos canais de áudio de origem necessários. O componente de áudio Stream Interleaver executa essa tarefa. Adicioná-lo para o fluxo de trabalho e ligar as saídas de áudio a partir de entrada de ficheiro do suporte de dados para o mesmo.

![Ligado Interleaver Stream de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Ligado Interleaver Stream de áudio*

Agora que temos uma transmissão de áudio intercalada, ainda não especificamos onde pretende atribuir as posições de orador do esquerda ou direita para. Para poder especificar isso, podemos aproveitar o mandatário de posição do orador.

![Adicionar um mandatário de posição de orador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Adicionar um mandatário de posição de orador*

Configurar o mandatário de posição de orador para utilização com um fluxo de entrada estéreo por meio de um filtro de configuração predefinida do codificador de "Custom" e a configuração predefinida do canal chamado "2.0 (L, R)." (Esta ação atribui a posição de orador à esquerda para a 1 de canal e a posição de orador certa para o canal 2.)

Ligue a saída do mandatário de posição orador para a entrada do codificador de AAC. Em seguida, informar ao codificador AAC para trabalhar com um "2.0 (L, R)" canal predefinido, para que sabe como para lidar com estéreo áudio como entrada.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexação de fluxos de áudio e vídeo para um contentor de MP4
Fornecido nosso AVC codificado de fluxo de vídeo e nosso AAC codificado por transmissão de áudio, conseguimos capturar ambos num. Contentor de MP4. O processo de misturar várias transmissões em fluxo para um único é chamado de "multiplexação" (ou "muxing"). Neste caso, iremos estiver intermediárias o áudio e os fluxos de vídeo num único coerente. Pacote de MP4. O componente que coordena isso para um. MP4 contentor é chamado Multiplexador o ISO MPEG-4. Adicione uma para a superfície de desenho e ligar o codificador de vídeo AVC e o codificador de AAC a das respetivas entradas.

![Ligado MPEG4 Multiplexador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Ligado MPEG4 Multiplexador*

### <a id="MXF_to_MP4_writing_mp4"></a>Escrever o ficheiro MP4
Ao escrever um ficheiro de saída, o componente de saída do arquivo é usado. Pode ligar este à saída do Multiplexador ISO MPEG-4, para que o respetivo resultado é escrito no disco. Para fazer isso, ligue-se o pin de saída do contentor (MPEG-4) para o pin de entrada de escrita de ficheiro de saída.

![Ligado a saída do ficheiro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Ligado a saída do ficheiro*

O nome de ficheiro utilizado é determinado pela propriedade de ficheiro. Embora essa propriedade pode ser codificado para um determinado valor, mais provavelmente, uma quer defini-lo por meio de uma expressão.

Para que o fluxo de trabalho determinar automaticamente a saída de propriedade de nome de uma expressão de ficheiros, clique no botão junto ao nome do ficheiro (junto ao ícone de pasta). No menu pendente, em seguida, selecione "Expressão". Esta ação exibe o editor de expressões. Limpe o conteúdo do editor pela primeira vez.

![Editor de expressões vazia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de expressões vazia*

O editor de expressões permite-lhe introduzir qualquer valor literal, misturado com um ou mais variáveis. Variáveis de começam com um cifrão. Como pressiona a tecla de $, o editor mostra uma caixa de lista suspensa com uma escolha das variáveis disponíveis. No nosso caso, usaremos uma combinação da variável de diretório de saída e a variável de nome de ficheiro de entrada base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Preenchido o Editor de expressões](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Preenchido o Editor de expressões*

> [!NOTE]
> Para ver um ficheiro de saída do seu trabalho de codificação no Azure, tem de fornecer um valor no editor de expressões.
>
>

Quando o utilizador confirma a expressão ao pressionar ok, a janela de propriedade pré-visualizações que valor o propriedade de ficheiro seja resolvido no momento.

![Expressão de arquivo resolve dir de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Expressão de arquivo resolve dir de saída*

### <a id="MXF_to_MP4_asset_from_output"></a>Criação de um elemento de serviços de multimédia a partir do ficheiro de saída
Enquanto estamos escreveram um ficheiro de saída MP4, ainda precisamos indicar que este ficheiro pertence ao elemento de saída que serviços de suporte de dados gera como resultado de executar este fluxo de trabalho. Para este fim, é utilizado o nó de arquivo/recurso de saída na tela do fluxo de trabalho. Todos os ficheiros de entrada para este nó que façam parte do elemento resultante dos serviços de multimédia do Azure.

Ligue-se o componente de saída do ficheiro para o componente de arquivo/recurso de saída para concluir o fluxo de trabalho.

![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fluxo de trabalho concluído*

### <a id="MXF_to_MP4_test"></a>Testar o fluxo de trabalho concluído localmente
Para testar localmente o fluxo de trabalho, pressione o botão play na barra de ferramentas na parte superior. Quando o fluxo de trabalho concluído em execução, Inspecione o resultado gerado na pasta de saída configurados. Verá o ficheiro de saída MP4 terminado que foi codificado do ficheiro de origem de entrada de ficheiros do MXF.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Codificação de ficheiros do MXF num MP4 - multibitrate empacotamento dinâmico ativado
Este passo a passo cria um conjunto de ficheiros MP4 de velocidade de transmissão múltipla com AAC codificado áudio de um único. Ficheiro de entrada de ficheiros do MXF.

Quando uma saída de ativo de velocidade de transmissão for o pretendido para utilização em combinação com os recursos de empacotamento dinâmico oferecidos pelos serviços de multimédia do Azure, vários ficheiros de alinhado GOP MP4 de cada uma velocidade de transmissão diferente e a resolução tem de ser gerado. Para fazê-lo, o [MXF de codificação de mensagens em fila numa MP4 de velocidade de transmissão única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) passo a passo dá-nos com um ponto de partida.

![A iniciar o fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*A iniciar o fluxo de trabalho*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Adicionar um ou mais saídas MP4 adicionais
Todos os ficheiros MP4 no nosso recurso de serviços de multimédia do Azure resultante oferece suporte a uma velocidade de transmissão diferente e a resolução. Vamos adicionar um ou mais ficheiros de saída MP4 ao fluxo de trabalho.

Para certificar-se de que temos nosso codificadores de vídeo criados com as mesmas definições, é mais conveniente duplicar o codificador de vídeo AVC já existente e configurar outra combinação de resolução e velocidade de transmissão (vamos adicionar um 960 x 540 a 25 quadros por segundo a 2,5 Mbps ). Para duplicar o codificador existente, o cópia colá-lo a superfície de design.

Ligar o pin de saída de vídeo não comprimida da entrada de ficheiro do suporte de dados para o nosso novo componente de AVC.

![Codificador de AVC segundo ligado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Codificador de AVC segundo ligado*

Agora, adaptar-se a configuração para o nosso codificador AVC nova para a saída 960 x 540 a 2,5 Mbps. (Usar suas propriedades "largura de saída", "Altura de saída" e "Transmissão de velocidade de transmissão (kbps)" para isso.)

Tendo em conta que queremos usar o elemento resultante, juntamente com um empacotamento dinâmico dos serviços de multimédia do Azure, o ponto final de transmissão em fluxo tem de ser capaz de gerar destes ficheiros MP4 fragmentos HLS/fragmentado MP4/DASH que estão alinhados exatamente entre si de forma que os clientes que estão a alternância entre diferentes velocidades de transmissão obtém uma única contínua áudio e vídeo experiência positiva. Para fazer isso acontecer, precisamos de Certifique-se de que, nas propriedades de codificadores AVC o GOP ("grupo de imagens"), tamanho de ambos os ficheiros MP4 está definido como 2 segundos, que pode ser feitos por:

* definir o modo de tamanho de GOP tamanho fixo GOP e
* o intervalo de quadro chave como dois segundos.
* também definir o controle IDR GOP GOP fechado para garantir que todos os GOPs estão aguardando em seus próprios sem dependências

Para tornar este fluxo de trabalho mais fácil de entender, mudar o nome do codificador de AVC primeiro para "codificador de vídeo AVC 640 x 360 1200 kbps" e o codificador de AVC segundo "codificador de vídeo AVC 960 x 540 2500 kbps."

Agora, adicione uma segunda ISO MPEG-4 Multiplexador e um segundo ficheiro de saída. Ligue o Multiplexador para o novo codificador de AVC e certificar-se de que o resultado é direcionado para a saída do ficheiro. Em seguida, ligar também entrada AAC codificador áudio saída para o novo do Multiplexador. A saída do ficheiro por sua vez pode, em seguida, ser ligada ao nó de arquivo/recurso de saída para adicioná-lo para o recurso de serviços de suporte de dados que vai ser criada.

![Ligado Muxer segundo e o ficheiro de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Ligado Muxer segundo e o ficheiro de saída*

Para compatibilidade com o empacotamento dinâmico de serviços de multimédia do Azure, configure de segmentos de modo o Multiplexador a contagem de GOP ou duração e defina os GOPs por segmento para 1. (Deve ser a predefinição).

![Modos de segmentos de Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos de segmentos de Muxer*

Nota: poderá repetir este processo para quaisquer outros resolução e velocidade de transmissão combinações que pretende ter adicionado para a saída de ativo.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurar os nomes de saída do ficheiro
Temos mais de um único ficheiro adicionado ao elemento de saída. Isso fornece a necessidade de certificar-se de que os nomes de arquivo para cada um dos ficheiros de saída são diferentes entre si e talvez até mesmo aplicar uma convenção de nomenclatura de ficheiro para que o torna-se claro a partir do nome do ficheiro o que está lidando com.

Nomes de saída de arquivos podem ser controlada por meio de expressões no designer. Abrir o painel de propriedade de um dos componentes de saída do arquivo e abrir o editor de expressão para a propriedade de ficheiro. Nosso primeiro ficheiro de saída foi configurado por meio da seguinte expressão (veja o tutorial para ir do [MXF para uma saída MP4 de velocidade de transmissão única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Isso significa que o nosso nome de ficheiro é determinado pelo duas variáveis: o diretório de saída para escrever em e o nome de base de ficheiro de origem. O primeiro é exposto como uma propriedade na raiz de fluxo de trabalho e a última opção é determinado pelo ficheiro de entrada. O diretório de saída é o que utilizar para a realização de testes locais; Esta propriedade irá ser substituída pelo mecanismo de fluxo de trabalho quando o fluxo de trabalho é executado pelo processador de multimédia com base na cloud nos serviços de multimédia do Azure.
Para atribuir ambos os ficheiros de saída nosso um resultado consistente de nomenclatura, altere o primeiro arquivo expressão para a atribuição de nomes:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e o segundo para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Execute um teste intermediário executar para se certificar de que ambos os ficheiros de saída MP4 corretamente são gerados.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Adicionar uma faixa de áudio separado
Como verá mais tarde quando geramos um ficheiro. ISM com ficheiros MP4 de saída, podemos também exigirá ficheiros MP4 só de áudio como a faixa de áudio para nossa transmissão em fluxo adaptável. Para criar este ficheiro, adicione um muxer adicional ao fluxo de trabalho (ISO-MPEG-4 Multiplexador) e ligar o pin de saída do codificador AAC com o pin de entrada para a faixa de 1.

![Áudio Muxer adicionado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Áudio Muxer adicionado*

Crie um terceiro componente de saída do ficheiro de saída do fluxo de saída do muxer e configurar o ficheiro de nomenclatura expressão como:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Áudio Muxer criar ficheiro de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Áudio Muxer criar ficheiro de saída*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Adicionar o. Arquivo SMIL ISM
Para o empacotamento dinâmico trabalhar em combinação com ficheiros MP4 (tanto o MP4 de só de áudio) no nosso recurso de serviços de multimédia, também precisamos de um arquivo de manifesto (também chamado de arquivo de "SMIL": Sincronizadas a linguagem de integração de multimídia). Este ficheiro indica aos serviços de multimédia do Azure que ficheiros MP4 estão disponíveis para empacotamento dinâmico e qual a considerar para o streaming de áudio. Um arquivo de manifesto típico para um conjunto do MP4 com um único fluxo de áudio tem esta aparência:

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

Contém o ficheiro. ISM dentro de uma instrução switch, uma referência a cada um dos arquivos de vídeo MP4 individuais e, além dessas referências de arquivo de áudio também uma (ou mais) para um MP4 que contém apenas o áudio.

Gerar o arquivo de manifesto para nosso conjunto do MP4 pode ser feito por meio de um componente chamado "Gravador de manifesto de AMS." Para usá-lo, arraste-o para a superfície e ligar os pins de saída "Escrever concluído" a partir de três componentes de saída do ficheiro para a entrada de escritor do manifesto do AMS. Em seguida, certificar-se de que ligue a saída do escritor de manifesto do AMS para o ficheiro/recurso saída.

Tal como acontece com nossos outros ficheiros saída componentes, configure o nome de saída do ficheiro. ISM com uma expressão:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Nosso fluxo de trabalho concluído é semelhante a abaixo:

![Ficheiros do MXF concluído para o fluxo de trabalho do multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Ficheiros do MXF concluído para o fluxo de trabalho do multibitrate MP4*

## <a id="MXF_to__multibitrate_MP4"></a>Codificar MXF na multibitrate MP4 - esquema aprimorada
Na [instruções de fluxo de trabalho anteriores](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) já vimos como um único elemento de entrada de ficheiros do MXF pode ser convertido para um elemento de saída com ficheiros MP4 de velocidade de transmissão, ficheiros MP4 só de áudio e um arquivo de manifesto para utilização em conjunto com suporte de dados do Azure Serviços de empacotamento dinâmico.

Estas instruções mostram como alguns dos aspectos podem ser melhorados e torna mais conveniente.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Descrição geral de fluxo de trabalho para aprimorar
![Fluxo de trabalho Multibitrate MP4 para aprimorar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Fluxo de trabalho Multibitrate MP4 para aprimorar*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>As convenções de nomenclatura de ficheiros
O fluxo de trabalho anterior, especificamos uma expressão simples como base para gerar nomes de ficheiro de saída. Temos alguma duplicação entanto: todos os componentes do ficheiro de saída individual especificado tal expressão.

Por exemplo, o nosso componente de saída do ficheiro para o primeiro ficheiro de vídeo está configurado com esta expressão:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Embora, para a segunda saída vídeo, temos uma expressão, como:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Não seria mais limpo, menos propenso a erro e mais conveniente se poderíamos remover alguns dessa duplicação e tornar as coisas mais configurável em vez disso? Felizmente, pode: capacidades de expressão do designer em combinação com a capacidade de criar propriedades personalizadas em nossa raiz do fluxo de trabalho irão fornecer uma camada adicional de conveniência.

Vamos supor que irá orientar configuração de nome de ficheiro de velocidades de transmissão dos ficheiros MP4 individuais. Estes velocidades de transmissão que vai visamos para configurar a num sítio central (na raiz do nosso gráfico), de onde vai ser acedidos para configurar e a geração de nome de ficheiro de unidade. Para fazer isso, vamos começar por publicar a propriedade de velocidade de transmissão a partir de codificadores AVC para a raiz do nosso fluxo de trabalho, para que ele se torna acessível a partir de ambos os raiz, bem como os codificadores AVC. (Mesmo se o exibido na dois pontos diferentes, há apenas um valor subjacente.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Propriedades do componente de publicação para a raiz de fluxo de trabalho
Abra o codificador de AVC primeiro, vá para a propriedade de velocidade de transmissão (kbps) e na lista pendente, escolha publicar.

![A propriedade de velocidade de transmissão de publicação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*A propriedade de velocidade de transmissão de publicação*

Configure a caixa de diálogo para publicar na raiz do nosso gráfico de fluxo de trabalho, com um nome publicado de "video1bitrate" e um nome de exibição legíveis das "Velocidade de transmissão do vídeo 1". Configurar um personalizado o nome do grupo chamado "De transmissão em fluxo velocidades de transmissão" e clique em publicar.

![A propriedade de velocidade de transmissão de publicação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Caixa de diálogo publicação para a propriedade de velocidade de transmissão*

Repita o mesmo para a propriedade de velocidade de transmissão do codificador de AVC segundo e dê-lhe o nome "video2bitrate" com um nome a apresentar do "Vídeo 2 velocidade de transmissão", no mesmo grupo personalizado "De transmissão em fluxo velocidades de transmissão".

Se agora, vamos inspecionar as propriedades de raiz do fluxo de trabalho, veremos o nosso grupo personalizado com as duas propriedades publicados aparecer. Ambos são que reflete o valor de seus respectivo AVC codificador velocidade de transmissão múltipla.

![Propriedades de velocidade de transmissão publicados na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Sempre que queremos acessar essas propriedades de código ou de uma expressão, podemos fazer isso como este:

* a partir do código inline de um componente logo abaixo da raiz: node.getPropertyAsString('.. / video1bitrate', nulo)
* dentro de uma expressão: ${ROOT_video1bitrate}

Vamos concluir o grupo de "De transmissão em fluxo velocidades de transmissão" publicando nossa faixa de áudio de velocidade de transmissão no mesmo também. Nas propriedades do codificador de AAC, procure a definição de velocidade de transmissão e selecione publicar no menu pendente junto ao mesmo. Publicar na raiz do gráfico com o nome "audio1bitrate" e exibir o nome "Velocidade de transmissão do áudio 1". o dentro do nosso grupo personalizado "De transmissão em fluxo velocidades de transmissão".

![Caixa de diálogo publicação de velocidade de transmissão de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Caixa de diálogo publicação de velocidade de transmissão de áudio*

![Propriedades de áudio e vídeos resultantes na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Propriedades de áudio e vídeos resultantes na raiz*

Alterar nenhum dessas três valores também reconfigura e altera os valores nos respectivos componentes estão ligados com (e onde publicados a partir do).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Tiver gerado o ficheiro de saída nomes utilizam valores de propriedade publicados
Em vez de codificar nossos nomes de ficheiro XML gerado, podemos agora alterar nossa expressão de nome de ficheiro em cada um dos componentes do ficheiro de saída contar com as propriedades de velocidade de transmissão que publicamos na raiz do gráfico. A partir do nosso primeiro saída de arquivo, localizar a propriedade de ficheiro e editar a expressão como esta:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Os parâmetros diferentes nesta expressão podem ser acedidos e inseridos por atingir o símbolo de dólar norte-americano no teclado enquanto na janela de expressão. Um dos parâmetros disponíveis é nossa propriedade video1bitrate que publicamos anteriormente.

![Acessando parâmetros de dentro de uma expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Acessando parâmetros de dentro de uma expressão*

Fazer o mesmo para a saída do ficheiro para o nosso vídeo segundo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e para a saída de arquivo só de áudio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se alterarmos agora a velocidade de transmissão de qualquer um dos ficheiros de vídeos ou áudio, o codificador respectivo será reconfigurado e a Convenção de nome de ficheiro com base na velocidade de transmissão será cumprida tudo automática.

## <a id="thumbnails_to__multibitrate_MP4"></a>Adição de miniaturas para multibitrate saída MP4
A partir de um fluxo de trabalho que gera [uma saída de multibitrate MP4 de uma entrada de MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos agora irá procurar para a inclusão de miniaturas para a saída.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Descrição geral de fluxo de trabalho para adicionar miniaturas para
![Multibitrate MP4 fluxo de trabalho para iniciar a partir de](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4 fluxo de trabalho para iniciar a partir de*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Adicionar a codificação de JPG
O coração da nossa geração de miniaturas serão o componente do codificador de JPG, capaz de JPG ficheiros de saída.

![Codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codificador JPG*

Diretamente no entanto, não é possível ligar nosso fluxo de vídeo não comprimida da entrada de ficheiro do suporte de dados para o codificador JPG. Em vez disso, ele espera ser entregue quadros individuais. Isso, podemos fazer por meio do componente de porta de quadro de vídeo.

![Ligar uma porta de quadro ao codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ligar uma porta de quadro ao codificador JPG*

A porta de quadro uma vez a cada tantos segundos ou frames permite que um quadro de vídeo passar. O intervalo e o tempo de deslocamento com que isso acontece pode ser configurada nas propriedades.

![Propriedades de porta de quadro vídeo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriedades de porta de quadro vídeo*

Vamos criar uma miniatura de cada minuto ao definir o modo de tempo (segundos) e o intervalo para 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Lidando com a conversão de espaço de cor
Embora parecer lógico de ambos os pins de vídeo não comprimida da porta de quadro e a entrada de ficheiro do suporte de dados podem ser conectados agora, Obteremos um aviso se nós a faríamos isso.

![Erro de espaço de cor de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erro de espaço de cor de entrada*

Isto acontece porque a maneira na qual cor informações são representadas no nosso original não processado não comprimido fluxo de vídeo, provenientes de nossos MXF, é diferente do que o codificador de JPG está esperando. Mais especificamente, um chamado "cor espaço" de "RGB" ou "Em tons de cinzento" é esperado que o flow na. Isso significa que o fluxo de vídeo entrado da porta quadro de vídeo tem de ter uma conversão aplicada primeiro em relação a seu espaço de cor.

Arraste para o fluxo de trabalho o conversor de espaço de cor - Intel e ligá-la à nossa porta de quadro.

![Ligar um conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Ligar um conversor de espaço de cor*

Na janela Propriedades, escolha a entrada de BGR 24 na lista de configuração predefinida.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Escrever as miniaturas
Ao contrário do nosso vídeo de MP4, o componente do codificador de JPG devolve mais de um ficheiro. Para lidar com isso, um componente de pesquisa de cena JPG escritor de ficheiro pode ser utilizado: ele leva as miniaturas JPG recebidas e escreve-os, cada nome de ficheiro que está a ser um sufixo formado por um número diferente. (O número, normalmente, que indica o número de segundos/unidades no fluxo que a miniatura foi desenhada a partir de.)

![Apresentando o escritor de ficheiro JPG de pesquisa de cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Apresentando o escritor de ficheiro JPG de pesquisa de cena*

Configurar a propriedade do caminho da pasta de saída com a expressão: ${ROOT_outputWriteDirectory}

e a propriedade de prefixo de nome de ficheiro com o:

    ${ROOT_sourceFileBaseName}_thumb_

O prefixo determina como os arquivos em miniatura estão a ser chamados. Eles são o sufixo com um número que indica a posição do polegar no fluxo.

![Propriedades do gravador de ficheiro JPG de pesquisa de cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriedades do gravador de ficheiro JPG de pesquisa de cena*

Ligar o escritor de ficheiro de cena pesquisa JPG ao nó de arquivo/recurso de saída.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Detectar erros num fluxo de trabalho
Ligue-se a entrada do conversor de espaço de cor para a saída de vídeo não comprimida não processada. Agora, execute um teste de local para o fluxo de trabalho. Há uma boa chance do fluxo de trabalho, de repente, irá parar a execução e indicar com um destaque de vermelho no componente que encontrou um erro:

![Erro de conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erro de conversor de espaço de cor*

Clique no ícone pequeno de "E" vermelho no canto superior direito de falha de canto do componente conversor de espaço de cor para ver o que é o motivo pelo qual a tentativa de codificação.

![Caixa de diálogo de erro de conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Caixa de diálogo de erro de conversor de espaço de cor*

Acontece que, como pode ver, em que o espaço de cor entrada padrão para o conversor de espaço de cor tem de ser rec601 para a nossa conversão pedida de YUV para RGB. Aparentemente, o nosso fluxo não indica seu rec601. (Rec 601 é um padrão para codificação entrelaçadas sinais de vídeo analógico no formato de vídeo digital. Especifica uma região de Active Directory que abrange 720 amostras luminance e 360 chrominance exemplos por linha. A cor do sistema de codificação é conhecida como YCbCr 4:2:2.)

Para corrigir este problema, podemos irá indicar nos metadados do nosso fluxo que estamos lidando com rec601 conteúdo. Para fazer isso, usaremos um componente do Atualizador de tipo de dados de vídeo, o que, colocamos entre nossa fonte bruto e o componente de conversão de espaço de cor. Esse atualizador do tipo de dados permite a atualização manual de determinados dados de vídeo propriedades do tipo. Configure para indicar um padrão de espaço de cor de "Rec 601". Isso faz com que o Atualizador de tipo de dados de vídeo marcar o fluxo com o espaço de cor "Rec 601" se não houvesse nenhum espaço de cor definido ainda. (Ele não substituirá quaisquer metadados existentes, a menos que a caixa de verificação de substituição tiver sido selecionada.)

![A atualizar o espaço de cor padrão sobre o Atualizador de tipo de dados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*A atualizar o espaço de cor padrão sobre o Atualizador de tipo de dados*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Fluxo de trabalho concluído
Agora que nosso fluxo de trabalho está concluído, fazer outro teste execução para ver o teste é aprovado.

![Fluxo de trabalho concluído para a saída de várias mp4 com miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fluxo de trabalho concluído para a saída de várias mp4 com miniaturas*

## <a id="time_based_trim"></a>Remoção de baseados no tempo de mensagens em fila de saída multibitrate MP4
A partir de um fluxo de trabalho que gera [uma saída de multibitrate MP4 de uma entrada de MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos agora irá procurar em Cortar o vídeo de origem com base nos carimbos de data / hora.

### <a id="time_based_trim_start"></a>Descrição geral de fluxo de trabalho para começar a adicionar dirá
![A iniciar o fluxo de trabalho para adicionar dirá](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*A iniciar o fluxo de trabalho para adicionar dirá*

### <a id="time_based_trim_use_stream_trimmer"></a>Usando o visualizem do Stream
O componente de visualizem Stream permite-lhe cortar o início e fim de um fluxo de entrada base em informações (segundos, minutos,...) a exceder o tempo. O visualizem não suporta a remoção de baseados em frame.

![Stream visualizem](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream visualizem*

Em vez de vincular os codificadores AVC e mandatário de posição de orador para a entrada de ficheiro do suporte de dados diretamente, colocaremos entre aqueles visualizem o fluxo. (Um para o sinal de vídeo e outro para o sinal de áudio intercalado.)

![Colocar o Stream visualizem entre](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Colocar o Stream visualizem entre*

Vamos configurar o visualizem, de modo a que iremos processar apenas vídeo e áudio entre 15 segundos e 60 segundos no vídeo.

Vá para as propriedades de visualizem de Stream vídeo e configure os dois Start Time (15 s) e a hora de fim (60 s) propriedades. Para certificar-se de que os nossos visualizem de áudio e vídeo sempre está configurado para o mesmo valores inicial e final, publicamos aqueles na raiz do fluxo de trabalho.

![Publicar a propriedade de hora de início do Stream visualizem](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicar a propriedade de hora de início do Stream visualizem*

![Publicar a caixa de diálogo de propriedade para a hora de início](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicar a caixa de diálogo de propriedade para a hora de início*

![Publicar a caixa de diálogo de propriedade para a hora de fim](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publicar a caixa de diálogo de propriedade para a hora de fim*

Se agora, vamos inspecionar a raiz do nosso fluxo de trabalho, ambas as propriedades são claramente apresentado e configuráveis a partir daí.

![Publicado propriedades disponíveis na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicado propriedades disponíveis na raiz*

Agora, abra as propriedades de remoção do áudio visualizem e configurar horas de início e fim com uma expressão que se refere às propriedades publicadas na raiz do nosso fluxo de trabalho.

Para a hora de início de remoção de áudio:

    ${ROOT_TrimmingStartTime}

e para a hora de fim:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Fluxo de trabalho concluído
![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fluxo de trabalho concluído*

## <a id="scripting"></a>Apresentando o componente com script
Componentes com script podem executar scripts arbitrários durante as fases de execução do nosso fluxo de trabalho. Existem quatro scripts diferentes que podem ser executadas, cada qual com características específicas e seu próprio local no ciclo de vida de fluxo de trabalho:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

A documentação do componente com script vai mais detalhadamente para cada um dos acima. Na [a seção a seguir](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), o **realizeScript** componente de criação de scripts é usado para construir um xml cliplist imediatamente quando o fluxo de trabalho é iniciado. Este script é chamado durante a configuração de componente, o que acontece apenas uma vez no seu ciclo de vida.

### <a id="scripting_hello_world"></a>Dentro de um fluxo de trabalho de scripts: Olá, mundo
Arraste um componente com script para a superfície de desenho e mude o nome (por exemplo, "SetClipListXML").

![Adicionar um componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente com script*

Quando inspecionar as propriedades do componente com script, os quatro tipos de script diferente será mostrado, cada configuráveis para um script diferente.

![Propriedades do componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente com script*

Limpe o processInputScript e abra o editor para o realizeScript. Agora, estão configuradas e pronto para começar a criar scripts.

Scripts são escritos em baseada em Groovy, uma linguagem de script dinamicamente compilada para a plataforma de Java que mantém a compatibilidade com o Java. Na verdade, a maior parte do código Java é código excelente válido.

Vamos escrever um script de excelente de mundo Olá simples no contexto de nosso realizeScript. Introduza o seguinte no editor:

    node.log("hello world");

Agora a executar uma execução de teste local. Após essa prática, Inspecione a propriedade de registos (por meio de separador de sistema no componente com script).

![Saída de registo do Hello world](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Saída de registo do Hello world*

O objeto de nó, chamamos o método de registo, se refere ao nosso "nó" atual ou o componente que está a scripts dentro. Todos os componentes como tal, tem a capacidade de dados de registo de saída, disponível através do separador de sistema. Neste caso, produzimos a saída o literal de cadeia de caracteres "hello world". É importante compreender aqui é que isso pode se para tornar uma ferramenta de depuração inestimável, dando-lhe conhecimentos aprofundados sobre o que o script está realmente fazendo.

De dentro do nosso ambiente de script, também temos acesso a propriedades em outros componentes. Experimente isto:

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

Nossa janela de registo nos mostra o seguinte:

![Saída de registo para aceder aos caminhos de nó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Saída de registo para aceder aos caminhos de nó*

## <a id="frame_based_trim"></a>Remoção de quadros de mensagens em fila de saída multibitrate MP4
A partir de um fluxo de trabalho que gera [uma saída de multibitrate MP4 de uma entrada de MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), podemos agora irá procurar em Cortar o vídeo de origem com base nas contagens de quadro.

### <a id="frame_based_trim_start"></a>Descrição geral do esquema para começar a adicionar dirá
![Fluxo de trabalho para começar a adicionar dirá](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Fluxo de trabalho para começar a adicionar dirá*

### <a id="frame_based_trim_clip_list"></a>Usando a lista de Clip XML
Em todos os tutoriais de fluxo de trabalho anteriores, usamos o componente de entrada de ficheiro do suporte de dados como o nosso vídeo origem de entrada. Para este cenário específico, vamos utilizar o componente de origem da lista de Clip em vez disso. Isso não deve ser a maneira preferencial de trabalhar; Utilize apenas a origem da lista de Clip quando há uma razão para isso (como no caso seguinte, em que estamos a disponibilizar utilização das funcionalidades de remoção de lista de clip).

Para mudar da nossa entrada de ficheiro de suporte de dados para a origem da lista de Clip, arraste o componente de origem da lista de Clip para a superfície de design e ligue-se o pin de XML de lista de Clip para o nó de XML de lista de Clip do designer fluxo de trabalho. Isso popula a origem da lista de Clip com pins de saída, de acordo com o nosso vídeo de entrada. Agora ligar o vídeo não comprimidos e os pins de áudio descompactados da origem da lista de Clip para o respectivo AVC codificadores e áudio Stream Interleaver. Agora, remova a entrada de ficheiro do suporte de dados.

![Substituído a entrada de ficheiro do suporte de dados com a origem da lista de Clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Substituído a entrada de ficheiro do suporte de dados com a origem da lista de Clip*

O componente de origem da lista de Clip utiliza como entrada uma "Clip lista XML." Ao selecionar o ficheiro de origem para testar com localmente, este xml de lista do clip é preenchido automaticamente para.

![Propriedade de XML de lista de Clip preenchidos automaticamente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriedade de XML de lista de Clip preenchidos automaticamente*

À procura um pouco mais de perto do XML, isso é como ele se parece com:

![Caixa de diálogo do clip lista editar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Caixa de diálogo do clip lista editar*

Isso no entanto não reflete as capacidades do xml de lista clip. Uma opção que temos é adicionar um elemento de "Trim" em ambos os o áudio e vídeo origem, como este:

![Adição de um elemento de compactação para a lista de clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Adição de um elemento de compactação para a lista de clip*

Se modificar o xml de lista de clip assim acima e executar uma execução de testes locais, verá o vídeo foi corretamente removido entre 10 e 20 segundos no vídeo.

Ao contrário do que acontece quando o fizer uma execução local no entanto, esse mesmo xml de cliplist não teria o mesmo efeito quando aplicados a um fluxo de trabalho que é executado nos serviços de multimédia do Azure. Quando é iniciado o codificador de Premium do Azure, o xml de cliplist é gerado sempre que novamente, com base no ficheiro de entrada que foi indicado o trabalho de codificação. Isso significa que quaisquer alterações que fazemos no xml Infelizmente seriam substituídas.

Para conter o xml de cliplist sejam limpo quando é iniciado um trabalho de codificação, podemos pode gerá-lo novamente sem perder tempo apenas após o início de nosso fluxo de trabalho. Tais ações personalizadas podem ser realizadas por meio do que é chamado de "Component com script". Para obter mais informações, consulte [apresentando o componente com script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Arrastar um componente com script para a superfície de desenho e renomeá-lo para "SetClipListXML."

![Adicionar um componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionar um componente com script*

Quando inspecionar as propriedades do componente com script, os quatro tipos diferentes de script são apresentadas, cada configuráveis para um script diferente.

![Propriedades do componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente com script*

### <a id="frame_based_trim_modify_clip_list"></a>Modificar a lista de clip a partir de um componente com script
Antes, podemos reescrever o xml de cliplist que é gerado durante a inicialização de fluxo de trabalho, vamos precisar ter acesso para a propriedade de xml cliplist e o conteúdo. Podemos fazer isso como este:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Lista de clip entrada que está sendo registrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clip entrada que está sendo registrada*

Primeiro, precisamos de uma forma de determinar de qual ponto até que ponto que queremos cortar o vídeo. Para que isso seja conveniente para o usuário menos técnicas do fluxo de trabalho, publicar duas propriedades para a raiz do gráfico. Para tal, clique com o botão direito na superfície de desenho e selecione "Adicionar propriedade de":

* Primeira propriedade: "ClippingTimeStart" do tipo: "INÍCIO"
* Segunda propriedade: "ClippingTimeEnd" do tipo: "INÍCIO"

![Adicionar a caixa de diálogo de propriedade para a hora de início de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adicionar a caixa de diálogo de propriedade para a hora de início de recorte*

![Publicado a propriedades de tempo de recorte na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicado a propriedades de tempo de recorte na raiz do fluxo de trabalho*

Configure ambas as propriedades para um valor adequado:

![Configurar o início de recorte e propriedades de fim](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurar o início de recorte e propriedades de fim*

Agora, de dentro do nosso script, estamos podem aceder a ambas as propriedades, como este:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Janela de registo que mostra o início e fim de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Janela de registo que mostra o início e fim de recorte*

Vamos analisar as cadeias de caracteres de início numa mais conveniente usar o formulário, com uma simple expressão regular:

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

![Janela de registo com a saída de início analisado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Janela de registo com a saída de início analisado*

Com essas informações em mãos, podemos agora modificar o xml de cliplist para refletir as horas de início e de fim para o recorte de quadro precisos pretendida do filme.

![Código de script para adicionar elementos de compactação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código de script para adicionar elementos de compactação*

Isso era feito por meio de operações de manipulação de cadeia de caracteres normal. O xml de lista de clip modificado resultante é gravado para a propriedade clipListXML na raiz de fluxo de trabalho através do método "setProperty". A janela de registo após a execução de outro teste seria mostrados nos seguintes:

![A lista resultante do clip de registo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*A lista resultante do clip de registo*

Fazer uma execução de teste para ver como os fluxos de áudio e vídeos tem sido recortados. Como fará a execução de teste mais do que uma com diferentes valores para os pontos de remoção, observará que aqueles serão não ser levadas em conta no entanto! A razão disso é que o designer, ao contrário do tempo de execução do Azure, não substitui o xml de cliplist sempre que for executado. Isso significa que apenas na primeira vez que tiver definido a sessão e os pontos, fará com que o xml transformar, todas as outras vezes, nossa cláusula de guarda (se (clipListXML.indexOf ("<trim>") = = -1)) irá impedir que o fluxo de trabalho adicionar outro elemento corte quando existe já um presente.

Para tornar o nosso fluxo de trabalho conveniente para testar localmente, estamos melhor adicionar algum código de manutenção de casa que inspeciona se um elemento de compactação já estava presente. Nesse caso, estamos pode removê-lo antes de continuar, modificando o xml com os novos valores. Em vez de usar manipulações de seqüência de caracteres simples, é provavelmente mais seguro fazer isso por meio do modelo de objeto real xml de análise.

Antes de tal código podemos adicionar embora, vamos precisar de adicionar um número de declarações de importação no início do nosso script primeiro:

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

Este código passa logo acima do ponto em que podemos adicionar os elementos de compactação do XML de cliplist.

Neste ponto, podemos executar e modificar nosso fluxo de trabalho, como o tempo como queremos enquanto tem as alterações aplicadas cada vez.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Adicionar uma propriedade de conveniência ClippingEnabled
Como é sempre recomendável não remover a ocorrer, vamos finalizar nosso fluxo de trabalho, adicionando um sinalizador booleano conveniente que indica se é ou não que Desejamos permitir cortar / recorte.

Como antes, publica uma nova propriedade para a raiz do nosso fluxo de trabalho chamado "ClippingEnabled" do tipo "BOOLEANO".

![Publicado uma propriedade para ativar o recorte de](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicado uma propriedade para ativar o recorte de*

Com o abaixo cláusula de guarda simples, podemos verificar se é necessária a remoção e decidir se nossa lista de clip como tal, precisa de ser modificado ou não.

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

### <a id="code"></a>Código completo

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

## <a name="also-see"></a>Consulte também
[Apresentando o Encoding de multimédia do Azure, serviços Premium](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como utilizar o Encoding de multimédia do Azure, serviços Premium](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificação de conteúdo a pedido com o serviço de multimédia do Azure](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formatos e Codecs de Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-premium-workflow-encoder-formats.md)

[Ficheiros de fluxo de trabalho de exemplo](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta do Explorador dos serviços de multimédia do Azure](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
