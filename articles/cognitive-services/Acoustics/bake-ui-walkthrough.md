---
title: Inserir acoustics com Acoustics do projeto
titlesuffix: Azure Cognitive Services
description: Este documento descreve o processo de submissão de um criar acoustics usando a extensão de editor do Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3f567e66a1e84dccaa18db550f30d6bb34ad8162
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442941"
---
# <a name="bake-acoustics"></a>Inserir acoustics

Este documento descreve o processo de submissão de um criar acoustics usando a extensão de editor do Unity. Para obter mais informações sobre acoustics, veja [o que é Acoustics](what-is-acoustics.md). Para obter um guia de início rápido, consulte [introdução ao](getting-started.md).

## <a name="import-the-plugin"></a>Importar o plug-in

Importe o pacote de plug-in Acoustics ao seu projeto. Em seguida, abra a interface do Usuário de Acoustics escolhendo **janela > Acoustics** no menu do Unity:

![Janela de Acoustics aberto](media/WindowAcoustics.png)

## <a name="principles"></a>Princípios

A janela da ferramenta Acoustics recolhe as informações a acoustics motor precisa calcular o acoustics para sua cena. Existem cinco passos para fazer uma criar:

1. Criar ou marcar sua malha de navegação do leitor
2. Mark acoustics geometry
3. Atribuir propriedades de materiais acústico a geometria
4. Colocação de sonda de pré-visualização
5. Inserir

Depois de concluída a criar, ver [visão geral do processo de Design de Acoustics](design-process.md) para obter os passos de design de pós-criar opcional.

## <a name="create-or-mark-a-navigation-mesh"></a>Criar ou marcar uma malha de navegação

Uma malha de navegação é utilizada para colocar os pontos de sonda de simulação. Pode usar do Unity [fluxo de trabalho de malha de navegação](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html), ou pode especificar sua própria malha de navegação. As malhas de navegação criadas com o fluxo de trabalho do Unity serão detetadas pelo sistema acoustics. Para utilizar o seu próprio malhas, marcá-los a partir da **objetos** separador conforme descrito no passo seguinte.

## <a name="objects-tab"></a>Separador de objetos

