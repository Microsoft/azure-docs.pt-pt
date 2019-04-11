---
title: Tutorial de criar projeto Acoustics Unity
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve acoustics implantando com o projeto Acoustics no Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 8875674b0f9c621a573dda591b4dc2b6f018a83c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470343"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tutorial de criar projeto Acoustics Unity
Este tutorial descreve acoustics implantando com o projeto Acoustics no Unity.

Requisitos de software:
* [Unity 2018.2 +](http://unity3d.com) para Windows
* [Plug-in do projeto Acoustics integrado no seu projeto Unity](unity-integration.md) ou o [conteúdos de exemplo de projeto Acoustics Unity](unity-quickstart.md)
* Opcional: Uma [conta do Azure Batch](create-azure-account.md) para acelerar a incorpora a utilizar a informática na cloud

## <a name="open-the-project-acoustics-bake-window"></a>Abra o projeto Acoustics colocá janela
Escolher **janela > Acoustics** no menu do Unity:

![Editor de captura de ecrã do Unity com a opção de menu de janela Acoustics realçada](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Criar uma malha de navegação
Projeto Acoustics utiliza uma malha de navegação para colocar pontos de sonda de serviço de escuta de simulação. Pode usar do Unity [fluxo de trabalho de malha de navegação](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), ou utilize outro pacote de modelagem 3D para estruturar o seu próprio malha. 

## <a name="mark-acoustic-scene-objects"></a>Marcar os objetos de cena acústico
Projeto Acoustics se baseia em dois tipos de objetos de cena de simulação: os objetos que irão refletir e occlude som na simulação e a malha de navegação do leitor que restringe os pontos de sonda de serviço de escuta na simulação. Ambos os tipos de objeto são marcados com o **objetos** separador. 

Porque simplesmente marcar objetos adiciona a **AcousticsGeometry** ou **AcousticsNavigation** componentes para o objeto, pode também usar o [Unity componente fluxo de trabalho padrão](https://docs.unity3d.com/Manual/UsingComponents.html)marcar ou desmarcar os objetos. Apenas os compositores de malha e Terrains podem ser marcados. Todos os outros tipos de objeto serão ignorados. As caixas de seleção irão marcar ou desmarcar todos os objetos afetados.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Marcar acústico geometry oclusão e reflexão
Abra o **objetos** separador da **Acoustics** janela. Marcar todos os objetos como **Acoustics Geometry** se eles devem occlude, refletir ou absorver som. Acoustics geometry pode incluir coisas como zero, paredes, roofs, windows e transparência da janela, rugs e mobiliário grandes. Pode usar qualquer nível arbitrário de complexidade para esses objetos. Como a cena é voxelized antes de simulação, as malhas altamente detalhadas, como árvores com muitos folhas pequenas, não são mais dispendiosas implantar que objetos simplificados.

Não incluem as coisas que não devem afetar acoustics, tais como as malhas de colisão invisível.

Transformação de um objeto no momento do cálculo da sonda (através do **sondas** separador, abaixo) é fixa nos resultados de criar. Mover qualquer um dos objetos marcados na cena será necessário refazer o cálculo da sonda e rebaking a cena.

### <a name="mark-the-navigation-mesh"></a>Marcar a malha de navegação
As malhas de navegação criadas com o fluxo de trabalho do Unity serão detetadas pelo sistema acoustics. Para utilizar o seu próprio malhas, marcá-los a partir da **objetos** separador.

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes de separador de objetos
As partes da página da guia são:

1. Os botões de seleção da guia (**objetos** separador selecionado). Utilize estes botões para percorrer as diversas etapas de fazer um criar acoustics, da esquerda para a direita.
2. Uma breve descrição sobre o que precisa fazer usando esta página.
3. Filtros disponíveis para a janela de hierarquia. Utilize esta opção para filtrar a janela de hierarquia para objetos do tipo especificado para que pode marcá-los mais facilmente. Se não o tiver feito, mas marcado nada para acoustics, selecionar as duas últimas opções mostrará a nada. No entanto, pode ser útil para localizar objetos marcados, assim que tiver feito isso.
4. Quando não existem objetos estiverem selecionados, esta secção mostra o estado de todos os objetos da cena:
    * Total - o número total de objetos do Active Directory, não oculto na cena.
    * Ignorado - o número de objetos que não sejam compositores de malha ou Terrains.
    * O número de malha compositor de malha - objetos da cena
    * Terreno - o número de terreno objetos da cena
    * Geometry - o número de malha ou terreno objetos da cena marcados como "Acoustics Geometry"
    * Navegação - o número de malha ou terreno objetos da cena marcados como "Navegação Acoustics". Este número não inclui NavMesh do Unity.
5. Mostra o número total de objetos 'mark ajustável' da cena, que é apenas compositores de malha e Terrains. Mostra as caixas de verificação que pode utilizar para marcar (adicionar o componente apropriado para) esses objetos como geometria ou a navegação para acoustics
6. Quando nada estiver selecionado, esta nota irá relembrá-lo para selecionar objetos para marcação, se necessário. Também pode verificar uma ou ambas as caixas de verificação para marcar todos os objetos da cena sem selecionar qualquer coisa.
7. Quando são selecionados objetos, esta secção mostra o estado de apenas os objetos selecionados.
8. Mostra o número total de "marca-capaz de" objetos selecionados. Marcando ou desmarcando as caixas de seleção marcar ou desmarcar apenas os objetos selecionados.

Se tiver nada selecionado em seu cenário, o separador de objetos será semelhante a imagem seguinte:

![Separador de captura de ecrã de Acoustics objetos com nenhuma seleção](media/objects-tab-no-selection-detail.png)

Se tiver algo selecionado na sua janela de cena ou uma hierarquia, ele será semelhante a imagem seguinte:

![Captura de ecrã de Acoustics objetos separador com seleção mostrada](media/objects-tab-selection-detail.png)

Se alguns objetos são marcados e algumas não estão, na caixa de verificação adequada irá mostrar um valor de "misto":

![Separador de captura de ecrã de Acoustics objetos com o ícone de seleção misto realçado](media/mixed-object-selection-detail.png)

Ao clicar na caixa de verificação forçará todos os objetos sejam marcadas e clicar novamente irá desmarcar todos os objetos.

Objetos podem ser marcados para geometria e navegação.

## <a name="select-acoustic-materials"></a>Selecione acústicos materiais
Assim que os objetos são marcados, clique nas **materiais** botão e atribuir acústicos materiais. O sistema de materiais de projeto Acoustics está associado ao sistema de materiais visual Unity: para dois objetos ter materiais acústicos separadas, têm de ter materiais visual separados.

Os materiais acústicos controlam a quantidade de energia som refletida novamente a partir de cada superfície. O material acústico padrão tem absorption semelhante à concreto. Projeto Acoustics sugere materiais com base no nome do material visual. Pode atribuir o material acústico 'Custom' para um material para permitir um controlo de deslize do coeficiente de absorption.

O tempo de reverberation de um determinado material numa sala inversamente está relacionado ao seu coeficiente de absorption, com a maioria dos materiais ter valores absorption no intervalo 0,01 para 0,20. Materiais com coeficientes absorption fora deste intervalo são muito absorbent.

![Gráfico que mostra a correlação negativa de tempo de reverberation com coeficiente de absorption](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes do separador de materiais
![Separador de captura de ecrã de Acoustics materiais no Unity](media/materials-tab-detail.png)

1. O **materiais** botão de separador, usado para exibir esta página.
2. Uma breve descrição sobre o que precisa fazer usando esta página.
3. Quando selecionada, apenas os materiais usados pelos objetos marcados como **Acoustics Geometry** serão listadas. Caso contrário, serão apresentados todos os materiais usados na cena.
4. Utilize estas opções para alterar a ordem de menu pendente que é mostrado quando clica numa lista suspensa na coluna Acoustics abaixo (n º 6). **Nome** ordena os materiais acústicos por nome. "Absorptivity" ordena-as por ordem de absorptivity de baixa para alta.
5. A lista de materiais usados na cena, ordenada por ordem alfabética. Se o **Mostrar marcados como só** caixa de seleção está marcada (n º 3), apenas os materiais usados pelos objetos marcado como **Acoustics Geometry** são mostrados. Clicar num material aqui, irá selecionar todos os objetos da cena que utilizam esse material.
6. Mostra o material acústico que o material de cena lhe foi atribuído. Clique numa lista pendente para reatribuir um material de cena para um material acústico diferente. Pode alterar a ordem de classificação do menu mostrado quando clica num item aqui com o **Acoustics de ordenar por:** opções acima (n. º 4).
7. Mostra o coeficiente de absorption acústico do material selecionado na coluna anterior. Um valor zero significa que perfeitamente reflexiva (sem absorption), enquanto um valor 1 significa que perfeitamente absorptive (nenhuma reflexão). Não é possível alterar o coeficiente de absorption, a menos que o material selecionado é "Personalizado".
8. Para um material atribuído para "Personalizado", o controlo de deslize já não está desativada e pode escolher o coeficiente de absorption no controlo de deslize ou digitando um valor.

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e reveja as localizações da sonda de serviço de escuta
Depois de atribuir os materiais, mude para o **sondas** separador e clique em **Calculate** colocar pontos de sonda de serviço de escuta de simulação.

### <a name="what-the-calculate-button-calculates"></a>O que calcula o botão "Calcular..."
O **calcular...**  botão utiliza sua geometria da cena acústico selecionado para preparar sua cena de simulação:

1. Ele usa a geometria de malhas a cena e calcula um volume voxel. O volume de voxel é um volume 3-dimensional que engloba sua cena inteira e é constituído por pequenas cúbica "voxels". O tamanho do voxels é determinado pela frequência de simulação, que é definida à **resolução de simulação** definição. Cada voxel está marcado como sendo um "Abrir ligação sem fios" ou que contém a geometria da cena. Se um voxel contém geometry, em seguida, o voxel é marcado com o coeficiente de absorption do material atribuído a esse geometry.
2. Ele usa o mesh(es) de navegação para colocar os pontos de sonda do serviço de escuta. O algoritmo equilibra as preocupações concorrentes de geográfico cobertura e simulação tempo e o ficheiro de tamanho, garantindo que restringir corredores e espaços pequeno sempre tenham alguma quantidade de cobertura. Intervalo de 100 de cenas de pequenas a alguns milhares de cenas grandes pelas contagens do ponto de sonda típico.

Consoante o tamanho da sua cena e a velocidade da sua máquina, esses cálculos podem demorar vários minutos.

### <a name="review-voxel-and-probe-placement"></a>Colocação de voxel e sonda de revisão
Pré-visualize os dados de voxel e as localizações de pontos de sonda para garantir que está pronto para implementar a sua cena. Uma malha de navegação incompleta ou em falta ou extra acústico geometry, normalmente, será rapidamente visível na pré-visualização. Colocação Voxel e sonda pode ser ativada ou desativado usando o menu de Gizmos:

![Menu de captura de ecrã de Gizmos no Unity](media/gizmos-menu.png)

Voxels contendo acústico geometry são apresentados como cubos verde. Explore sua cena e certifique-se de que tem tudo o que deve ser a geometria voxels. A câmara de cena deve ser dentro de cerca de 5 metros do objeto para o voxels mostrar.

Se comparar o voxels criado com resolução fina do vs resolução genérico, verá que o voxels genérico duas vezes são tão grandes.

![Captura de ecrã da pré-visualização voxels genérico no editor do Unity](media/voxel-cubes-preview.png)

Resultados da simulação são interpolados entre localizações de pontos de sonda de serviço de escuta no tempo de execução. Verifique existem pontos da sonda quase qualquer lugar que o leitor é esperado que percorre a cena.

![Captura de ecrã de sondas de pré-visualização no editor do Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Tenha cuidado com muda de cena
O nome de cena é utilizado para ligar a cena aos ficheiros que armazenar o posicionamento de ponto de sonda e voxelization. Se a cena for renomeada depois de sonda pontos são calculados, os dados de atribuição e colocação materiais são perdidos e devem ser executada novamente.

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes do separador de sondas
![Separador de captura de ecrã de sondas de Acoustics no Unity](media/probes-tab-detail.png)

1. O **sondas** botão de separador usado para exibir esta página
2. Uma breve descrição sobre o que precisa fazer usando esta página
3. Utilizá-las para escolher uma resolução de simulação de genérico ou tudo bem. Genérico é mais rápido, mas tem determinadas vantagens e desvantagens. Ver [inserir resolução](bake-resolution.md) abaixo para obter detalhes.
4. Escolha a localização onde os ficheiros de dados acoustics devem ser colocados através deste campo. Clique no botão com "..." para utilizar um Seletor de pasta. A predefinição é **ativos/AcousticsData**. Uma **Editor** subpasta também será criada nesta localização. Para obter mais informações sobre os ficheiros de dados, consulte [ficheiros de dados](#Data-Files) abaixo.
5. Os ficheiros de dados para essa cena serão nomeados com o prefixo fornecido aqui. A predefinição é "Acoustics_ [nome da cena]".
6. Depois das sondas foram calculadas, os controlos acima serão desativados. Clique nas **clara** botão para apagar os cálculos e ativar os controlos para que pode recalcular com novas definições.
7. Clique no **calcular...**  botão para voxelize a cena e calcular as localizações de pontos de sonda. Isso é feito localmente no seu computador e deve ser feito antes de fazer um criar.

Nesta versão do projeto Acoustics, sondas não é possível colocar manualmente e devem ser colocadas durante o processo automatizado fornecido no **sondas** separador.

Ver [inserir resolução](bake-resolution.md) para obter mais detalhes sobre o vs genérico bem resolução.

## <a name="bake-your-scene-using-azure-batch"></a>Inserir sua cena com o Azure Batch
Pode incluir sua cena com um cluster de computação na cloud com o serviço Azure Batch. O plug-in do projeto Acoustics Unity liga-se diretamente ao Azure Batch para criar uma instância, gerenciar e subdividir um cluster do Azure Batch para cada criar. Sobre o **colocá** separador, introduza as credenciais do Azure, selecione um tipo de máquina de cluster e o tamanho e clique em **inserir**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes do separador de criar
![Separador de captura de ecrã de Acoustics inserir no Unity](media/bake-tab-details.png)

1. O botão de separador Inserir usado para exibir esta página.
2. Uma breve descrição do que fazer nesta página.
3. Campos para introduzir as suas credenciais do Azure quando tiver sido criada a sua conta do Azure. Para obter mais informações, consulte [criar uma conta do Azure Batch](create-azure-account.md).
4. Etiqueta de imagem do docker para o conjunto de ferramentas de acoustics.
5. Inicie o portal do Azure para gerir as subscrições, monitorizar a utilização e ver informações de faturação etc. 
6. Tipo de nó a utilizar para o cálculo de computação do batch do Azure. O tipo de nó tem de ser suportado pela sua localização do Datacenter do Azure. Se não tem a certeza, deixe **Standard_F8s_v2**.
7. Número de nós a utilizar para este cálculo. O número que introduzir aqui afeta o tempo de execução a criar e é limitado pela sua alocação de núcleos do Azure Batch. A alocação padrão apenas permite que para nós de duas de 8 núcleos ou nó de uma de 16 núcleos, mas pode ser expandida. Para obter mais informações sobre restrições de alocação de núcleos, consulte [criar uma conta do Azure Batch](create-azure-account.md).
8. Selecione esta caixa de verificação para configurar o conjunto de computação para utilizar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm muito custo mais baixo, mas nem sempre é disponíveis ou podem ser suplantadas em qualquer altura.
9. A contagem de sonda para sua cena calculado em como o **sondas** separador. O número de sondas determina o número de simulações que devem ser executados na cloud. Não é possível especificar mais nós do que existem sondas.
10. A quantidade de tempo decorrido que se espera que o necessário para que a tarefa seja executada na cloud. Isso não inclui o tempo de inicialização do nó. Assim que a tarefa é iniciada em execução, isso é quanto deve estar antes de obtém os resultados. Tenha em atenção que se trata de apenas uma estimativa.
11. A quantidade total de tempo de computação necessário para executar simulações. Esta é a quantidade total de tempo de computação de nó que será utilizado no Azure. Ver [estimativa criar custo](#Estimating-bake-cost) abaixo para obter mais informações sobre como utilizar este valor.
12. Esta mensagem indica onde os resultados do criar serão guardados depois da tarefa é concluída.
13. (Apenas a utilização avançada) Se por algum motivo precisar impor Unity a esquecer um criar submetido (por exemplo, que transferiu os resultados usando outra máquina), clique nas **Limpar estado** botão esquecer sobre a tarefa que foi submetida. Observe que isso significa que o ficheiro de resultado, quando estiver pronto, serão **não** ser transferido, e **não é o mesmo que a cancelar a tarefa**. A tarefa, se executar, irá continuar a executar na cloud.
14. Clique nas **inserir** botão para submeter a criar para a cloud. Enquanto uma tarefa está em execução, isso mostra **Cancelar tarefa** em vez disso.
15. Prepara para processamento [simulação acoustics no seu PC](#Local-bake).
16. Esta área apresenta o estado do criar. Quando concluída, deve ser apresentado **Downloaded**.

Sempre pode obter informações completas sobre tarefas ativas, conjuntos de computação e armazenamento no [portal do Azure](https://portal.azure.com).

Enquanto uma tarefa está a executar o **colocá** botão muda para **Cancelar tarefa**. Utilize este botão para cancelar a tarefa em curso. Será solicitado a confirmar antes da tarefa é cancelada. A cancelar uma tarefa não pode ser anulada, os resultados não estarão disponíveis e ainda incorrerá em qualquer tempo de computação do Azure utilizado.

Assim que tiver iniciado um criar, pode fechar o Unity. Dependendo do projeto, o tipo de nó e o número de nós, criar uma cloud pode demorar várias horas. O estado da tarefa de criar será atualizado quando recarregar o projeto e abra a janela de Acoustics. Se a tarefa for concluída, será transferido o ficheiro de saída.

As credenciais do Azure são armazenadas em segurança no seu computador local e associadas com o seu editor de Unity. Eles são usados unicamente para estabelecer uma ligação segura para o Azure.

### <a name="Estimating-bake-cost"></a> Estimar o custo de criar o Azure

Para estimar o que um determinado criar terão um custo, pegamos o valor mostrado para **custo estimado de computação**, que é um período de tempo e multiply que, pela hora a hora de custos na sua moeda local do **tipo de nó de VM** que selecionou. O resultado não incluirá o tempo de nó necessário colocar os nós e em execução. Por exemplo, se selecionar **Standard_F8s_v2** para o seu tipo de nó, que tem um custo de US $ 0,40/h e o custo estimado de computação é de 3 horas e 57 minutos, o custo estimado para executar a tarefa será de US $0,40 * ~ 4 horas = ~ $1.60. O custo real provavelmente será um pouco maior devido ao tempo adicional para obter os nós iniciado. Pode encontrar o nó por hora de custos no [preços do Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) página (selecione "com otimização de computação" ou "computação de elevado desempenho" para a categoria).

## <a name="Local-bake"></a> Inserir sua cena no seu PC
Pode incluir sua cena em seu próprio computador. Isso pode ser útil para fazer experiências com acoustics com pequenas plano antes de criar uma conta do Azure Batch. Tenha em atenção a simulação de acoustics pode demorar algum tempo dependendo do tamanho da cena.

### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware
* Um processador de x86 64 com pelo menos 8 núcleos e 32 GB de RAM

Por exemplo, no nosso teste numa máquina de 8 núcleos com o Intel Xeon E5-1660 @ 3 GHz e 32 GB de RAM-
* Uma pequena cena com 100 sondas pode demorar cerca de 2 horas para um criar genérico ou 32 horas para um criar tudo bem.
* Uma cena de médio porte com 1000 sondas pode demorar cerca de 20 horas para um criar genérico ou 21 dias para um criar tudo bem.

### <a name="setup-docker"></a>Configurar Docker
Instalar e configurar o Docker no PC que irá processar a simulação-
1. Instalar o [conjunto de ferramentas do Docker](https://www.docker.com/products/docker-desktop).
2. Inicie as definições do Docker, navegue para as opções de "Advanced" e configurar recursos de ter, pelo menos, 8GB de RAM. As CPUs mais pode alocar a Docker, mais rapidamente a criar irá concluir. ![Captura de ecrã das definições de Docker de exemplo](media/docker-settings.png)
3. Navegue para "Unidades partilhadas" e ativar partilha para a unidade utilizada para processamento.![Opções de unidade de captura de ecrã do Docker partilhado](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Executar criar local
1. Clique no botão "Preparar Local inserir" no **inserir** separador e selecione uma pasta onde serão guardados os ficheiros de entrada e os scripts de execução. Em seguida, pode executar a criar em qualquer máquina, desde que cumprem os requisitos mínimos de hardware e tem o Docker instalado ao copiar a pasta para que a máquina.
2. Inicie a simulação usando o script "runlocalbake.bat". Este script irá obter a imagem do Docker de Acoustics de projeto com o conjunto de ferramentas necessário para processamento de simulação e inicia a simulação. 
3. Quando tiver terminado de simulação, copie o ficheiro de .ace resultante para seu projeto Unity. Para certificar-se de que Unity reconhece isso como um arquivo binário, acrescente ".bytes" para a extensão de ficheiro (por exemplo, "Scene1.ace.bytes"). Os registos detalhados para a simulação são armazenados em "AcousticsLog.txt." Caso se depare com quaisquer problemas, partilhe este ficheiro para ajudar com diagnóstico.

## <a name="Data-Files"></a> Ficheiros de dados adicionados pelo processo de criar

Existem quatro arquivos de dados criados durante o processo de criar. Um contém os resultados da simulação e é fornecido com o título. Armazenam os outros dados relacionados com o Editor do Unity.

Resultado de simulação:
* **Ativos/AcousticsData/Acoustics\_.ace.bytes [SceneName]**: Esta é a tabela de pesquisa de tempo de execução e contém os elementos de cena acústico voxelized e os resultados da simulação. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.

Tenha cuidado para não excluir o arquivo de resultado de simulação. Não é recuperável, exceto por rebaking a cena.

Ficheiros de dados do Editor:
* **Editor de recursos / / [SceneName]\_AcousticsParameters.asset**: Esse arquivo armazena os dados que insira nos campos na IU do Acoustics. Não não possível alterar a localização e o nome deste ficheiro.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Esse arquivo armazena a geometria de acoustics voxelized e as propriedades materiais que são calculadas usando o **Calculate...**  botão na guia sondas. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.
* **Ativos/AcousticsData/Editor/Acoustics\_[SceneName]\_Config. XML**: Esse arquivo armazena os parâmetros de simulação calculados utilizando a **Calculate...**  botão a **sondas** separador. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.

## <a name="set-up-the-acoustics-lookup-table"></a>Configurar a tabela de pesquisa acoustics
Arraste e largue a **projeto Acoustics** prefab a partir do painel de projeto em sua cena:

![Prefab de captura de ecrã de Acoustics no Unity](media/acoustics-prefab.png)

Clique nas **ProjectAcoustics** objeto do jogo e passa para o seu painel de inspetor. Especifique a localização do seu resultado de criar (o. ACE arquivo, na **ativos/AcousticsData**) ao arrastar-e-removê-lo para o script de Acoustics Manager ou ao clicar no botão círculo junto à caixa de texto.

![Captura de ecrã do Gestor de Acoustics prefab no Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Passos Seguintes
* Explorar o [desenvolver controles para Unity](unity-workflow.md)
* Explorar o [conceitos de design de projeto Acoustics](design-process.md)

