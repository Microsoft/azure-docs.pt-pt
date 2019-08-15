---
title: Tutoriais de Media Encoder Premium Workflow avançados
description: Este documento contém instruções explicativas que mostram como executar tarefas avançadas com Media Encoder Premium Workflow e também como criar fluxos de trabalho complexos com Designer de Fluxo de Trabalho.
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
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "69016720"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Tutoriais de Media Encoder Premium Workflow avançados
## <a name="overview"></a>Descrição geral
Este documento contém instruções explicativas que mostram como personalizar fluxos de trabalho com **Designer de fluxo de trabalho**. Você pode encontrar os arquivos de fluxo de trabalho reais [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>ANALÍTICO
Os tópicos a seguir são abordados:

* [Codificando MXF em um MP4 de taxa de bits única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Iniciando um novo fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Usando a entrada do arquivo de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspecionando fluxos de mídia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Adicionando um codificador de vídeo para. Geração de arquivo MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Codificando o fluxo de áudio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexando fluxos de áudio e vídeo em um contêiner MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Gravando o arquivo MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Criando um ativo dos serviços de mídia a partir do arquivo de saída](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testar o fluxo de trabalho concluído localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Codificando MXF em MP4s de múltiplas taxas de bits habilitadas para empacotamento dinâmico](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Adicionando uma ou mais saídas MP4 adicionais](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Configurando os nomes de saída de arquivo](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Adicionando uma faixa de áudio separada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Adicionando o arquivo SMIL "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Codificando MXF em gráficos com múltiplas taxas de bits MP4-avançado](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Visão geral do fluxo de trabalho para aprimorar
  * [Convenções de nomenclatura de arquivo](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publicando Propriedades do componente na raiz do fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Gerar nomes de arquivo de saída com base nos valores de propriedade publicados](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Adicionando miniaturas à saída MP4 de múltiplas taxas de bits](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Visão geral do fluxo de trabalho para adicionar miniaturas
  * [Adicionando codificação JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Lidando com a conversão de espaço de cores](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Escrevendo as miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Detectando erros em um fluxo de trabalho](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Corte baseado em tempo da saída MP4 de múltiplas taxas de bits](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Visão geral do fluxo de trabalho para começar a adicionar corte a](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Usando o corte de fluxo](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Fluxo de trabalho concluído](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Apresentando o componente com script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Criando scripts em um fluxo de trabalho: Olá, mundo](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Corte baseado em quadros de saída MP4 com múltiplas taxas de bits](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Visão geral do Blueprint para começar a adicionar corte a](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Usando o XML da lista de clipes](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Modificando a lista de clipes de um componente com script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Adicionando uma propriedade de conveniência ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Codificando MXF em um MP4 de taxa de bits única
Esta seção demonstra como criar uma taxa de bits única. Arquivo MP4 com o áudio codificado AAC de um. Arquivo de entrada MXF.

### <a id="MXF_to_MP4_start_new"></a>Iniciando um novo fluxo de trabalho
Abra Designer de Fluxo de Trabalho e selecione Arquivo > novo espaço de trabalho > plano gráfico de transcodificação

O novo fluxo de trabalho mostra três elementos:

* Arquivo de origem primário
* XML da lista de clipes
* Arquivo/ativo de saída  

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Novo fluxo de trabalho de codificação*

### <a id="MXF_to_MP4_with_file_input"></a>Usando a entrada do arquivo de mídia
Para aceitar o arquivo de mídia de entrada, um começa com a adição de um componente de entrada de arquivo de mídia. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa do repositório e arraste a entrada desejada para o painel designer. Repita a ação para a entrada do arquivo de mídia e conecte o componente do arquivo de origem primário ao PIN de entrada do nome de arquivo da entrada de arquivos de mídia.

![Entrada do arquivo de mídia conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada do arquivo de mídia conectado*

Inicialmente, identifique um arquivo de exemplo apropriado para usar ao criar um fluxo de trabalho personalizado. Para fazer isso, clique no plano de fundo do painel de designer e procure a propriedade arquivo de origem principal no painel de propriedades à direita. Clique no ícone de pasta e selecione o arquivo desejado para testar o fluxo de trabalho. O componente de entrada do arquivo de mídia inspeciona o arquivo e popula seus PINs de saída para refletir os detalhes do arquivo de exemplo que ele inspecionou.

![Entrada do arquivo de mídia populada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada do arquivo de mídia populada*

Agora que a entrada está populada, a próxima etapa é definir as configurações de codificação de saída. Assim como o arquivo de origem primário foi configurado, agora configure a propriedade da variável da pasta de saída, logo abaixo dela.

![Propriedades de entrada e saída configuradas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Propriedades de entrada e saída configuradas*

### <a id="MXF_to_MP4_streams"></a>Inspecionando fluxos de mídia
Muitas vezes, é desejável saber como o fluxo se parece que ele flui pelo fluxo de trabalho. Para inspecionar um fluxo em qualquer ponto do fluxo de trabalho, basta clicar em uma saída ou PIN de entrada em qualquer um dos componentes. Nesse caso, tente clicar no pino de saída vídeo descompactado da entrada do arquivo de mídia. Uma caixa de diálogo é aberta e permite inspecionar o vídeo de saída.

![Inspecionando o pino de saída do vídeo descompactado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspecionando o pino de saída do vídeo descompactado*

Nesse caso, ele mostra que o vídeo contém uma entrada 1920 x 1080 a 24 quadros por segundo na amostragem de 4:2:2 para um vídeo de quase 2 minutos.

### <a id="MXF_to_MP4_file_generation"></a>Adicionando um codificador de vídeo para. Geração de arquivo MP4
Agora, um vídeo descompactado e vários Pins de saída de áudio não compactados estão disponíveis para uso na entrada do arquivo de mídia. Para codificar o vídeo de entrada, um componente de codificação precisa ser adicionado ao fluxo de trabalho, nesse caso, para gerar. Arquivos MP4.

Para codificar a transmissão de vídeo para H. 264, adicione o componente de codificador de vídeo AVC à superfície do designer. Esse componente usa um fluxo de vídeo descompactar como entrada e entrega um fluxo de vídeo compactado AVC em seu pino de saída.

![Codificador AVC não conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codificador AVC não conectado*

Suas propriedades determinam como a codificação ocorre exatamente. Vamos dar uma olhada em algumas das configurações mais importantes:

* Largura de saída e altura de saída: determina a resolução do vídeo codificado. Nesse caso, 640 x 360 é uma boa configuração.
* Taxa de quadros: quando definida como passagem, ela simplesmente adotará a taxa de quadros de origem, mas será possível substituir isso. Essa conversão de taxa de quadros não é compensada por movimento.
* Perfil e nível: determina o perfil e o nível de AVC. Para obter convenientemente mais informações sobre os diferentes níveis e perfis, clique no ícone de ponto de interrogação no componente de codificador de vídeo AVC e a página de ajuda mostrará mais detalhes sobre cada um dos níveis. Para este exemplo, use o perfil principal no nível 3,2 (o padrão).
* Modo de controle de taxa e taxa de bits (Kbps): nesse cenário, opte por uma saída de taxa de bits constante (CBR) a 1200 kbps
* Formato de vídeo: fornece informações sobre o VUI (informações de usabilidade do vídeo) que são gravados no fluxo H. 264 (informações do lado que podem ser usadas por um decodificador para aprimorar a exibição, mas não para a decodificação correta):
* NTSC (típico para US ou Japão, usando 30 fps)
* PAL (típica para Europa, usando 25 fps)
* Modo de tamanho de GOP: defina o tamanho de GOP fixo para nossos objetivos com um intervalo de chave de 2 segundos com GOPS segundos fechado. A configuração de 2 segundos garante a compatibilidade com o empacotamento dinâmico fornecido pelos serviços de mídia do Azure.

Para alimentar o codificador AVC, conecte o pino de saída vídeo descompactado do componente de entrada do arquivo de mídia ao pino de entrada de vídeo descompactado do codificador AVC.

![Codificador AVC conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Codificador principal AVC conectado*

### <a id="MXF_to_MP4_audio"></a>Codificando o fluxo de áudio
Neste ponto, o fluxo de áudio original não compactado ainda precisa ser compactado. Para compactação do fluxo de áudio, adicione um componente de codificador AAC (Dolby) ao fluxo de trabalho.

![Codificador AVC não conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificador AAC não conectado*

Agora há uma incompatibilidade: há apenas um único PIN de entrada de áudio descompactado do codificador AAC, enquanto mais do que provavelmente a entrada do arquivo de mídia terá dois fluxos de áudio descompactados diferentes disponíveis: um para o canal de áudio à esquerda e outro para a direita. (Se você estiver lidando com Surround Sound, são seis canais.) Portanto, não é possível conectar diretamente o áudio da fonte de entrada do arquivo de mídia ao codificador de áudio AAC. O componente AAC espera um fluxo de áudio "intercalado". um único fluxo que tem os canais esquerdo e direito intercalados entre si. Depois de conhecermos o arquivo de mídia de origem que as faixas de áudio estão em qual posição na origem, podemos gerar tal fluxo de áudio intercalado com as posições de alto-falante atribuídas corretamente à esquerda e à direita.

Primeiro, um deseja gerar um fluxo intercalado dos canais de áudio de origem necessários. O componente intercalador de fluxo de áudio lida com isso para nós. Adicione-o ao fluxo de trabalho e conecte as saídas de áudio da entrada do arquivo de mídia a ele.

![Intercalador de fluxo de áudio conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Intercalador de fluxo de áudio conectado*

Agora que temos um fluxo de áudio Intercalado, ainda não especificamos para onde atribuir as posições de alto-falantes à esquerda ou à direita. Para especificar isso, podemos aproveitar o atribuidor de posição de alto-falante.

![Adicionando um atribuidor de posição de orador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Adicionando um atribuidor de posição de orador*

Configure o atribuidor de posição do alto-falante para uso com um fluxo de entrada estéreo por meio de um filtro de predefinição de codificador de "personalizado" e a predefinição de canal chamada "2,0 (L, R)". (Isso atribui a posição do alto-falante esquerdo ao canal 1 e a posição do alto-falante direito ao canal 2.)

Conecte a saída do atribuidor de posição do orador à entrada do codificador AAC. Em seguida, diga ao codificador AAC para trabalhar com uma predefinição de canal "2,0 (L, R)", portanto, ele sabe lidar com áudio estéreo como entrada.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexando fluxos de áudio e vídeo em um contêiner MP4
Considerando nosso fluxo de vídeo codificado AVC e nosso fluxo de áudio codificado AAC, podemos capturar ambos em um. Contêiner MP4. O processo de misturar fluxos diferentes em um único é chamado de "multiplexação" (ou "muxing"). Nesse caso, estamos intercalando o áudio e os fluxos de vídeo em um único coerente. Pacote MP4. O componente que coordena isso para um. O contêiner MP4 é chamado de multiplexador ISO MPEG-4. Adicione um à superfície do designer e conecte o codificador de vídeo AVC e o codificador AAC às suas entradas.

![Multiplexador MPEG4 conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Multiplexador MPEG4 conectado*

### <a id="MXF_to_MP4_writing_mp4"></a>Gravando o arquivo MP4
Ao gravar um arquivo de saída, o componente de saída do arquivo é usado. Podemos conectar isso à saída do Multiplexador ISO MPEG-4 para que sua saída seja gravada no disco. Para fazer isso, conecte o pino de saída do contêiner (MPEG-4) ao pino de entrada de gravação da saída do arquivo.

![Saída do arquivo conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Saída do arquivo conectado*

O nome de arquivo usado é determinado pela propriedade File. Embora essa propriedade possa ser codificada para um determinado valor, é mais provável que um queira defini-la por meio de uma expressão.

Para que o fluxo de trabalho determine automaticamente a propriedade nome do arquivo de saída de uma expressão, clique no botão ao lado do nome do arquivo (ao lado do ícone de pasta). No menu suspenso, selecione "expressão". Isso abre o editor de expressão. Limpe primeiro o conteúdo do editor.

![Editor de expressão vazio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de expressão vazio*

O editor de expressão permite que você insira qualquer valor literal, misto com uma ou mais variáveis. As variáveis começam com um cifrão. À medida que você pressiona a tecla $, o editor mostra uma caixa suspensa com uma opção de variáveis disponíveis. Em nosso caso, usaremos uma combinação da variável do diretório de saída e da variável de nome do arquivo de entrada de base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Editor de expressão preenchido](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Editor de expressão preenchido*

> [!NOTE]
> Para ver um arquivo de saída do seu trabalho de codificação no Azure, você deve fornecer um valor no editor de expressão.
>
>

Quando você confirma a expressão pressionando OK, a janela de propriedades visualiza o valor que a propriedade File resolve nesse momento.

![A expressão do arquivo resolve o dir de saída](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*A expressão do arquivo resolve o dir de saída*

### <a id="MXF_to_MP4_asset_from_output"></a>Criando um ativo dos serviços de mídia a partir do arquivo de saída
Embora tenhamos escrito um arquivo de saída MP4, ainda precisamos indicar que esse arquivo pertence ao ativo de saída que os serviços de mídia geram como resultado da execução desse fluxo de trabalho. Para esse fim, o nó arquivo/ativo de saída na tela do fluxo de trabalho é usado. Todos os arquivos de entrada nesse nó fazem parte do ativo dos serviços de mídia do Azure resultante.

Conecte o componente saída de arquivo ao componente arquivo/ativo de saída para concluir o fluxo de trabalho.

![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Fluxo de trabalho concluído*

### <a id="MXF_to_MP4_test"></a>Testar o fluxo de trabalho concluído localmente
Para testar o fluxo de trabalho localmente, clique no botão reproduzir na barra de ferramentas na parte superior. Quando o fluxo de trabalho terminar a execução, inspecione a saída gerada na pasta de saída configurada. Você verá o arquivo de saída MP4 concluído que foi codificado do arquivo de origem de entrada MXF.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Codificando MXF em MP4-empacotamento dinâmico de múltiplas taxas de bits habilitado
Este tutorial cria um conjunto de arquivos MP4 com múltiplas taxas de bits com áudio codificado AAC de um único. Arquivo de entrada MXF.

Quando uma saída de ativo de várias taxas de bits for desejada para uso em combinação com os recursos de empacotamento dinâmico oferecidos pelos serviços de mídia do Azure, vários arquivos MP4 alinhados a GOP de cada taxa de bits e resolução diferentes precisarão ser gerados. Para fazer isso, a [codificação MXF em uma instrução MP4 de taxa de bits única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) nos fornece um bom ponto de partida.

![Iniciando fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Iniciando fluxo de trabalho*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Adicionando uma ou mais saídas MP4 adicionais
Todo arquivo MP4 em nosso ativo resultante dos serviços de mídia do Azure dá suporte a uma taxa de bits e resolução diferentes. Vamos adicionar um ou mais arquivos de saída MP4 ao fluxo de trabalho.

Para ter certeza de que temos todos os nossos codificadores de vídeo criados com as mesmas configurações, é mais conveniente duplicar o codificador de vídeo AVC já existente e configurar outra combinação de resolução e taxa de bits (vamos adicionar um de 960 x 540 a 25 quadros por segundo em 2,5 Mbps ). Para duplicar o codificador existente, copie cole-o na superfície do designer.

Conecte o pino de saída vídeo descompactado da entrada do arquivo de mídia ao nosso novo componente AVC.

![Segundo codificador AVC conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Segundo codificador AVC conectado*

Agora, adapte a configuração para nosso novo codificador AVC para gerar 960 x 540 a 2,5 Mbps. (Use suas propriedades "largura da saída", "altura da saída" e "taxa de bits (Kbps)" para isso.)

Como queremos usar o ativo resultante junto com o empacotamento dinâmico dos serviços de mídia do Azure, o ponto de extremidade de streaming precisa ser capaz de gerar a partir desses arquivos MP4 HLS/fragmentos de MP4/DASH fragmentados que são exatamente alinhados entre si de forma que Os clientes que estão alternando entre taxas de bits diferentes obtêm uma experiência contínua de vídeo e áudio simples. Para fazer isso acontecer, precisamos garantir que, nas propriedades de ambos os codificadores AVC, o tamanho do GOP ("grupo de imagens") para ambos os arquivos MP4 seja definido como 2 segundos, o que pode ser feito por:

* definindo o modo de tamanho de GOP como fixo GOP tamanho e
* o intervalo de quadros chave para dois segundos.
* Além disso, defina o controle IDR GOP como GOP fechado para garantir que todos os GOPS segundos estejam sozinhos por conta própria, sem dependências

Para facilitar a compreensão desse fluxo de trabalho, renomeie o primeiro codificador AVC como "codificador de vídeo AVC 640 x 360 1200 kbps" e o segundo codificador AVC "codificador de vídeo AVC 960 x 540 2500 kbps".

Agora, adicione um segundo multiplexador ISO MPEG-4 e uma segunda saída de arquivo. Conecte o multiplexador ao novo codificador AVC e certifique-se de que sua saída seja direcionada para a saída do arquivo. Em seguida, conecte também a saída do codificador de áudio AAC à entrada do novo Multiplexador. A saída do arquivo, por sua vez, pode ser conectada ao nó arquivo/ativo de saída para adicioná-lo ao ativo dos serviços de mídia que será criado.

![Segundo muxer e saída de arquivo conectada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Segundo muxer e saída de arquivo conectada*

Para compatibilidade com o empacotamento dinâmico dos serviços de mídia do Azure, configure o modo de bloco do Multiplexador para contagem ou duração de GOP e defina o GOPS segundos por parte como 1. (Deve ser o padrão.)

![Modos de bloco muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos de bloco muxer*

Observação: Talvez você queira repetir esse processo para quaisquer outras combinações de taxa de bits e resolução que você deseja adicionar à saída do ativo.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurando os nomes de saída de arquivo
Temos mais de um único arquivo adicionado ao ativo de saída. Isso fornece a necessidade de garantir que os nomes de arquivo de cada um dos arquivos de saída sejam diferentes uns dos outros e, talvez, até mesmo apliquem uma Convenção de nomenclatura de arquivos, de modo que fique claro do nome do arquivo com o qual você está lidando.

A nomeação de saída de arquivo pode ser controlada por meio de expressões no designer. Abra o painel de propriedades de um dos componentes de saída do arquivo e abra o editor de expressão para a propriedade File. Nosso primeiro arquivo de saída foi configurado por meio da expressão a seguir (consulte o tutorial para ir de [MXF para uma saída MP4 de taxa de bits única](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Isso significa que nosso nome de arquivo é determinado por duas variáveis: o diretório de saída para gravar e o nome de base do arquivo de origem. O primeiro é exposto como uma propriedade na raiz do fluxo de trabalho e o último é determinado pelo arquivo de entrada. O diretório de saída é o que você usa para teste local; Essa propriedade será substituída pelo mecanismo de fluxo de trabalho quando o fluxo de trabalho for executado pelo processador de mídia baseado em nuvem nos serviços de mídia do Azure.
Para dar a nossos arquivos de saída uma nomenclatura de saída consistente, altere a primeira expressão de nomenclatura de arquivo para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e o segundo a:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Execute uma execução de teste intermediária para garantir que ambos os arquivos de saída MP4 sejam gerados corretamente.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Adicionando uma faixa de áudio separada
Como veremos posteriormente quando gerarmos um arquivo. ISM para ir com nossos arquivos de saída MP4, também precisaremos de um arquivo MP4 somente de áudio como a faixa de áudio para nosso streaming adaptável. Para criar esse arquivo, adicione um muxer adicional ao fluxo de trabalho (multiplexador ISO-MPEG-4) e conecte o pino de saída do codificador AAC com seu pino de entrada para Track 1.

![Muxer de áudio adicionado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Muxer de áudio adicionado*

Crie um terceiro componente de saída de arquivo para gerar o fluxo de saída do muxer e configurar a expressão de nomenclatura de arquivo como:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Saída de muxer de áudio criando arquivo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Saída de muxer de áudio criando arquivo*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Adicionando o. Arquivo SMIL do ISM
Para que o empacotamento dinâmico funcione em combinação com ambos os arquivos MP4 (e o MP4 somente de áudio) em nosso ativo de serviços de mídia, também precisamos de um arquivo de manifesto (também chamado de arquivo "SMIL": Linguagem de integração multimídia sincronizada). Esse arquivo indica aos serviços de mídia do Azure quais arquivos MP4 estão disponíveis para o empacotamento dinâmico e quais deles devem ser considerados para o streaming de áudio. Um arquivo de manifesto típico para um conjunto de Mp4s com um único fluxo de áudio tem esta aparência:

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

O arquivo. ISM contém uma instrução switch, uma referência a cada um dos arquivos de vídeo MP4 individuais e também a um (ou mais) referências de arquivo de áudio a um MP4 que contém apenas o áudio.

A geração do arquivo de manifesto para nosso conjunto de Mp4s pode ser feita por meio de um componente chamado "gravador de manifesto AMS". Para usá-lo, arraste-o para a superfície e conecte os Pins de saída "Write Complete" dos três componentes de saída do arquivo à entrada do gravador do manifesto do AMS. Em seguida, certifique-se de conectar a saída do gravador do manifesto do AMS ao arquivo/ativo de saída.

Assim como em nossos outros componentes de saída de arquivo, configure o nome de saída do arquivo. ISM com uma expressão:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Nosso fluxo de trabalho concluído é semelhante ao seguinte:

![Concluído o MXF para o fluxo de trabalho MP4 de múltiplas taxas de bits](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Concluído o MXF para o fluxo de trabalho MP4 de múltiplas taxas de bits*

## <a id="MXF_to__multibitrate_MP4"></a>Codificando MXF em gráficos com múltiplas taxas de bits MP4-avançado
No [passo a passos do fluxo de trabalho anterior](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) , vimos como um único ativo de entrada MXF pode ser convertido em um ativo de saída com arquivos MP4 com várias taxas de bits, um arquivo MP4 somente de áudio e um arquivo de manifesto para uso em conjunto com o empacotamento dinâmico dos serviços de mídia do Azure.

Este tutorial mostra como alguns dos aspectos podem ser aprimorados e tornam-se mais convenientes.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para aprimorar
![Fluxo de trabalho MP4 de múltiplas taxas de bits para aprimorar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Fluxo de trabalho MP4 de múltiplas taxas de bits para aprimorar*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenções de nomenclatura de arquivo
No fluxo de trabalho anterior, especificamos uma expressão simples como a base para gerar nomes de arquivo de saída. No entanto, temos uma duplicação: todos os componentes de arquivo de saída individuais especificavam essa expressão.

Por exemplo, nosso componente de saída de arquivo para o primeiro arquivo de vídeo é configurado com esta expressão:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Enquanto para o segundo vídeo de saída, temos uma expressão como:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Não seria mais claro, menos propenso a erros e mais conveniente se pudéssemos remover algumas dessas duplicações e tornar as coisas mais configuráveis? Felizmente, podemos: os recursos de expressão do designer em combinação com a capacidade de criar propriedades personalizadas em nossa raiz de fluxo de trabalho fornecerão uma camada de conveniência adicional.

Vamos supor que vamos orientar a configuração de nome de arquivo das taxas de bits dos arquivos MP4 individuais. Essas taxas de bits que Visaremos configurar em um local central (na raiz do nosso grafo), de onde serão acessadas para configurar e orientar a geração de nome de arquivo. Para fazer isso, começamos publicando a propriedade de taxa de bits de ambos os codificadores AVC para a raiz de nosso fluxo de trabalho, para que ele se torne acessível a partir da raiz, bem como dos codificadores AVC. (Mesmo que seja exibido em dois pontos diferentes, há apenas um valor subjacente.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Publicando Propriedades do componente na raiz do fluxo de trabalho
Abra o primeiro codificador AVC, vá para a propriedade taxa de bits (Kbps) e, na lista suspensa, escolha publicar.

![Publicando a propriedade de taxa de bits](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicando a propriedade de taxa de bits*

Configure a caixa de diálogo Publicar para publicar na raiz do nosso grafo de fluxo de trabalho, com um nome publicado de "video1bitrate" e um nome de exibição legível de "taxa de bits de vídeo 1". Configure um nome de grupo personalizado chamado "taxas de bits de streaming" e clique em publicar.

![Publicando a propriedade de taxa de bits](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Caixa de diálogo de publicação para propriedade de taxa de bits*

Repita o mesmo para a propriedade taxa de bits do segundo codificador AVC e nomeie-o como "video2bitrate" com um nome de exibição de "taxa de bits de vídeo 2", no mesmo grupo personalizado "taxas de bits de streaming".

Se agora inspecionamos as propriedades raiz do fluxo de trabalho, veremos nosso grupo personalizado com as duas propriedades publicadas exibidas. Ambos estão refletindo o valor de sua respectiva taxa de bits de codificador AVC.

![Propriedades de taxa de bits publicadas na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Sempre que quisermos acessar essas propriedades do código ou de uma expressão, podemos fazer isso da seguinte maneira:

* do código embutido de um componente logo abaixo da raiz: node. getPropertyAsString ('.. /video1bitrate ', NULL)
* dentro de uma expressão: $ {ROOT_video1bitrate}

Vamos concluir o grupo "taxas de bits de streaming" publicando nossa taxa de bits de faixa de áudio também. Nas propriedades do codificador AAC, pesquise a configuração de taxa de bits e selecione publicar na lista suspensa ao lado dela. Publique na raiz do grafo com o nome "audio1bitrate" e o nome de exibição "áudio 1 taxa de bits" em nosso grupo personalizado "taxas de bits de streaming".

![Caixa de diálogo de publicação para taxa de bits de áudio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Caixa de diálogo de publicação para taxa de bits de áudio*

![Propriedades de vídeo e áudio resultantes na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Propriedades de vídeo e áudio resultantes na raiz*

A alteração de qualquer um desses três valores também reconfigura e altera os valores nos respectivos componentes aos quais eles estão vinculados (e onde são publicados).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Gerar nomes de arquivo de saída com base nos valores de propriedade publicados
Em vez de codificar nossos nomes de arquivos gerados, agora podemos alterar nossa expressão de nome de arquivo em cada um dos componentes de saída de arquivo para contar com as propriedades de taxa de bits publicadas na raiz do grafo. Começando com nossa primeira saída de arquivo, localize a propriedade File e edite a expressão como esta:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Os parâmetros diferentes nessa expressão podem ser acessados e inseridos pressionando-se o sinal de dólar no teclado enquanto estiver na janela de expressão. Um dos parâmetros disponíveis é nossa propriedade video1bitrate que publicamos anteriormente.

![Acessando parâmetros dentro de uma expressão](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Acessando parâmetros dentro de uma expressão*

Faça o mesmo para a saída do arquivo para nosso segundo vídeo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e para a saída do arquivo somente de áudio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se agora alterarmos a taxa de bits para qualquer um dos arquivos de vídeo ou áudio, o respectivo codificador será reconfigurado e a Convenção de nome de arquivo com base na taxa de bits será respeitada automaticamente.

## <a id="thumbnails_to__multibitrate_MP4"></a>Adicionando miniaturas à saída MP4 de múltiplas taxas de bits
A partir de um fluxo de trabalho que gera [uma saída MP4 de múltiplas taxas de bits de uma entrada MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), agora vamos procurar adicionar miniaturas à saída.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Visão geral do fluxo de trabalho para adicionar miniaturas
![Fluxo de trabalho MP4 com múltiplas taxas de bits para iniciar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Fluxo de trabalho MP4 com múltiplas taxas de bits para iniciar*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Adicionando codificação JPG
O coração de nossa geração de miniatura será o componente de codificador JPG, capaz de gerar arquivos JPG.

![Codificador de JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codificador de JPG*

No entanto, não podemos conectar diretamente nosso fluxo de vídeo descompactado da entrada do arquivo de mídia no codificador JPG. Em vez disso, espera-se que sejam entregues quadros individuais. Isso pode ser feito por meio do componente de porta do quadro de vídeo.

![Conectando um portão de quadro ao codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Conectando um portão de quadro ao codificador JPG*

O portão de quadro uma vez a cada muitos segundos ou quadros permite que um quadro de vídeo passe. O intervalo e o deslocamento de tempo com o qual isso acontece é configurável nas propriedades.

![Propriedades do portão de quadro de vídeo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propriedades do portão de quadro de vídeo*

Vamos criar uma miniatura a cada minuto definindo o modo como time (segundos) e o intervalo como 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Lidando com a conversão de espaço de cores
Embora pareçam lógicos ambos os pinos de vídeo não compactados do portão de quadro e a entrada do arquivo de mídia agora possa ser conectada, receberemos um aviso se o fizermos.

![Erro de espaço de cor de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Erro de espaço de cor de entrada*

Isso ocorre porque a maneira como as informações de cores são representadas em nosso fluxo de vídeo não compactado original, proveniente de nosso MXF, é diferente do que o codificador de JPG está esperando. Mais especificamente, um chamado "espaço de cores" de "RGB" ou "tons de cinza" deve fluir. Isso significa que o fluxo de vídeo de entrada do portão de quadros de vídeo precisa ter uma conversão aplicada em relação a seu espaço de cor primeiro.

Arraste para o fluxo de trabalho o conversor de espaço de cor-Intel e conecte-o ao nosso portão de quadro.

![Conectando um conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Conectando um conversor de espaço de cor*

Na janela Propriedades, escolha a entrada BGR 24 na lista predefinida.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Escrevendo as miniaturas
Diferente de nossos vídeos MP4, o componente de codificador JPG gera mais de um arquivo. Para lidar com isso, um componente de gravador de arquivo JPG de pesquisa de cena pode ser usado: ele usa as miniaturas de JPG de entrada e as grava, cada nome de arquivo que está sendo sufixado por um número diferente. (O número geralmente indica o número de segundos/unidades no fluxo do qual a miniatura foi extraída.)

![Introdução ao gravador de arquivo JPG de pesquisa de cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introdução ao gravador de arquivo JPG de pesquisa de cena*

Configure a propriedade de caminho da pasta de saída com a expressão: $ {ROOT_outputWriteDirectory}

e a propriedade de prefixo filename com:

    ${ROOT_sourceFileBaseName}_thumb_

O prefixo determina como os arquivos de miniatura estão sendo nomeados. Eles têm um sufixo com um número que indica a posição do polegar no fluxo.

![Propriedades do gravador de arquivo JPG de pesquisa de cena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propriedades do gravador de arquivo JPG de pesquisa de cena*

Conecte o gravador de arquivo JPG de pesquisa de cena ao nó arquivo/ativo de saída.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Detectando erros em um fluxo de trabalho
Conecte a entrada do conversor de espaço de cor à saída de vídeo não compactada bruta. Agora, execute uma execução de teste local para o fluxo de trabalho. Há uma boa chance de que o fluxo de trabalho interrompa repentinamente a execução e indique com um contorno vermelho no componente que encontrou um erro:

![Erro no conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Erro no conversor de espaço de cor*

Clique no pequeno ícone "E" vermelho no canto superior direito do componente conversor de espaço de cor para ver qual é o motivo pelo qual a tentativa de codificação falhou.

![Caixa de diálogo de erro do conversor de espaço de cor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Caixa de diálogo de erro do conversor de espaço de cor*

Acontece, como você pode ver, que o padrão de espaço de cores de entrada para o conversor de espaço de cor deve ser rec601 para nossa conversão solicitada de YUV para RGB. Aparentemente, nosso fluxo não indica seu rec601. (REC 601 é um padrão para codificação de sinais de vídeo analógicos entrelaçados no formato de vídeo digital. Ele especifica uma região ativa que cobre exemplos de luminância de 720 e 360 amostras de crominância por linha. O sistema de codificação de cores é conhecido como YCbCr 4:2:2.)

Para corrigir isso, indicaremos nos metadados do nosso fluxo que estamos lidando com o conteúdo do rec601. Para isso, usaremos um componente atualizador de tipo de dados de vídeo, que colocaremos entre nossa fonte bruta e o componente de conversão de espaço de cor. Esse atualizador de tipo de dados permite a atualização manual de determinadas propriedades de tipo de dados de vídeo. Configure-o para indicar um padrão de espaço de cor de "REC 601". Isso faz com que o Atualizador de tipo de dados de vídeo marque o fluxo com o espaço de cores "REC 601" se ainda não havia nenhum espaço de cores definido. (Ele não substituirá os metadados existentes, a menos que a caixa de seleção substituir esteja marcada.)

![Atualizando o padrão de espaço de cores no atualizador de tipo de dados](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Atualizando o padrão de espaço de cores no atualizador de tipo de dados*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Fluxo de trabalho concluído
Agora que o fluxo de trabalho foi concluído, faça outra execução de teste para vê-lo passar.

![Fluxo de trabalho concluído para saída de vários MP4 com miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Fluxo de trabalho concluído para saída de vários MP4 com miniaturas*

## <a id="time_based_trim"></a>Corte baseado em tempo da saída MP4 de múltiplas taxas de bits
A partir de um fluxo de trabalho que gera [uma saída MP4 de múltiplas taxas de bits de uma entrada MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), agora vamos examinar a corte do vídeo de origem com base em carimbos de data/hora.

### <a id="time_based_trim_start"></a>Visão geral do fluxo de trabalho para começar a adicionar corte a
![Iniciando o fluxo de trabalho para adicionar corte a](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Iniciando o fluxo de trabalho para adicionar corte a*

### <a id="time_based_trim_use_stream_trimmer"></a>Usando o corte de fluxo
O componente de corte de fluxo permite que você corte o início e o término de um fluxo de entrada base em informações de tempo (segundos, minutos,...). O corte não oferece suporte a corte baseado em quadro.

![Corte de fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Corte de fluxo*

Em vez de vincular os codificadores AVC e o atribuidor de posição do orador à entrada do arquivo de mídia diretamente, colocaremos entre eles o corte de fluxo. (Um para o sinal de vídeo e outro para o sinal de áudio intercalado.)

![Colocar corte de fluxo entre](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Colocar corte de fluxo entre*

Vamos configurar o corte para que possamos processar apenas vídeo e áudio entre 15 segundos e 60 segundos no vídeo.

Vá para as propriedades do corte de fluxo de vídeo e configure as propriedades de hora de início (15 s) e hora de término (60 s). Para garantir que ambos os cortes de áudio e vídeo estejam sempre configurados com os mesmos valores de início e término, publicamos esses na raiz do fluxo de trabalho.

![Publicar a propriedade de hora de início do corte de fluxo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicar a propriedade de hora de início do corte de fluxo*

![Publicar a caixa de diálogo de propriedade para a hora de início](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicar a caixa de diálogo de propriedade para a hora de início*

![Caixa de diálogo Publicar propriedade para hora de término](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Caixa de diálogo Publicar propriedade para hora de término*

Se agora inspecionamos a raiz de nosso fluxo de trabalho, ambas as propriedades são exibidas e configuráveis de lá.

![Propriedades publicadas disponíveis na raiz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Propriedades publicadas disponíveis na raiz*

Agora, abra as propriedades de corte do corte de áudio e configure os horários de início e término com uma expressão que se refere às propriedades publicadas na raiz de nosso fluxo de trabalho.

Para a hora de início do corte de áudio:

    ${ROOT_TrimmingStartTime}

e para sua hora de término:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Fluxo de trabalho concluído
![Fluxo de trabalho concluído](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Fluxo de trabalho concluído*

## <a id="scripting"></a>Apresentando o componente com script
Os componentes com script podem executar scripts arbitrários durante as fases de execução de nosso fluxo de trabalho. Há quatro scripts diferentes que podem ser executados, cada um com características específicas e seu próprio local no ciclo de vida do fluxo de trabalho:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

A documentação do componente com script apresenta mais detalhes para cada um dos itens acima. Na [seção a seguir](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), o componente de script **realizeScript** é usado para construir um XML de cliplist imediatamente quando o fluxo de trabalho é iniciado. Esse script é chamado durante a configuração do componente, que ocorre apenas uma vez em seu ciclo de vida.

### <a id="scripting_hello_world"></a>Criando scripts em um fluxo de trabalho: Olá, mundo
Arraste um componente com script para a superfície do designer e renomeie-o (por exemplo, "Definirxmldelistadeclipes").

![Adicionando um componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionando um componente com script*

Quando você inspeciona as propriedades do componente com script, os quatro tipos de script diferentes são mostrados, cada um configurável para um script diferente.

![Propriedades do componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente com script*

Limpe o processInputScript e abra o editor para o realizeScript. Agora, estamos configurados e prontos para começar a criar scripts.

Os scripts são escritos em Groovy, uma linguagem de script compilada dinamicamente para a plataforma Java que mantém a compatibilidade com o Java. Na verdade, a maior parte do código Java é um código do Groovy válido.

Vamos escrever um script de Groovy Hello World simples no contexto de nosso realizeScript. Insira o seguinte no editor:

    node.log("hello world");

Agora execute uma execução de teste local. Após essa execução, inspecione (por meio da guia sistema no componente com script) a propriedade logs.

![Saída de log do Hello World](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Saída de log do Hello World*

O objeto node no qual chamamos o método log, refere-se ao nosso "nó" atual ou ao componente no qual estamos criando scripts. Cada componente, como tal, tem a capacidade de gerar dados de log, disponíveis por meio da guia sistema. Nesse caso, vamos gerar a cadeia de caracteres literal "Olá, mundo". Importante entender aqui é que isso pode provar ser uma ferramenta de depuração inestimável, fornecendo a você informações sobre o que o script realmente está fazendo.

De dentro de nosso ambiente de script, também temos acesso a propriedades em outros componentes. Experimente isto:

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

Nossa janela de log mostra o seguinte:

![Saída de log para acessar caminhos de nó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Saída de log para acessar caminhos de nó*

## <a id="frame_based_trim"></a>Corte baseado em quadros de saída MP4 com múltiplas taxas de bits
A partir de um fluxo de trabalho que gera [uma saída MP4 de múltiplas taxas de bits de uma entrada MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), agora vamos examinar a corte do vídeo de origem com base em contagens de quadros.

### <a id="frame_based_trim_start"></a>Visão geral do Blueprint para começar a adicionar corte a
![Fluxo de trabalho para começar a adicionar corte a](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Fluxo de trabalho para começar a adicionar corte a*

### <a id="frame_based_trim_clip_list"></a>Usando o XML da lista de clipes
Em todos os tutoriais de fluxo de trabalho anteriores, usamos o componente de entrada do arquivo de mídia como nossa fonte de entrada de vídeo. No entanto, para esse cenário específico, vamos usar o componente de origem da lista de clipes. Essa não deve ser a maneira preferida de trabalhar; Use apenas a origem da lista de clipes quando houver um motivo real para fazer isso (como no caso a seguir, no qual estamos usando os recursos de corte da lista de clipes).

Para alternar da entrada do arquivo de mídia para a origem da lista de clipes, arraste o componente origem da lista de clipes para a superfície de design e conecte o PIN XML da lista de clipes ao nó XML da lista de clips do designer de fluxo de trabalho. Isso popula a fonte da lista de clipes com Pins de saída, de acordo com nosso vídeo de entrada. Agora, conecte o vídeo descompactado e os Pins de áudio descompactados da origem da lista de clipes aos respectivos codificadores AVC e intercalador de fluxo de áudio. Agora, remova a entrada do arquivo de mídia.

![A entrada do arquivo de mídia foi substituída pela origem da lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*A entrada do arquivo de mídia foi substituída pela origem da lista de clipes*

O componente de origem da lista de clipes usa como entrada um "XML da lista de clipes". Ao selecionar o arquivo de origem para testar localmente, este XML de lista de clipes é preenchido automaticamente para você.

![Propriedade XML da lista de clipes preenchida automaticamente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propriedade XML da lista de clipes preenchida automaticamente*

Olhando um pouco mais para o XML, é assim que se parece com:

![Caixa de diálogo Editar lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Caixa de diálogo Editar lista de clipes*

No entanto, isso não reflete os recursos do XML da lista de clipes. Uma opção que temos é adicionar um elemento "Trim" sob a fonte de vídeo e áudio, assim:

![Adicionando um elemento Trim à lista de clipes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Adicionando um elemento Trim à lista de clipes*

Se você modificar o XML da lista de clipes como mostrado acima e executar uma execução de teste local, verá o vídeo corretamente cortado entre 10 e 20 segundos no vídeo.

Ao contrário do que acontece quando você faz uma execução local, esse mesmo XML de cliplist não teria o mesmo efeito quando aplicado em um fluxo de trabalho executado nos serviços de mídia do Azure. Quando o codificador Premium do Azure é iniciado, o XML de cliplist é gerado toda vez, com base no arquivo de entrada que o trabalho de codificação foi fornecido. Isso significa que todas as alterações que fazemos no XML, infelizmente, seriam substituídas.

Para combater o XML de cliplist sendo apagado quando um trabalho de codificação é iniciado, podemos gerá-lo novamente imediatamente após o início de nosso fluxo. Essas ações personalizadas podem ser tomadas por meio do que é chamado de "componente com script". Para obter mais informações, consulte [introdução ao componente com script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Arraste um componente com script para a superfície do designer e renomeie-o como "Definirxmldelistadeclipes".

![Adicionando um componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Adicionando um componente com script*

Quando você inspeciona as propriedades do componente com script, os quatro tipos de script diferentes são mostrados, cada um configurável para um script diferente.

![Propriedades do componente com script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propriedades do componente com script*

### <a id="frame_based_trim_modify_clip_list"></a>Modificando a lista de clipes de um componente com script
Antes que possamos reescrever o XML de cliplist gerado durante a inicialização do fluxo de trabalho, precisaremos ter acesso à propriedade e ao conteúdo do XML de cliplist. Podemos fazer isso da seguinte maneira:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Lista de clipes de entrada sendo registrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clipes de entrada sendo registrada*

Primeiro, precisamos de uma maneira de determinar a partir de qual ponto deseja cortar o vídeo. Para tornar isso conveniente para o usuário menos técnico do fluxo de trabalho, publique duas propriedades na raiz do grafo. Para fazer isso, clique com o botão direito do mouse na superfície do designer e selecione "Adicionar Propriedade":

* Primeira propriedade: "ClippingTimeStart" do tipo: CÓDIGO
* Segunda Propriedade: "ClippingTimeEnd" do tipo: CÓDIGO

![Adicionar caixa de diálogo de propriedade para hora de início do recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adicionar caixa de diálogo de propriedade para hora de início do recorte*

![Propriedades de hora de recorte publicadas na raiz do fluxo de trabalho](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Propriedades de hora de recorte publicadas na raiz do fluxo de trabalho*

Configure as duas propriedades para um valor adequado:

![Configurar as propriedades de início e término do recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurar as propriedades de início e término do recorte*

Agora, de dentro de nosso script, podemos acessar ambas as propriedades, desta forma:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Janela de log mostrando o início e o término do recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Janela de log mostrando o início e o término do recorte*

Vamos analisar as cadeias de código de code em um formulário mais conveniente para usar, usando uma expressão regular simples:

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

Com essas informações em mãos, agora podemos modificar o XML de cliplist para refletir as horas de início e de término para o recorte preciso de quadro desejado do filme.

![Código de script para adicionar elementos Trim](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código de script para adicionar elementos Trim*

Isso foi feito por meio de operações normais de manipulação de cadeia de caracteres. O XML da lista de clipes modificada resultante é gravado novamente na propriedade clipListXML na raiz do fluxo de trabalho por meio do método "SetProperty". A janela de log após outra execução de teste nos mostraria o seguinte:

![Registrando em log a lista de clipes resultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registrando em log a lista de clipes resultante*

Execute um teste para ver como os fluxos de áudio e vídeo foram cortados. Como você fará mais de um teste-executar com valores diferentes para os pontos de corte, você observará que eles não serão levados em conta no entanto! O motivo disso é que o designer, ao contrário do tempo de execução do Azure, não substitui o XML de cliplist a cada execução. Isso significa que somente a primeira vez que você definir os pontos de entrada e saída fará com que o XML seja transformado, todas as outras vezes, nossa cláusula de`clipListXML.indexOf("<trim>") == -1`proteção (if ()) impedirá que o fluxo de trabalho Adicione outro elemento Trim quando já houver um presente.

Para tornar nosso fluxo de trabalho conveniente para teste localmente, adicionamos melhor um código de manutenção de casa que inspeciona se um elemento Trim já estava presente. Nesse caso, podemos removê-lo antes de continuar modificando o XML com os novos valores. Em vez de usar manipulações de cadeia de caracteres simples, é provável que isso seja mais seguro por meio da análise de modelo de objeto XML real.

No entanto, antes de adicionarmos esse código, precisaremos adicionar várias instruções Import ao início do nosso script primeiro:

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

Esse código fica logo acima do ponto no qual adicionamos os elementos Trim ao XML de cliplist.

Neste ponto, podemos executar e modificar nosso fluxo de trabalho tanto quanto desejarmos enquanto as alterações são aplicadas sempre.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Adicionando uma propriedade de conveniência ClippingEnabled
Como você talvez nem sempre queira que o corte aconteça, vamos concluir nosso fluxo de trabalho adicionando um sinalizador booliano conveniente que indica se queremos ou não habilitar a corte/recorte.

Como antes, publique uma nova propriedade na raiz do nosso fluxo de trabalho chamado "ClippingEnabled" do tipo "BOOLIANo".

![Publicou uma propriedade para habilitar o recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicou uma propriedade para habilitar o recorte*

Com a cláusula de proteção simples abaixo, podemos verificar se o corte é necessário e decidir se nossa lista de clipes deve ser modificada ou não.

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
[Introdução à codificação Premium nos serviços de mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como usar a codificação Premium nos serviços de mídia do Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificando conteúdo sob demanda com o serviço de mídia do Azure](media-services-encode-asset.md#media-encoder-premium-workflow)

[Formatos e Codecs de Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-premium-workflow-encoder-formats.md)

[Arquivos de fluxo de trabalho de exemplo](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta Azure Media Services Explorer](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
