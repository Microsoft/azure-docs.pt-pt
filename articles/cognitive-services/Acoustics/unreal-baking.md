---
title: Tutorial do projeto Acoustics Unreal criar
titlesuffix: Azure Cognitive Services
description: Este documento descreve o processo de submissão de um criar acoustics usando a extensão de Unreal editor.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: michem
ms.openlocfilehash: afe4421bea27ff029bd4a1a7808241a54027a6ac
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136396"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Tutorial do projeto Acoustics Unreal criar
Este documento descreve o processo de submissão de um criar acoustics usando a extensão de Unreal editor.

Existem cinco passos para fazer uma criar:

1. Criar ou marcar sua malha de navegação do leitor
2. Etiqueta acoustics geometry
3. Atribuir propriedades de materiais acústico a geometria
4. Colocação de sonda de pré-visualização
5. Inserir

## <a name="open-the-project-acoustics-editor-mode"></a>Abra o modo de edição Acoustics do projeto

Importe o pacote de plug-in do projeto Acoustics ao seu projeto. Para obter ajuda com isso, consulte a [integração Unreal](unreal-integration.md) tópico. Assim que o plug-in é integrado, abra a interface do Usuário de Acoustics clicando no ícone do modo de Acoustics novo.

![Modo de Acoustics aberto](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Atores de marca para acoustics

O separador de objetos é o primeiro separador, que é apresentado quando abrir o modo de Acoustics. Utilize este separador para atores de marca no seu nível, que adiciona a **AcousticsGeometry** ou **AcousticsNavigation** etiquetas para os atores.

Selecione um ou mais objetos no Outliner mundo, ou utilizar o **seleção em massa** secção para ajudar a selecionar todos os objetos da categoria específica. Assim que os objetos estão selecionados, utilize o **etiquetagem** secção para aplicar a etiqueta pretendida para os objetos selecionados.

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes de separador de objetos

![Objetos Unreal separador Detalhes](media/unreal-objects-tab-details.png)

1. Os botões de seleção da guia (**objetos** separador selecionado). Utilize estes botões para percorrer as diversas etapas de fazer um criar acoustics, de cima para baixo.
2. Uma breve descrição sobre o que precisa fazer usando esta página.
3. Seletores disponíveis para atores no nível.
4. Clicar **selecione** irá selecionar todos os objetos no nível que correspondem a, pelo menos, um dos tipos de ator marcada.
5. Clicar **Anular toda seleção** limpará a seleção atual. Este é o mesmo quanto pressionar a tecla ESC.
6. Utilize estes botões de opção para escolher se pretende aplicar a etiqueta de geometria ou a navegação para os atores selecionados.
7. Clicar **marca** irá adicionar a etiqueta selecionada para todos os atores atualmente selecionadas.
8. Clicar **Untag** irá remover a etiqueta selecionada de todos os atores atualmente selecionadas.
9. Clicar **selecione etiquetados** desmarque a seleção atual e selecione todos os atores com a etiqueta selecionada atualmente.
10. Estas estatísticas mostram quantos atores são marcados com cada tipo de etiqueta.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Etiqueta acoustics geometry oclusão e reflexão

Abra o separador de objetos da janela Acoustics. Marca todos os objetos como geometria Acoustics se deve occlude, refletir ou absorver som. Acoustics geometry pode incluir coisas como zero, paredes, roofs, windows e transparência da janela, rugs e mobiliário grandes. Pode usar qualquer nível arbitrário de complexidade para esses objetos. Como a cena é voxelized antes de simulação, as malhas altamente detalhadas, como árvores com muitos folhas pequenas, não são mais dispendiosas implantar que objetos simplificados.

Não incluem as coisas que não devem afetar acoustics, tais como as malhas de colisão invisível.

Transformação de um objeto no momento do cálculo da sonda (através do separador de sondas, abaixo) é fixa nos resultados de criar. Mover qualquer um dos objetos marcados na cena será necessário refazer o cálculo da sonda e rebaking a cena.

## <a name="create-or-tag-a-navigation-mesh"></a>Criar ou marcar uma malha de navegação

Uma malha de navegação é utilizada para colocar os pontos de sonda de simulação. Pode usar do Unreal [Volume dos limites de malha Nav](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html), ou pode especificar sua própria malha de navegação. Tem de marcar pelo menos um objeto como **Acoustics navegação**.

## <a name="select-acoustic-materials"></a>Selecione acústicos materiais

Depois dos objetos são marcados, clique nas **materiais** botão para ir para o separador de materiais. Este separador será utilizado para especificar as propriedades materiais para cada material no nível. Antes de quaisquer atores são etiquetados, este ficará em branco.

Os materiais acústicos controlam a quantidade de energia som refletida novamente a partir de cada superfície. O material acústico padrão tem absorption semelhante à concreto. Projeto Acoustics sugere materiais com base no nome do material da cena.

O tempo de reverberation de um determinado material numa sala inversamente está relacionado ao seu coeficiente de absorption, com a maioria dos materiais ter valores absorption no intervalo 0,01 para 0,20. Materiais com coeficientes absorption acima deste intervalo são muito absorbent. Por exemplo, se sons de sala demasiado reverberant, altere o material acústico do paredes, piso ou limite para algo absorptivity superior. A atribuição de material acústica se aplica a todos os atores que utilizam esse material de cena.

![Gráfico de tempos de reverberação](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes do separador de materiais

![Objetos Unreal separador Detalhes](media/unreal-materials-tab-details.png)

1. O **materiais** botão de separador, usado para exibir esta página.
2. Uma breve descrição sobre o que precisa fazer usando esta página.
3. A lista de materiais usados no nível, retirado dos atores marcado como **AcousticsGeometry**. Clicar num material aqui, irá selecionar todos os objetos da cena que utilizam esse material.
4. Mostra o material acústico que o material de cena lhe foi atribuído. Clique numa lista pendente para reatribuir um material de cena para um material acústico diferente.
5. Mostra o coeficiente de absorption acústico do material selecionado na coluna anterior. Um valor zero significa que perfeitamente reflexiva (sem absorption), enquanto um valor 1 significa que perfeitamente absorptive (nenhuma reflexão). Alterar este valor irá atualizar o Material de Acoustics (etapa #4) para **personalizado**.


## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e reveja as localizações da sonda de serviço de escuta

Depois de atribuir os materiais, mude para o **sondas** separador.

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes do separador de sondas

![Detalhe do separador de sondas](media/unreal-probes-tab-details.png)

1. O **sondas** botão de separador usado para exibir esta página
2. Uma breve descrição sobre o que precisa fazer usando esta página
3. Utilize esta opção para escolher uma resolução de simulação de genérico ou tudo bem. Genérico é mais rápido, mas tem determinadas vantagens e desvantagens. Ver [vs genérico bem resolução](#Coarse-vs-Fine-Resolution) abaixo para obter detalhes.
4. Escolha a localização onde os ficheiros de dados acoustics devem ser colocados através deste campo. Clique no botão com "..." para utilizar um Seletor de pasta. Para obter mais informações sobre os ficheiros de dados, consulte [ficheiros de dados](#Data-Files) abaixo.
5. Os ficheiros de dados para essa cena serão nomeados com o prefixo fornecido aqui. A predefinição é "[nome do nível] _AcousticsData".
6. Clique nas **Calculate** botão para voxelize a cena e calcular as localizações de pontos de sonda. Isso é feito localmente no seu computador e deve ser feito antes de fazer um criar. Depois das sondas foram calculadas, os controlos acima serão desativados e este botão será alterado para dizer **clara**. Clique nas **clara** botão para apagar os cálculos e ativar os controlos para que pode recalcular com novas definições.

Sondas devem ser colocadas durante o processo automatizado fornecido no **sondas** separador.


### <a name="what-the-calculate-button-calculates"></a>O que calcula o botão "Calculate."

O **Calculate** botão leva todos os dados fornecidos até agora (geometry, navegação, materiais e a definição de genérico/fina) e passa por vários passos:

1. Ele usa a geometria de malhas a cena e calcula um volume voxel. O volume de voxel é um volume 3-dimensional que engloba sua cena inteira e é constituído por pequenas cúbica "voxels". O tamanho do voxels é determinado pela frequência de simulação, que é definida à **resolução de simulação** definição. Cada voxel está marcado como sendo um "Abrir ligação sem fios" ou que contém a geometria da cena. Se um voxel contém geometry, em seguida, o voxel é marcado com o coeficiente de absorption do material atribuído a esse geometry.
2. Em seguida, utiliza os dados de navegação para calcular acoustically interessantes locais em que o jogador poderá ir. Ele tenta encontrar um conjunto razoavelmente pequeno um destes locais, que inclui áreas mais pequenas, como doorways e corredores e, em seguida, para ambientes, para abrir espaços. De cenas pequeno isso normalmente é menos de 100 localidades, enquanto o plano grandes pode ter até mil.
3. Para cada uma das localizações de final de serviço de escuta computa, ele determina que um número de parâmetros, como como "aberto" é o espaço, o tamanho do espaço está a ser, etc.
4. Os resultados desses cálculos são armazenados nos arquivos na localização que especificar (consulte [ficheiros de dados](#Data-Files) abaixo)

Consoante o tamanho da sua cena e a velocidade da sua máquina, esses cálculos podem demorar vários minutos.

Depois que esses cálculos forem concluídos, pode visualizar os dados de voxel e as localizações de pontos de sonda para ajudar a garantir que a criar irá dar-lhe bons resultados. Coisas como uma malha de navegação ruim ou em falta/extra geometry normalmente serão rapidamente visível na pré-visualização para que pode corrigi-lo.


## <a name="debug-display"></a>Depurar a apresentar

Após a conclusão do cálculo de pesquisa, um ator novo irá aparecer no Outliner mundo chamado **AcousticsDebugRenderer**. A verificar a **renderizar sondas** e **renderizar Voxels** caixas de seleção permitirá que a exibição de depuração dentro da janela viewport do editor.

![Compositor de depurar Acoustics](media/acoustics-debug-renderer.png)

Se não vir qualquer voxels ou sobrepostas seu nível de sondas, certifique-se de processamento em tempo real está ativado da janela viewport.

![Ativar o processamento em tempo real](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels são apresentadas na janela da cena como cubos verde em torno de geometria participante. Voxels que contêm apenas ar não são apresentados. Há uma grande caixa verde em torno de sua cena inteira que denota o volume de voxel completa que será utilizado na simulação.
Mover-se a sua cena e certifique-se de que a geometria acoustically occluding tem voxels. Além disso, verifique que não acoustics objetos, tais como as malhas de colisão ainda não foram voxelized. A câmara de cena deve ser dentro de cerca de 5 metros do objeto para o voxels mostrar.

Se comparar o voxels criado com resolução fina do vs resolução genérico, verá que o voxels genérico duas vezes são tão grandes.

![Pré-visualização do Voxel](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Pontos de sonda

Pontos de sonda são sinônimos de localizações de player possíveis (serviço de escuta). Quando implantando, a simulação calcula acoustics ligar todas as localizações de origem possíveis para cada ponto de sonda. No tempo de execução, a localização de serviço de escuta é interpolada entre pontos próximos da sonda.

É importante verificar que, pontos de sonda existam qualquer lugar, que espera-se que o jogador percorre a cena. Pontos de sonda são colocados em malha navegação pelo mecanismo de Acoustics de projeto e não não possível mover ou editá-lo, por isso, certifique-se de bastidores de malha navegação todas as localizações de player possível inspecionando os pontos de sonda.

![Pré-visualização de sondas](media/unreal-probes-preview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Resolução vs genérico

A única diferença entre as definições de resolução de genérico e tudo bem, é a frequência com que a simulação é executada. Bem utiliza uma frequência de duas vezes tão elevada como genérico.
Embora isso possa parecer simple, tem várias implicações na simulação acústica:

* O wavelength para genérico se duas vezes, desde que tudo bem, sendo, portanto, os voxels duas vezes tão grandes.
* O tempo de simulação está diretamente relacionada com o tamanho de voxel, tornando um genérico criar aproximadamente 16 vezes mais rápido do que um criar tudo bem.
* Portais (por exemplo, as portas ou windows) inferior ao tamanho de voxel não podem ser simulados. A definição genérico pode fazer com que alguns destes portais menores, a não ser simulada; portanto, eles não passará som por meio em tempo de execução. Pode ver se isso está acontecendo, visualizando o voxels.
* A frequência de simulação inferior resulta em menos diffraction em torno de cantos e margens.
* Origens de som não podem estar localizadas dentro de "preenchido" voxels, que é voxels que contêm a geometria - isso resulta em nenhum som. É mais difícil colocar as origens de som para que não estejam dentro o voxels maiores de genérico que é quando utiliza a definição adequada.
* O maior voxels será atrapalham a mais em portais, conforme mostrado abaixo. A primeira imagem foi criada com genérico, enquanto o segundo é a mesma porta com resolução fina. Conforme indicado pelas marcas de vermelhas, há muito menos intrusões a porta através da definição tudo bem. A linha azul é a porta, conforme definido por geometry, enquanto a linha vermelha é o portal de acústico eficaz definido pelo tamanho voxel. Como esta invasão papel em determinada situação depende completamente como os voxels alinhar com a geometria do portal, que é determinado pelo tamanho e localizações de seus objetos na cena.

![Porta genérico](media/unreal-coarse-bake.png)

![Porta adequada](media/unreal-fine-bake.png)

## <a name="bake-your-level-using-azure-batch"></a>Inserir o seu nível com o Azure Batch

Pode incluir sua cena com um cluster de computação na cloud com o serviço Azure Batch. O plug-in do projeto Acoustics Unreal liga-se diretamente ao Azure Batch para criar uma instância, gerenciar e subdividir um cluster do Azure Batch para cada criar. Na guia criar, introduza as credenciais do Azure, selecione um tipo de máquina de cluster e o tamanho e clique em criar.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes do separador de criar

![Inserir o detalhe do separador](media/unreal-bake-tab-details.png)

1. O botão de separador Inserir usado para exibir esta página.
2. Uma breve descrição do que fazer nesta página.
3. Campos para introduzir as suas credenciais do Azure quando tiver sido criada a sua conta do Azure. Para obter mais informações, consulte [criar uma conta do Azure Batch](create-azure-account.md).
4. Inicie o portal do Azure para gerir as subscrições, monitorizar a utilização e ver informações de faturação etc. 
5. Tipo de nó a utilizar para o cálculo de computação do batch do Azure. O tipo de nó tem de ser suportado pela sua localização do Datacenter do Azure. Se não tem a certeza, deixe **Standard_F8s_v2**.
6. Número de nós a utilizar para este cálculo. O número que introduzir aqui afeta o tempo de execução a criar e é limitado pela sua alocação de núcleos do Azure Batch. A alocação padrão apenas permite que para nós de duas de 8 núcleos ou nó de uma de 16 núcleos, mas pode ser expandida. Para obter mais informações sobre restrições de alocação de núcleos, consulte [criar uma conta do Azure Batch](create-azure-account.md).
7. Selecione esta caixa de verificação para configurar o conjunto de computação para utilizar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm muito custo mais baixo, mas nem sempre é disponíveis ou podem ser suplantadas em qualquer altura.
8. A quantidade de tempo decorrido, que espera-se a tomar para sua tarefa para ser executada na cloud. Isso não inclui o tempo de inicialização do nó. Assim que a tarefa é iniciada em execução, isso é quanto deve estar antes de obtém os resultados. Tenha em atenção que se trata de apenas uma estimativa.
9. A quantidade total de tempo de computação necessário para executar simulações. Esta é a quantidade total de tempo de computação de nó que será utilizado no Azure. Ver [estimativa criar custo](#Estimating-bake-cost) abaixo para obter mais informações sobre como utilizar este valor.
10. Clique no botão Criar para submeter a criar para a cloud. Enquanto uma tarefa está em execução, isso mostra **Cancelar tarefa** em vez disso. Se existirem erros neste separador, ou se o fluxo de trabalho no **sondas** separador não foi concluído, este botão será desabilitado.
11. A contagem de sonda para sua cena calculado em como o **sondas** separador. O número de sondas determina o número de simulações que devem ser executados na cloud. Não é possível especificar mais nós do que existem sondas.
12. Esta mensagem indica o estado atual da tarefa, ou se existirem quaisquer erros neste separador, o que esses erros são.

Sempre pode obter informações completas sobre tarefas ativas, conjuntos de computação e armazenamento no [portal do Azure](https://portal.azure.com).

Enquanto uma tarefa está a executar o **colocá** botão muda para **Cancelar tarefa**. Utilize este botão para cancelar a tarefa em curso. A cancelar uma tarefa não pode ser anulada, os resultados não estarão disponíveis e ainda incorrerá em qualquer tempo de computação do Azure utilizado antes do cancelamento.

Assim que tiver iniciado um criar, pode fechar o Unreal. Dependendo do projeto, o tipo de nó e o número de nós, criar uma cloud pode demorar várias horas. O estado da tarefa de criar será atualizado quando recarregar o projeto e abra a janela de Acoustics. Se a tarefa for concluída, será transferido o ficheiro de saída.

As credenciais do Azure são armazenadas em segurança no seu computador local e associadas ao projeto Unreal. Eles são usados unicamente para estabelecer uma ligação segura para o Azure.

### <a name="Estimating-bake-cost"></a> Estimar o custo de criar o Azure

Para estimar o que um determinado criar terão um custo, pegamos o valor mostrado para **custo estimado de computação**, que é um período de tempo e multiply que, pela hora a hora de custos na sua moeda local do **tipo de nó de VM** que selecionou. O resultado não incluirá o tempo de nó necessário colocar os nós e em execução. Por exemplo, se selecionar **Standard_F8s_v2** para o seu tipo de nó, que tem um custo de US $ 0,40/h e o custo estimado de computação é de 3 horas e 57 minutos, o custo estimado para executar a tarefa será de US $0,40 * ~ 4 horas = ~ $1.60. O custo real provavelmente será um pouco maior devido ao tempo adicional para obter os nós iniciado. Pode encontrar o nó por hora de custos no [preços do Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) página (selecione "com otimização de computação" ou "computação de elevado desempenho" para a categoria).

### <a name="reviewing-the-bake-results"></a>Rever os resultados de criar

Depois de concluída a criar, verifique que os pontos de voxels e sonda são em seus locais esperados ao executar o plug-in de tempo de execução.

## <a name="Data-Files"></a>Ficheiros de dados

Existem quatro arquivos de dados criados por este plug-in em vários pontos. Somente uma delas é necessário no tempo de execução e é colocada na pasta de conteúdo/Acoustics de seu projeto, que é automaticamente adicionada ao caminho de empacotamento do seu projeto. Os outros três são dentro da pasta de dados de Acoustics e não são empacotados.

* **[Project]/Config/ProjectAcoustics.cfg**: Esse arquivo armazena os dados que insira nos campos na interface de Usuário de modo Acoustics. Não não possível alterar a localização e o nome deste ficheiro. Existem outros valores armazenados neste ficheiro, que afetam a criar, mas eles são para utilizadores avançados e não devem ser alterados.
* **[Project]/Content/Acoustics/[LevelName]\_AcousticsData.ace**: Esse arquivo é o que é criado durante a simulação de criar e contém os dados de pesquisa utilizados pelo tempo de execução para renderizar acoustics da sua cena. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Esse arquivo armazena a geometria de acoustics voxelized e as propriedades materiais. Calculado utilizando a **Calculate** botão a **sondas** separador. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.
* **[Project]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Esse arquivo armazena parâmetros calculados utilizando a **Calculate** botão a **sondas** separador. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.

Tenha cuidado para não excluir o arquivo de *.ace transferido a partir do Azure. Este ficheiro não é recuperável, exceto por rebaking a cena.

## <a name="next-steps"></a>Passos Seguintes
* Explorar o [desenvolver controles para Unreal](unreal-workflow.md)
* Explorar o [conceitos de design de projeto Acoustics](design-process.md)