Abra o **objetos** separador da **Acoustics** janela. Utilize este separador para marcar os objetos na sua cena, que simplesmente adiciona os **AcousticsGeometry** ou **AcousticsNavigation** componentes para o objeto. Também pode utilizar o [fluxo de trabalho de componente do Unity padrão](https://docs.unity3d.com/Manual/UsingComponents.html) para marcar ou desmarcar os objetos.

Selecione um ou mais objetos na janela de cena ou hierarquia e, em seguida, marcar ou desmarcá-los para **AcousticsGeometry** ou **AcousticsNavigation** conforme descrito abaixo. Se nada estiver selecionado, em seguida, pode marcar ou desmarcar tudo na cena de uma só vez.

Apenas os compositores de malha e Terrains podem ser marcados. Todos os outros tipos de objeto serão ignorados. As caixas de seleção irão marcar ou desmarcar todos os objetos afetados.

Se tiver nada selecionado em seu cenário, ele será semelhante a imagem seguinte:

![Objetos não separador nenhuma seleção](media/ObjectsTabNoSelectionDetail.png)

Se tiver algo selecionado na sua janela de cena ou uma hierarquia, ele será semelhante a imagem seguinte:

![Objetos não separador nenhuma seleção](media/ObjectsTabWithSelectionDetail.png)

### <a name="objects-tab-parts"></a>Partes do separador de objetos

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

Se alguns objetos são marcados e algumas não estão, na caixa de verificação adequada irá mostrar um valor de "misto":

![Caixa de verificação do valor misto](media/MixedObjectSelectionDetail.png)

Ao clicar na caixa de verificação forçará todos os objetos sejam marcadas e clicar novamente irá desmarcar todos os objetos.

Objetos podem ser marcados para geometria e navegação.

### <a name="guidelines-for-marking-objects"></a>Diretrizes para marcar objetos

Certifique-se de que marcar todos os objetos como **Acoustics Geometry** se eles devem occlude, refletir ou absorver som. Acoustics geometry pode incluir coisas como zero, paredes, roofs, windows e transparência da janela, rugs e mobiliário grandes. Há problema em incluir objetos menores, como lamps, itens decorativos, aparelhos de iluminação, à medida que eles appreciably não aumentam o custo de criar. É importante para não perder elementos principais, como zero ou um limite. Além disso, não incluem as coisas que não devem afetar acoustics, por exemplo, as malhas de colisão.

Transformação de um objeto no momento do cálculo da sonda (através do **sondas** separador, abaixo) é fixa nos resultados de criar. Mover qualquer um dos objetos marcados na cena será necessário refazer o cálculo da sonda e rebaking a cena.

## <a name="materials-tab"></a>Separador de materiais

Assim que os objetos são marcados, clique nas **materiais** botão para ir para o separador de materiais.

### <a name="parts-of-the-materials-tab"></a>Partes do separador de materiais

![Detalhe do separador de materiais](media/MaterialsTabDetail.png)

1. O **materiais** botão de separador, usado para exibir esta página.
2. Uma breve descrição sobre o que precisa fazer usando esta página.
3. Quando selecionada, apenas os materiais usados pelos objetos marcados como **Acoustics Geometry** serão listadas. Caso contrário, serão apresentados todos os materiais usados na cena.
4. Utilize estas opções para alterar a ordem de menu pendente que é mostrado quando clica numa lista suspensa na coluna Acoustics abaixo (n º 6). **Nome** ordena os materiais acústicos por nome. "Absorptivity" ordena-as por ordem de absorptivity de baixa para alta.
5. A lista de materiais usados na cena, ordenada por ordem alfabética. Se o **Mostrar marcados como só** caixa de seleção está marcada (n º 3), apenas os materiais usados pelos objetos marcado como **Acoustics Geometry** são mostrados. Clicar num material aqui, irá selecionar todos os objetos da cena que utilizam esse material.
6. Mostra o material acústico que o material de cena lhe foi atribuído. Clique numa lista pendente para reatribuir um material de cena para um material acústico diferente. Pode alterar a ordem de classificação do menu mostrado quando clica num item aqui com o **Acoustics de ordenar por:** opções acima (n. º 4).
7. Mostra o coeficiente de absorption acústico do material selecionado na coluna anterior. Um valor zero significa que perfeitamente reflexiva (sem absorption), enquanto um valor 1 significa que perfeitamente absorptive (nenhuma reflexão). Não é possível alterar o coeficiente de absorption, a menos que o material selecionado é "Personalizado".
8. Para um material atribuído para "Personalizado", o controlo de deslize já não está desativada e pode escolher o coeficiente de absorption no controlo de deslize ou digitando um valor. Para obter mais informações sobre as propriedades materiais, consulte [visão geral do processo de Design de Acoustics](design-process.md).

### <a name="guidelines-for-assigning-materials-or-absorption-values"></a>Diretrizes para atribuição de materiais (ou valores de absorption)

Este separador tenta adivinhar o que é o absorption de seus materiais com base no nome. Por exemplo, se o nome de material de cena for LivingRoom_WoodTable, o material acústico inicial atribuído à mesma serão "madeira". Materiais onde não é possível determinar um material conhecido são atribuídos o material de "Predefinição", que tem absorption semelhante à concreto.

Pode reatribuir os materiais acústicos para cada material de cena. Por exemplo, se sons de sala demasiado reverberant, altere o material acústico do paredes, piso ou limite para algo absorptivity superior. A atribuição de material acústica aplica-se a todos os objetos que utilizam esse material de cena.

## <a name="probes-tab"></a>Separador de sondas

Depois de atribuir os materiais, mude para o **sondas** separador.

### <a name="parts-of-the-probes-tab"></a>Partes do separador de sondas

![Detalhe do separador de sondas](media/ProbesTabDetail.png)

1. O **sondas** botão de separador usado para exibir esta página
2. Uma breve descrição sobre o que precisa fazer usando esta página
3. Utilizá-las para escolher uma resolução de simulação de genérico ou tudo bem. Genérico é mais rápido, mas tem determinadas vantagens e desvantagens. Ver ["vs genérico bem Resolução"](#Coarse-vs-Fine-Resolution) abaixo para obter detalhes.
4. Escolha a localização onde os ficheiros de dados acoustics devem ser colocados através deste campo. Clique no botão com "..." para utilizar um Seletor de pasta. A predefinição é **ativos/AcousticsData**. Uma **Editor** subpasta também será criada nesta localização. Para obter mais informações sobre os ficheiros de dados, consulte ["Arquivos de dados"](#Data-Files) abaixo.
5. Os ficheiros de dados para essa cena serão nomeados com o prefixo fornecido aqui. A predefinição é "Acoustics_ [nome da cena]".
6. Depois das sondas foram calculadas, os controlos acima serão desativados. Clique nas **clara** botão para apagar os cálculos e ativar os controlos para que pode recalcular com novas definições.
7. Clique no **calcular...**  botão para voxelize a cena e calcular as localizações de pontos de sonda. Isso é feito localmente no seu computador e deve ser feito antes de fazer um criar.

Nesta versão do projeto Acoustics, sondas não é possível colocar manualmente e devem ser colocadas durante o processo automatizado fornecido no **sondas** separador.

### <a name="what-the-calculate-button-calculates"></a>O que calcula o botão "Calcular..."

O **calcular...**  botão leva todos os dados fornecidos até agora (geometry, navegação, materiais e a definição de Coarse/fina) e passa por vários passos:

1. Ele usa a geometria de malhas a cena e calcula um volume voxel. O volume de voxel é um volume 3-dimensional que engloba sua cena inteira e é constituído por pequenas cúbica "voxels". O tamanho do voxels é determinado pela frequência de simulação, que é definida à **resolução de simulação** definição. Cada voxel está marcado como sendo um "Abrir ligação sem fios" ou que contém a geometria da cena. Se um voxel contém geometry, em seguida, o voxel é marcado com o coeficiente de absorption do material atribuído a esse geometry.
2. Em seguida, utiliza os dados de navegação para calcular acoustically interessantes locais em que o jogador poderá ir. Ele tenta encontrar um conjunto razoavelmente pequeno um destes locais, que inclui áreas mais pequenas, como doorways e corredores e, em seguida, para ambientes, para abrir espaços. De cenas pequeno isso normalmente é menos de 100 localidades, enquanto o plano grandes pode ter até mil.
3. Para cada uma das localizações de final de serviço de escuta computa, ele determina que um número de parâmetros, como como "aberto" é o espaço, o tamanho do espaço está a ser, etc.
4. Os resultados desses cálculos são armazenados nos arquivos na localização que especificar (consulte ["Arquivos de dados"](#Data-Files) abaixo)

Consoante o tamanho da sua cena e a velocidade da sua máquina, esses cálculos podem demorar vários minutos.

Depois que esses cálculos forem concluídos, pode visualizar os dados de voxel e as localizações de pontos de sonda para ajudar a garantir que a criar irá dar-lhe bons resultados. Coisas como uma malha de navegação ruim ou em falta/extra geometry normalmente serão rapidamente visível na pré-visualização para que pode corrigi-lo.

### <a name="scene-rename"></a>A mudança de nome de cena

O nome de cena é utilizado para ligar a cena aos ficheiros que armazenar o posicionamento de ponto de sonda e voxelization. Se a cena for renomeada depois de sonda pontos são calculados, os dados de atribuição e colocação materiais são perdidos e devem ser executada novamente.

## <a name="debug-display-through-gizmos"></a>Depurar a exibição por meio de Gizmos

Por predefinição, tanto o **sondas** e **Voxels** gizmos são ativadas. Estes mostrará a que o voxels e a sonda do ponto de localizações que foram calculadas. Que podem ser ativados ou desativado usando o menu de Gizmos:

![Menu de gizmos](media/GizmosMenu.png)

### <a name="voxels"></a>Voxels

Voxels são apresentadas na janela da cena como cubos verde em torno de geometria participante. Voxels que contêm apenas ar não são apresentados. Há uma grande caixa verde em torno de sua cena inteira que denota o volume de voxel completa que será utilizado na simulação.
Mover-se a sua cena e certifique-se de que tem tudo o que deve ser a geometria voxels. A câmara de cena deve ser dentro de cerca de 5 metros do objeto para o voxels mostrar.

Se comparar o voxels criado com resolução fina do vs resolução genérico, verá que o voxels genérico duas vezes são tão grandes.

![Pré-visualização do Voxel](media/VoxelCubesPreview.png)

### <a name="probe-points"></a>Pontos de sonda

Pontos de sonda são sinônimos de localizações de player possíveis (serviço de escuta). Ao fazer uma criar, acoustics para uma origem de som em qualquer lugar da cena é calculada para cada um desses pontos de sonda. Se o jogador não é diretamente num local de ponto de pesquisa, os dados dos pontos de pesquisa mais próximos o leitor são utilizados para interpolar os parâmetros nessa localização.

Por isso é importante certificar-se de que, pontos de sonda existam em qualquer lugar que o leitor é esperado que percorre a cena, e que áreas de pequeno e doorways adequadamente são representados. Pontos de sonda são colocados pelo mecanismo de projeto Acoustics com base na interpretação da geometria da cena e não podem ser movidos ou editados nesta pré-visualização do Designer. Utilize-os em vez disso, para verificar a correção de sua marcação de geometria e os dados de sugestão de navegação.

![Pré-visualização de sondas](media/ProbesPreview.png)

### <a name="Coarse-vs-Fine-Resolution"></a>Resolução vs genérico

A única diferença entre as definições de resolução Coarse e fina é a frequência com que a simulação é executada. Bem utiliza uma frequência de duas vezes tão elevado como Coarse.
Embora isso possa parecer simple, tem várias implicações na simulação acústica:

* Wavelength para Coarse é duas vezes como há muito tempo tão bem e, portanto, os voxels são duas vezes tão grandes.
* O tempo de simulação está diretamente relacionada com o tamanho de voxel, tornando um Coarse inserir cerca de 16 vezes mais rápido do que um criar tudo bem.
* Portais (por exemplo, as portas ou windows) inferior ao tamanho de voxel não podem ser simulados. A definição genérico pode fazer com que alguns destes portais menores, a não ser simulada; portanto, eles não passará som por meio em tempo de execução. Pode ver se isso está acontecendo, visualizando o voxels.
* A frequência de simulação inferior resulta em menos diffraction em torno de cantos e margens.
* Origens de som não podem estar localizadas dentro de "preenchido" voxels, que é voxels que contêm a geometria - isso resulta em nenhum som. É mais difícil localizar as origens de som para que não estejam dentro do voxels maiores de Coarse que está a utilizar a definição adequada.
* O maior voxels será atrapalham a mais em portais, conforme mostrado abaixo. A primeira imagem foi criada utilizando o Coarse, enquanto o segundo é a mesma porta com resolução fina. Conforme indicado pelas marcas de vermelhas, há muito menos intrusões a porta através da definição tudo bem. A linha azul é a porta, conforme definido por geometry, enquanto a linha vermelha é o portal de acústico eficaz definido pelo tamanho voxel. Como esta invasão papel em determinada situação depende completamente como os voxels alinhar com a geometria do portal, que é determinado pelo tamanho e localizações de seus objetos na cena.

![Porta genérico](media/CoarseVoxelDoorway.png)

![Porta adequada](media/FineVoxelDoorway.png)

## <a name="bake-tab"></a>Inserir separador

Assim que estiver satisfeito com os dados de pré-visualização, utilize o **inserir** separador para submeter o seu criar para a cloud.

### <a name="parts-of-the-bake-tab"></a>Partes do separador de criar

![Inserir o detalhe do separador](media/BakeTabDetails.png)

1. O botão de separador Inserir usado para exibir esta página.
2. Uma breve descrição do que fazer nesta página.
3. Campos para introduzir as suas credenciais do Azure quando tiver sido criada a sua conta do Azure. Para obter mais informações, consulte [criar uma conta do Azure Batch](create-azure-account.md).
4. Etiqueta de imagem do docker para o conjunto de ferramentas de acoustics.
5. Inicie o portal do Azure para gerir as subscrições, monitorizar a utilização e ver informações de faturação etc. 
6. Tipo de nó a utilizar para o cálculo de computação do batch do Azure. O tipo de nó tem de ser suportado pela sua localização do Datacenter do Azure. Se não tem a certeza, deixe **Standard_F8s_v2**.
7. Número de nós a utilizar para este cálculo. O número que introduzir aqui afeta o tempo de execução a criar e é limitado pela sua alocação de núcleos do Azure Batch. A alocação padrão apenas permite que para nós de duas de 8 núcleos ou nó de uma de 16 núcleos, mas pode ser expandida. Para obter mais informações sobre restrições de alocação de núcleos, consulte [criar uma conta do Azure Batch](create-azure-account.md).
8. Selecione esta caixa de verificação para configurar o conjunto de computação para utilizar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm muito custo mais baixo, mas nem sempre é disponíveis ou podem ser suplantadas em qualquer altura.
9. A contagem de sonda para sua cena calculado em como o **sondas** separador. O número de sondas determina o número de simulações que devem ser executados na cloud. Não é possível especificar mais nós do que existem sondas.
10. A quantidade de tempo decorrido, que espera-se a tomar para sua tarefa para ser executada na cloud. Isso não inclui o tempo de inicialização do nó. Assim que a tarefa é iniciada em execução, isso é quanto deve estar antes de obtém os resultados. Tenha em atenção que se trata de apenas uma estimativa.
11. A quantidade total de tempo de computação necessário para executar simulações. Esta é a quantidade total de tempo de computação de nó que será utilizado no Azure. Ver [estimativa criar custo](#Estimating-bake-cost) abaixo para obter mais informações sobre como utilizar este valor.
12. Esta mensagem indica onde os resultados do criar serão guardados depois da tarefa é concluída.
13. (Apenas a utilização avançada) Se por algum motivo precisar impor Unity a esquecer um criar submetido (por exemplo, que transferiu os resultados usando outra máquina), clique nas **Limpar estado** botão esquecer sobre a tarefa que foi submetida. Observe que isso significa que o ficheiro de resultado, quando estiver pronto, serão **não** ser transferido, e **não é o mesmo que a cancelar a tarefa**. A tarefa, se executar, irá continuar a executar na cloud.
14. Clique no botão Criar para submeter a criar para a cloud. Enquanto uma tarefa está em execução, isso mostra **Cancelar tarefa** em vez disso.
15. Prepara para o processamento de simulação de acoustics num local máquinas. Ver [criar Local](#Local-bake) para obter mais informações.  
16. Esta área apresenta o estado do criar. Quando concluída, deve ser apresentado **Downloaded**.

Sempre pode obter informações completas sobre tarefas ativas, conjuntos de computação e armazenamento no [Portal do Azure](https://portal.azure.com).

Enquanto uma tarefa está a executar o **colocá** botão muda para **Cancelar tarefa**. Utilize este botão para cancelar a tarefa em curso. Será solicitado a confirmar antes da tarefa é cancelada. A cancelar uma tarefa não pode ser anulada, os resultados não estarão disponíveis e ainda incorrerá em qualquer tempo de computação do Azure utilizado.

Assim que tiver iniciado um criar, pode fechar o Unity. Dependendo do projeto, o tipo de nó e o número de nós, criar uma cloud pode demorar várias horas. O estado da tarefa de criar será atualizado quando recarregar o projeto e abra a janela de Acoustics. Se a tarefa for concluída, será transferido o ficheiro de saída.

As credenciais do Azure são armazenadas em segurança no seu computador local e associadas com o seu editor de Unity. Eles são usados unicamente para estabelecer uma ligação segura para o Azure.

### <a name="Estimating-bake-cost"></a> Estimar o custo de criar o Azure

Para estimar o que um determinado criar terão um custo, pegamos o valor mostrado para **custo estimado de computação**, que é um período de tempo e multiply que, pela hora a hora de custos na sua moeda local do **tipo de nó de VM** que selecionou. O resultado não incluirá o tempo de nó necessário colocar os nós e em execução. Por exemplo, se selecionar **Standard_F8s_v2** para o seu tipo de nó, que tem um custo de US $ 0,40/h e o custo estimado de computação é de 3 horas e 57 minutos, o custo estimado para executar a tarefa será de US $0,40 * ~ 4 horas = ~ $1.60. O custo real provavelmente será um pouco maior devido ao tempo adicional para obter os nós iniciado. Pode encontrar o nó por hora de custos no [preços do Azure Batch](https://azure.microsoft.com/pricing/details/virtual-machines/linux) página (selecione "com otimização de computação" ou "computação de elevado desempenho" para a categoria).

### <a name="reviewing-the-bake-results"></a>Rever os resultados de criar

Depois de concluída a criar, verifique que os pontos de voxels e sonda são em seus locais esperados ao executar o plug-in de tempo de execução. Obter mais informações estão no [visão geral do processo de Design de Acoustics](design-process.md).

## <a name="Local-bake"></a>Criar local
Criar local executa simulação acoustics em seu próprio computador em vez de descarregá-los para o cluster de computação do Azure Batch. Isso pode ser uma boa opção para fazer experiências com acoustics sem a necessidade de uma subscrição do Azure. Tenha em atenção que a simulação de Acoustics computacionalmente está exigindo e pode demorar algum tempo dependendo do tamanho da cena, configuração de simulação e poder de computação não processado da máquina de processamento.

### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware
processador Intel de 64 bits com, pelo menos, 8 núcleos e 32 GB de RAM ou superior.

Por exemplo, numa máquina de 8 núcleos com o Intel Xeon E5-1660 @ 3 GHz e 32 GB de RAM-
* Pequena cena com 100 sondas demora aproximadamente 2 horas para um criar genérico e horas de aproximadamente 32 para criar uma resolução fina.
* Cenário maior com 1000 sondas pode demorar até ~ 20 horas para uma resolução genérico e ~ 21 dias para criar uma resolução fina.

### <a name="setup-docker"></a>Configurar Docker
Instalar e configurar o Docker no PC que irá processar a simulação-
1. Instalar o [conjunto de ferramentas do Docker](https://www.docker.com/products/docker-desktop).
2. Inicie as definições do Docker, navegue para as opções de "Advanced" e configurar recursos de ter, pelo menos, 8GB de RAM. As CPUs mais pode alocar a Docker, mais rapidamente a criar irá concluir. ![Definições de Docker de exemplo](media/DockerSettings.png)
3. Navegue para "Unidades partilhadas" e ativar partilha para a unidade utilizada para processamento.![DockerDriveSharing](media/DockerSharedDrives.png)

### <a name="run-local-bake"></a>Executar criar local
1. Clique no botão "Preparar Local inserir" na guia criar e selecione uma pasta onde serão guardados os ficheiros de entrada e os scripts de execução. Em seguida, pode executar a criar em qualquer máquina, desde que cumprem os requisitos mínimos de hardware e tem o Docker instalado ao copiar a pasta para que a máquina.
2. Inicie a simulação usando o script "runlocalbake.bat". Este script irá obter a imagem do Docker de Acoustics de projeto com o conjunto de ferramentas necessário para processamento de simulação e inicia a simulação. 
3. Quando tiver terminado de simulação, copie o ficheiro de .ace resultante para seu projeto Unity. Para certificar-se de que Unity reconhece isso como um arquivo binário, acrescente ".bytes" para a extensão de ficheiro (por exemplo, "Scene1.ace.bytes"). Os registos detalhados para a simulação são armazenados em "AcousticsLog.txt." Caso se depare com quaisquer problemas, partilhe este ficheiro para ajudar com diagnóstico.

## <a name="Data-Files"></a>Ficheiros de dados

Existem quatro arquivos de dados criados por este plug-in em vários pontos. Somente uma delas é necessário no tempo de execução, portanto os outros três são dentro das pastas denominadas "Editor", para que eles não ser compilados em seu projeto.

* **Editor de recursos / / [SceneName]\_AcousticsParameters.asset**: Esse arquivo armazena os dados que insira nos campos na IU do Acoustics. Não não possível alterar a localização e o nome deste ficheiro. Existem outros valores armazenados neste ficheiro, que afetam a criar, mas eles são para utilizadores avançados e não devem ser alterados.
* **Ativos/AcousticsData/Acoustics\_.ace.bytes [SceneName]**: Esse arquivo é o que é criado durante a simulação de criar e contém os dados de pesquisa utilizados pelo tempo de execução para renderizar acoustics da sua cena. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**: Esse arquivo armazena a geometria de acoustics voxelized e as propriedades materiais. Calculado utilizando a **Calculate...**  botão na guia sondas. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.
* **Ativos/AcousticsData/Editor/Acoustics\_[SceneName]\_Config. XML**: Esse arquivo armazena parâmetros calculados utilizando a **Calculate...**  botão a **sondas** separador. A localização e o nome deste ficheiro podem ser alteradas através dos campos no **sondas** separador.

Tome cuidado para não eliminar o *. ace.bytes ficheiro transferido a partir do criar. Este ficheiro não é recuperável, exceto por rebaking a cena.

## <a name="next-steps"></a>Passos Seguintes
* Aplicam-se os resultados de criar a origens de som num [visão geral do processo de Design de Acoustics](design-process.md).

