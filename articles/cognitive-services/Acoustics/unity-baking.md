---
title: Tutorial de Festival do projeto acústicas do Unity
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o trazendo acústicos com acústicas do projeto no Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 310decf8053ea16ba46250ba3aabe81c9c254e5e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243128"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tutorial de Festival do projeto acústicas do Unity
Este tutorial descreve os trazendo acústicos usando acústicas de projetos no Unity.

Requisitos de software:
* [Unity 2018.2 +](https://unity3d.com) para Windows ou MacOS
* [Plug-in de acústica do projeto integrado em seu projeto do Unity](unity-integration.md) ou o [conteúdo de exemplo do Unity acústicas do projeto](unity-quickstart.md)
* *Opcional:* Uma [conta do lote do Azure](create-azure-account.md) para acelerar o prepara usando a computação em nuvem

## <a name="open-the-project-acoustics-bake-window"></a>Abrir a janela de distorta do projeto acústicos
No Unity, selecione **acústicos** no menu **janela** .

![O editor do Unity com a opção acústica realçada no menu janela](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Criar uma malha de navegação
Os acústicos de projeto usam uma malha de navegação para posicionar pontos de investigação de ouvinte para simulação. Você pode usar o [fluxo de trabalho malha de navegação](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)do Unity. Ou você pode usar um pacote de modelagem 3D diferente para criar sua própria malha.

## <a name="mark-acoustic-scene-objects"></a>Marcar objetos de cena acústicos
A acústica do projeto depende de dois tipos de objetos de cena para simulação:
- Os objetos que refletem e occludem o som na simulação
- A malha de navegação do Player que restringe pontos de investigação de ouvinte na simulação

Os dois tipos de objeto são marcados usando a guia **objetos** .

Como a marcação de objetos simplesmente adiciona os componentes *AcousticsGeometry* ou *AcousticsNavigation* ao objeto, você também pode usar o [fluxo de trabalho do componente Unity padrão](https://docs.unity3d.com/Manual/UsingComponents.html) para marcar ou desmarcar objetos. Você só pode marcar os renderizadores de malha e os terrenos. Todos os outros tipos de objeto serão ignorados. As caixas de seleção marcam ou desmarcam todos os objetos afetados.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Marcar oclusão acústica e geometria de reflexo
Abra a guia **objetos** da janela **acústica** . Marque todos os objetos como *acústicos Geometry* se eles devem occluder, refletir ou absorver o som. A geometria acústica pode incluir coisas como terra, paredes, telhados, janelas e vidro de janela, Rugs e móveis grandes. Você pode usar qualquer nível arbitrário de complexidade para esses objetos. Como a cena é voxelized antes da simulação, malhas altamente detalhadas, como árvores que têm muitas folhas, não são mais dispendiosas de serem distortas do que objetos simplificados.

Não inclua coisas que não devem afetar os acústicos, como as malhas de colisão invisíveis.

A transformação de um objeto durante o cálculo da investigação (por meio da guia **investigações** ) é corrigida nos resultados da distorta. Se qualquer objeto marcado na cena for movido posteriormente, o cálculo de investigação e trazendo precisarão ser refeitos.

### <a name="mark-the-navigation-mesh"></a>Marcar a malha de navegação
As malhas de navegação criadas por meio do fluxo de trabalho do Unity serão selecionadas pelo sistema acústicos. Para usar suas próprias malhas, marque-as na guia **objetos** .

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: as partes da guia objetos
As partes da página da guia (configuradas após as descrições) são:

1. Os botões de seleção de guia (com a guia **objetos** selecionada). Use esses botões para percorrer as várias etapas de um sobrefestival de acústica, da esquerda para a direita.
1. Uma breve descrição do que você pode fazer usando essa guia.
1. Filtros disponíveis para a janela hierarquia. Use essas opções para filtrar a janela hierarquia para objetos do tipo especificado para que você possa marcá-las facilmente. Se você ainda não marcou nada para acústica, selecionar as duas últimas opções mostrará nada. No entanto, essas opções ajudam a localizar objetos depois que eles são marcados.
1. Quando nenhum objeto é selecionado, esta seção mostra o status de todos os objetos na cena.
    * Total: o número total de objetos ativos e não ocultos.
    * Ignorado: o número de objetos que não são renderizadores de malha ou terrenos.
    * Malha: o número de objetos do processador de malha.
    * Terreno: o número de objetos do terreno.
    * Geometry: o número de objetos de malha ou terreno que são marcados como **geometria acústica**.
    * Navegação: o número de objetos de malha ou terreno marcados como **navegação acústica**. Esse número não inclui o Unity NavMesh.
1. O número total de objetos "marcáveis" na cena, que são somente renderizadores de malha e terrenos. Use as caixas de seleção para marcar (adicione o componente apropriado a) esses objetos como geometria ou navegação para acústicas.
1. Quando nada estiver selecionado, essa observação o lembrará de selecionar objetos para marcação, se necessário. Você também pode marcar uma ou ambas as caixas de seleção para marcar todos os objetos na cena.
1. Quando os objetos são selecionados, esta seção mostra o status apenas dos objetos selecionados.
1. O número total de objetos selecionados "marcáveis". Marcar ou desmarcar as caixas de seleção marca ou desmarca apenas os objetos selecionados.

Se você não tiver nada selecionado em sua cena, a guia **objetos** será parecida com a imagem a seguir.

![A guia objetos acústicos sem nada selecionado](media/objects-tab-no-selection-detail.png)

Se você tiver algo selecionado em sua janela de cena ou de hierarquia, a guia será parecida com a imagem a seguir.

![A guia objetos acústicos com seleções](media/objects-tab-selection-detail.png)

Se alguns objetos estiverem marcados e outros não, as caixas de seleção apropriadas mostrarão um valor "misto", como a imagem a seguir.

![A guia objetos acústicos com uma seleção mista de ícones realçada](media/mixed-object-selection-detail.png)

Marque a caixa de seleção para marcar todos os itens. Desmarque-a para desmarcar todos os objetos.

Os objetos podem ser marcados para geometria e navegação.

## <a name="select-acoustic-materials"></a>Selecionar materiais acústicos
Depois que os objetos estiverem marcados, selecione o botão **materiais** . Em seguida, atribua materiais acústicos. O sistema de materiais acústicos do projeto está vinculado ao sistema de materiais visuais do Unity. Para que dois objetos tenham materiais acústicos separados, eles devem ter materiais visuais separados.

A seleção de material acústico determina a quantidade de energia de som que é refletida de volta de cada superfície. O material acústico padrão tem absorção semelhante a aço. O projeto acústicos sugere materiais com base no nome do material visual. Você também pode atribuir o material acústico **personalizado** a um material para ativar um controle deslizante de absorção-coeficiente ajustável.

O tempo de reverberation de um determinado material em uma sala está relacionado inversamente ao seu coeficiente de absorção. A maioria dos materiais tem valores de absorção em um intervalo de 0, 1 a 0,20. Os materiais que têm coeficientes de absorção fora desse intervalo são muito absorbents.

![Um grafo mostra a correlação negativa do tempo de reverberation e o coeficiente de absorção.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: partes da guia materiais
![A guia de materiais acústicos no Unity](media/materials-tab-detail.png)
1. O botão **materiais** exibe essa guia.
2. Uma breve descrição do que você pode fazer usando essa guia.
3. Quando essa caixa de seleção está marcada, somente materiais que são usados por objetos marcados como **geometria de acústica** são listados. Caso contrário, todos os materiais usados na cena serão listados.
4. Use essas opções para alterar a ordem das opções em um menu na coluna **acústica** (#6). Classifique materiais acústicos por **nome** ou por **Absorptivity**, de baixo para alto.
5. Uma lista de materiais classificada alfabeticamente que são usados na cena. Se a caixa de seleção **Mostrar somente marcados** estiver marcada (#3), somente os materiais usados por objetos marcados como **geometria acústica** serão mostrados. Selecione um material aqui para selecionar todos os objetos na cena que usam esse material.
6. O material acústico ao qual o material da cena foi atribuído. Selecione qualquer item para alterar o material acústico atribuído a esse material de cena. Para alterar a ordem de classificação desses menus, use as opções de **classificação acústicas por** (#4).
7. O coeficiente de absorção acústica do material selecionado na coluna à esquerda (#6). Um valor de 0 significa perfeitamente reflexivo (sem absorção), enquanto 1 significa perfeitamente absorptive (sem reflexão). O coeficiente de absorção não pode ser alterado, a menos que o material selecionado seja **personalizado.**
8. Para um material marcado como **personalizado**, o controle deslizante é ativado. Você pode mover o controle deslizante ou digitar um valor para atribuir um coeficiente de absorção.

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e revisar locais de investigação de ouvinte
Depois de atribuir os materiais, alterne para a guia **investigações** . Selecione **calcular** para posicionar pontos de investigação de ouvinte para simulação.

### <a name="what-the-calculate-button-does"></a>O que o botão "Calculate" faz
O botão **Calculate** usa a geometria da cena acústica selecionada para preparar sua cena para simulação:

- Ele usa a geometria das malhas de cena e calcula um *volume VOXEL*. O volume VOXEL é um volume de toda a sua cena composta por um pequeno voxels cúbico "." O tamanho de VOXEL é determinado pela frequência de simulação, que é controlada pela configuração de **resolução de simulação** . Cada voxel é marcado como "Open Air" ou que contém a geometria da cena. Se um VOXEL contiver Geometry, o VOXEL será marcado com o coeficiente de absorção do material atribuído a essa geometria.
- Ele usa as malhas de navegação para posicionar pontos de investigação de ouvinte. O algoritmo equilibra as preocupações concorrentes da cobertura espacial e do tempo de simulação e do tamanho do arquivo. Ele visa garantir que pavimentos e pequenos espaços delimitados sempre tenham alguma cobertura. As contagens de pontos de investigação típicas variam de 100 para cenas pequenas a alguns milhares de cenas grandes.

Dependendo do tamanho da sua cena e da velocidade do seu computador, esses cálculos podem levar vários minutos.

### <a name="review-voxel-and-probe-placement"></a>Examinar o Voxel e o posicionamento da investigação
Visualize os dados do Voxel e os locais do ponto de investigação para certificar-se de que você está pronto para criar sua cena. Uma malha de navegação incompleta ou uma geometria acústica ausente ou extra geralmente é facilmente visível na visualização. Habilite ou desabilite o Voxel e o posicionamento de investigação usando o menu **utensílios** .

![O menu utensílios no Unity](media/gizmos-menu.png)

Voxels que contêm geometria acústica são mostrados como cubos verdes. Explore sua cena e verifique se tudo o que deve ser Geometry tem voxels. A câmera de cena deve estar em cerca de 5 metros do objeto para o voxels mostrar.

Se você comparar voxels criados com resolução alta versus resolução refinada, verá que os voxelss grandes são duas vezes maiores.

![A visualização de voxels grosso no editor do Unity](media/voxel-cubes-preview.png)

Os resultados da simulação são interpolados entre locais de ponto de investigação de ouvinte no tempo de execução. Verifique se há pontos de investigação perto de qualquer lugar onde o Player é esperado na cena.

![A visualização de investigações no editor do Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Tome cuidado com renomeações de cena
O nome da cena é usado para conectar a cena a arquivos que armazenam o posicionamento do ponto de teste e voxelization. Se você renomear a cena após os pontos de investigação serem calculados, os dados de inserção e de posicionamento do material serão perdidos e deverão ser executados novamente.

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: partes da guia investigações
![A guia de investigações acústicas no Unity](media/probes-tab-detail.png)

1. O botão **investigações** abre essa guia.
2. Uma breve descrição do que você pode fazer nessa guia.
3. Use essas opções para definir a resolução de simulação grande ou fina. A grande velocidade é mais rápida, mas há compensações. Para obter detalhes, consulte [resoluções de distorta](bake-resolution.md).
4. Especifica onde colocar os arquivos de dados acústicos. Selecione o botão " **...** " para acessar um seletor de pasta. O local padrão é *assets/AcousticsData*. Uma subpasta do *Editor* também é criada neste local. Para obter mais informações, consulte [arquivos de dados adicionados pelo processo de distorta](#Data-Files), posteriormente neste artigo.
5. O prefixo especificado aqui é usado para nomear os arquivos de dados para esta cena. O padrão é "Acoustics_ *[nome da cena]* ".
6. Depois que as investigações são calculadas, os controles que acabamos de descrever são desabilitados. Selecione o botão **limpar** para apagar os cálculos e habilitar os controles para que você possa recalcular com as novas configurações.
7. Selecione **calcular** para voxelize a cena e calcular os locais dos pontos de investigação. O cálculo é feito localmente em seu computador. Ele deve ser feito antes de fazer um distorta.

Nesta versão do projeto acústica, as investigações não podem ser colocadas manualmente. Use o processo automatizado na guia **investigações** .

Para obter mais informações sobre resolução grande versus refinada, consulte [resoluções](bake-resolution.md)de sobreposição.

## <a name="bake-your-scene-by-using-azure-batch"></a>Distortar sua cena usando o lote do Azure
Você pode distortar sua cena em um cluster de computação na nuvem usando o serviço de lote do Azure. O plug-in de Unity do acústica do projeto se conecta diretamente ao lote do Azure para instanciar, gerenciar e subdividir um cluster do lote do Azure para cada distorta. Na guia **distorta** , insira suas credenciais do Azure, selecione um tipo de máquina de cluster e um tamanho e, em seguida, selecione **distortar**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: partes da guia de distorta
![A guia estortar acústicos no Unity](media/bake-tab-details.png)

1. O botão **distorta** exibe essa guia.
2. Uma breve descrição do que você pode fazer nesta página.
3. Insira suas credenciais do Azure nesses campos, depois que sua conta do Azure for criada. Para obter mais informações, consulte [criar uma conta do lote do Azure](create-azure-account.md).
4. O campo de marca de imagem do Docker para o conjunto de ferramentas acústicos.
5. Abre o portal do Azure para gerenciar suas assinaturas, monitorar o uso e exibir informações de cobrança.
6. Especifica o tipo de nó de computação do lote do Azure a ser usado para cálculo. O tipo de nó deve ser suportado pelo seu local de datacenter do Azure. Se você não tiver certeza, deixe como **Standard_F8s_v2**.
7. O número de nós a serem usados para o cálculo. Esse número afeta o tempo de torta. Ele é limitado pela sua alocação principal do lote do Azure. A alocação padrão permite apenas dois nós de 8 núcleos ou um nó de núcleo 1 16, mas pode ser expandido. Para obter mais informações sobre as restrições de alocação de núcleo, consulte [criar uma conta do lote do Azure](create-azure-account.md).
8. Marque essa caixa de seleção para configurar seu pool de computação para usar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm um custo muito menor. Mas eles nem sempre podem estar disponíveis ou podem ser preempçãos a qualquer momento.
9. A contagem de investigações para sua cena, conforme calculada na guia **investigações** . O número de investigações determina o número de simulações que precisam ser executadas na nuvem. Você não pode especificar mais nós do que há investigações.
10. Uma estimativa do tempo que o trabalho levará para ser executado na nuvem, excluindo o tempo de inicialização do nó. Depois que o trabalho começar a ser executado, esse campo mostrará uma estimativa de quanto tempo até que você obtenha os resultados.
11. A quantidade total de tempo de computação necessário para executar as simulações. Esse valor é a quantidade total de tempo de computação do nó que será usada no Azure. Para obter mais informações, consulte [estimar o custo de torta do Azure](#Estimating-bake-cost) mais adiante neste artigo.
12. Essa mensagem informa onde os resultados do torta serão salvos quando o trabalho for concluído.
13. *(Uso avançado somente:)* esse botão força o Unity a esquecer sobre um distorta que você enviou. Por exemplo, se você baixou os resultados usando outra máquina, selecione o botão **limpar estado** para esquecer esse trabalho. O arquivo de resultado, quando estiver pronto, *não* será baixado. *Isso não é o mesmo que cancelar o trabalho. O trabalho, se estiver em execução, continuará a ser executado na nuvem.*
14. Selecione este botão para enviar a distorta para a nuvem. Enquanto um trabalho estiver em execução, esse botão se tornará **Cancelar trabalho**.
15. Selecione este botão para preparar para processar [a simulação acústica em seu computador](#Local-bake).
16. Essa área mostra o status do distorta. Quando o distorta for concluído, ele mostrará "baixado".

Você sempre pode obter informações completas sobre trabalhos ativos, pools de computação e armazenamento no [portal do Azure](https://portal.azure.com).

Enquanto um trabalho está em execução, o rótulo do botão de **torta** muda para o **trabalho de cancelamento**. Use esse botão para cancelar o trabalho que está em andamento. Você será solicitado a confirmar. Não é possível desfazer o cancelamento de um trabalho. Quando você cancelar, nenhum resultado estará disponível, mas você ainda será cobrado por qualquer tempo de computação do Azure usado.

Depois de iniciar um distorta, você pode fechar o Unity. Dependendo do projeto, do tipo de nó e do número de nós, um distorta de nuvem pode levar várias horas. O status do trabalho de torta será atualizado quando você recarregar o projeto e abrir a janela acústica. Se o trabalho for concluído, o arquivo de saída será baixado.

Por segurança, as credenciais do Azure são armazenadas em seu computador local e associadas ao seu editor de Unity. Eles são usados apenas para estabelecer uma conexão segura com o Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Localizar o status de um trabalho em execução no portal do Azure

1. Localize a ID do trabalho de torta na guia de **torta** .

    ![A ID do trabalho de torta do Unity realçada na guia de torta](media/unity-job-id.png)  

2. Abra o [portal do Azure](https://portal.azure.com), vá para a conta do lote que foi usada para a distorta e selecione **trabalhos**.

    ![O link trabalhos no portal do Azure](media/azure-batch-jobs.png)  

3. Procure a ID do trabalho na lista de trabalhos.

   ![O status do trabalho de torta realçado no portal do Azure](media/azure-bake-job-status.png)  

4. Selecione a ID do trabalho para ver o status das tarefas relacionadas e o status geral do trabalho.

   ![O status da tarefa de torta](media/azure-batch-task-state.png)  


### <a name="Estimating-bake-cost"></a>Estimar o custo de torta do Azure

Para estimar o custo de um torta, comece com o valor de **custo estimado de computação** , que é uma duração. Multiplique esse valor pelo custo por hora em sua moeda local para o **tipo de nó de VM** que você selecionar. Observe que o resultado não incluirá o tempo do nó necessário para colocar os nós em funcionamento.

Por exemplo, digamos que você selecione **Standard_F8s_v2** para o tipo de nó, que tem um custo de $0.40/hr. Se o **custo estimado de computação** for de 3 horas e 57 minutos, o custo estimado para executar o trabalho será $0.40 * ~ 4 horas = ~ $1.60. O custo real provavelmente será um pouco maior por causa do tempo extra para iniciar os nós.

Encontre o custo de nós por hora em [preços do lote do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux). (Selecione **computação otimizada** ou **computação de alto desempenho** como a categoria.)

## <a name="Local-bake"></a>Distortar sua cena em seu PC
Você também pode distortar sua cena em seu próprio PC. Esse método pode ser útil para experimentar com acústicas para pequenas cenas antes de criar uma conta do lote do Azure. Mas Observe que a simulação de acústica local pode levar muito tempo, dependendo do tamanho da cena.

### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware
* Um processador x86-64 com pelo menos 8 núcleos e 32 GB de RAM
* [Hyper-V habilitado](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) para executar o Docker

Por exemplo, em nossos testes em uma máquina de 8 núcleos, Intel Xeon E5-1660 @ 3 GHz e 32 GB de RAM:
* Uma pequena cena com investigações de 100 demorou cerca de 2 horas para um supertorta ou 32 horas para um grande refinamento.
* Uma cena de tamanho médio com investigações de 1.000 demorou cerca de 20 horas para um supertorta ou 21 dias para uma supertorta.

### <a name="set-up-docker"></a>Configurar o Docker
Instale e configure o Docker no PC que processará a simulação:
1. Instale o [Docker desktop](https://www.docker.com/products/docker-desktop).
2. Abra configurações do Docker, vá para **avançado**e configure os recursos para pelo menos 8 GB de RAM. Quanto mais CPUs você puder alocar para o Docker, maior será a medida que o torta for concluído.  
![configurações do Docker de exemplo](media/docker-settings.png)
1. Vá para **unidades compartilhadas**e ative o compartilhamento para a unidade que é usada para processamento.  
![opções de unidade compartilhada do Docker](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Executar o distorta local
1. Selecione o botão **preparar torta local** na guia **distorta** . Em seguida, selecione um local de pasta para salvar os arquivos de entrada e scripts de execução. Em seguida, você pode executar o distorta em qualquer computador, desde que ele atenda aos requisitos mínimos de hardware e instale o Docker copiando a pasta para esse computador.
2. Para iniciar a simulação, execute o script *runlocalbake. bat* no Windows ou o script *runlocalbake.sh* no MacOS. Esse script busca a imagem do Docker acústica do projeto com o conjunto de ferramentas necessário para o processamento de simulação e inicia a simulação.
3. Depois que a simulação for concluída, copie o arquivo *. Ace* resultante de volta para seu projeto do Unity. Para garantir que o Unity o reconheça como um arquivo binário, acrescente ". bytes" à extensão do arquivo (por exemplo, "Scene1. Ace. bytes"). Os logs detalhados para a simulação são armazenados em *AcousticsLog. txt.* Se você encontrar algum problema, inspecione esse arquivo para ajudar a diagnosticar o problema.

## <a name="Data-Files"></a>Arquivos de dados adicionados pelo processo de torta

Os quatro arquivos de dados a seguir são criados durante o processo de distorta. Uma delas contém os resultados da simulação e é fornecida com seu título. Os outros armazenam dados relacionados ao editor Unity.

Resultado da simulação:
* *Ativos/AcousticsData/acústicos\_[scenename]. Ace. bytes*: esse arquivo é a tabela de pesquisa de tempo de execução. Ele contém os resultados da simulação e os elementos de cena acústico voxelized. Você pode alterar o nome e o local desse arquivo na guia **investigações** .

   *Tenha cuidado para não excluir o arquivo de resultado da simulação. Não é recuperável, exceto por rebaking a cena.*

Arquivos de dados do editor:
* *Ativos/editor/[scenename]\_AcousticsParameters. Asset*: esse arquivo armazena os dados inseridos nos campos na interface do usuário acústica. Você não pode alterar o nome e o local deste arquivo.
* *Ativos/AcousticsData/editor/Acoustics_ [scenename]. Vox*: esse arquivo armazena a geometria de voxelized acústicos e as propriedades de material que são computadas quando você seleciona o botão **calcular** na guia **investigações** . Você pode alterar o nome e o local desse arquivo na guia **investigações** .
* *Ativos/AcousticsData/editor/acústica\_[scenename]\_config. xml*: esse arquivo armazena os parâmetros de simulação que são computados quando você seleciona **calcular**. Você pode alterar o nome e o local desse arquivo na guia **investigações** .

## <a name="set-up-the-acoustics-lookup-table"></a>Configurar a tabela de pesquisa acústica
Arraste o **projeto acústica** pré-fabricado do painel de projeto para sua cena:

![Os pré-fabricado acústicos no Unity](media/acoustics-prefab.png)

Selecione o objeto jogo **ProjectAcoustics** e vá para o painel inspetor. Especifique o local do resultado do cotorta (o arquivo. Ace, em *ativos/AcousticsData*): arraste-o para o script do Gerenciador acústico ou selecione o botão de círculo ao lado da caixa de texto.

![O Gerenciador de acústica pré-fabricado no Unity](media/acoustics-manager.png)

## <a name="next-steps"></a>Passos seguintes
* Explore os [controles de design do Unity](unity-workflow.md).
* Explore os [conceitos de design acústicos do projeto](design-process.md).
