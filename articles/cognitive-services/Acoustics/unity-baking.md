---
title: Tutorial de Festival do projeto acústicas do Unity
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o trazendo acústicos com acústicas do projeto no Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 2362b3916d1b1f430350d975dc0b61914a777be2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706695"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Tutorial de Festival do projeto acústicas do Unity
Este tutorial descreve o trazendo acústicos com acústicas do projeto no Unity.

Requisitos de software:
* [Unity 2018.2 +](https://unity3d.com) para Windows
* [Plugin acústicos do projeto integrado em seu projeto do Unity](unity-integration.md) ou o [conteúdo de exemplo do Unity acústicas do projeto](unity-quickstart.md)
* Opcional: Uma [conta do lote do Azure](create-azure-account.md) para acelerar o prepara usando a computação em nuvem

## <a name="open-the-project-acoustics-bake-window"></a>Abrir a janela de distorta do projeto acústicos
Escolha **janela > acústicos** no menu do Unity:

![Captura de tela da opção de menu do editor do Unity com janela acústica realçada](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Criar uma malha de navegação
Os acústicos de projeto usam uma malha de navegação para posicionar pontos de investigação de ouvinte para simulação. Você pode usar o [fluxo de trabalho de malha de navegação](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)do Unity ou usar outro pacote de modelagem 3D para criar sua própria malha. 

## <a name="mark-acoustic-scene-objects"></a>Marcar objetos de cena acústicos
Os acústicas do projeto dependem de dois tipos de objetos de cena para simulação: os objetos que refletirão e occlude som na simulação e a malha de navegação do Player que restringe os pontos de investigação do ouvinte na simulação. Os dois tipos de objeto são marcados usando a guia **objetos** . 

Como a marcação de objetos simplesmente adiciona os componentes **AcousticsGeometry** ou **AcousticsNavigation** ao objeto, você também pode usar o [fluxo de trabalho do componente Unity padrão](https://docs.unity3d.com/Manual/UsingComponents.html) para marcar ou desmarcar objetos. Somente os renderizadores de malha e os terrenos podem ser marcados. Todos os outros tipos de objeto serão ignorados. As caixas de seleção marcarão ou desmarcarão todos os objetos afetados.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Marcar oclusão acústica e geometria de reflexo
Abra a guia **objetos** da janela **acústica** . Marque todos os objetos como **acústicos Geometry** se eles devem occluder, refletir ou absorver o som. A geometria acústica pode incluir coisas como aterramento, paredes, telhados, Windows & o vidro da janela, Rugs e móveis grandes. Você pode usar qualquer nível arbitrário de complexidade para esses objetos. Como a cena é voxelized antes da simulação, malhas altamente detalhadas, como árvores com muitas folhas pequenas, não são mais dispendiosas de serem distortas do que objetos simplificados.

Não inclua coisas que não devem afetar os acústicos, como as malhas de colisão invisíveis.

A transformação de um objeto no momento do cálculo da investigação (por meio da guia **investigações** , abaixo) é corrigida nos resultados da distorta. Mover qualquer um dos objetos marcados na cena exigirá a refazer o cálculo da investigação e rebaking a cena.

### <a name="mark-the-navigation-mesh"></a>Marcar a malha de navegação
As malhas de navegação criadas com o fluxo de trabalho do Unity serão coletadas pelo sistema acústicos. Para usar suas próprias malhas, marque-as na guia **objetos** .

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes da guia objetos
As partes da página da guia são:

1. Os botões de seleção de guia (guia**objetos** selecionados). Use esses botões para percorrer as várias etapas de fazer um sobrefestival de acústica, da esquerda para a direita.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. Filtros disponíveis para a janela hierarquia. Use isso para filtrar a janela hierarquia para objetos do tipo especificado para que você possa marcá-los com mais facilidade. Se você ainda não marcou nada para acústica, selecionar as duas últimas opções mostrará nada. No entanto, eles podem ser úteis para localizar objetos marcados assim que você tiver feito isso.
4. Quando nenhum objeto é selecionado, esta seção mostra o status de todos os objetos na cena:
    * Total-o número total de objetos ativos e não ocultos na cena.
    * Ignorado – o número de objetos que não são renderizadores de malha ou terrenos.
    * Malha – o número de objetos de processador de malha na cena
    * Terreno – o número de objetos do terreno na cena
    * Geometry-o número de objetos de malha ou terreno na cena marcada como "geometria acústica"
    * Navegação – o número de objetos de malha ou terreno na cena marcada como "navegação acústica". Esse número não inclui o NavMesh do Unity.
5. Mostra o número total de objetos ' Mark-aptos ' na cena, que são somente renderizadores de malha e terrenos. Mostra caixas de seleção que você pode usar para marcar (Adicionar o componente apropriado a) esses objetos como geometria ou navegação para acústicas
6. Quando nada estiver selecionado, essa observação o lembrará de selecionar objetos para marcação, se necessário. Você também pode marcar uma ou ambas as caixas de seleção para marcar todos os objetos na cena sem selecionar nada.
7. Quando os objetos são selecionados, esta seção mostra o status apenas dos objetos selecionados.
8. Mostra o número total de objetos selecionados de ' marca-habilitação '. Marcar ou desmarcar as caixas de seleção marcará ou desmostrará apenas os objetos selecionados.

Se você não tiver nada selecionado em sua cena, a guia objetos será parecida com a imagem a seguir:

![Captura de tela da guia objetos acústicos sem seleção](media/objects-tab-no-selection-detail.png)

Se você tiver algo selecionado em sua janela de cena ou de hierarquia, ele se parecerá com a imagem a seguir:

![Captura de tela da guia objetos acústicos com a seleção mostrada](media/objects-tab-selection-detail.png)

Se alguns objetos estiverem marcados e alguns não, a caixa de seleção apropriada mostrará um valor "misto":

![Captura de tela da guia objetos acústicos com o ícone de seleção mista realçado](media/mixed-object-selection-detail.png)

Clicar na caixa de seleção forçará a marcação de todos os objetos e, se você clicar novamente, desmarcará todos os objetos.

Os objetos podem ser marcados para geometria e navegação.

## <a name="select-acoustic-materials"></a>Selecionar materiais acústicos
Depois que os objetos estiverem marcados, clique no botão **materiais** e atribua materiais acústicos. O sistema de materiais acústicos do projeto está vinculado ao sistema de materiais visuais do Unity: para que dois objetos tenham materiais acústicos separados, eles devem ter materiais visuais separados.

Os materiais acústicos controlam a quantidade de energia de som refletida de volta de cada superfície. O material acústico padrão tem absorção semelhante ao concreto. O projeto acústicos sugere materiais com base no nome do material visual. Você pode atribuir o material acústico ' Custom ' a um material para habilitar um controle deslizante de coeficiente de absorção.

O tempo de reverberation de um determinado material em uma sala está relacionado inversamente ao seu coeficiente de absorção, com a maioria dos materiais com valores de absorção no intervalo de 0, 1 a 0,20. Materiais com coeficientes de absorção fora desse intervalo são muito absorbents.

![Gráfico mostrando a correlação negativa do tempo de reverberation com o coeficiente de absorção](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes da guia materiais
![Captura de tela da guia de materiais acústicos no Unity](media/materials-tab-detail.png)

1. O botão da guia **materiais** , usado para abrir esta página.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. Quando marcada, somente os materiais usados por objetos marcados como a **geometria acústica** serão listados. Caso contrário, todos os materiais usados na cena serão listados.
4. Use essas opções para alterar a ordem do menu suspenso exibido quando você clica em uma lista suspensa na coluna acústica abaixo (#6). **Nome** classifica os materiais acústicos por nome. "Absorptivity" os classifica em ordem de Absorptivity de baixo para alto.
5. A lista de materiais usada na cena, classificada alfabeticamente. Se a caixa de seleção **Mostrar somente marcados** estiver marcada (#3), somente os materiais usados por objetos marcados como **geometria acústica** serão mostrados. Clicar em um material aqui selecionará todos os objetos na cena que usam esse material.
6. Mostra o material acústico ao qual o material da cena foi atribuído. Clique em uma lista suspensa para reatribuir um material de cena a um material acústico diferente. Você pode alterar a ordem de classificação do menu mostrado ao clicar em um item aqui usando a opção **classificar acústica por:** opções acima (#4).
7. Mostra o coeficiente de absorção acústica do material selecionado na coluna anterior. Um valor igual A zero significa perfeitamente reflexivo (sem absorção), enquanto um valor de 1 significa perfeitamente absorptive (sem reflexão). O coeficiente de absorção não pode ser alterado, a menos que o material selecionado seja "personalizado".
8. Para um material atribuído a "Custom", o controle deslizante não está mais desabilitado e você pode escolher o coeficiente de absorção usando o controle deslizante ou digitando um valor.

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e revisar locais de investigação de ouvinte
Depois de atribuir os materiais, alterne para a guia **investigações** e clique em **calcular** para posicionar pontos de investigação de ouvinte para simulação.

### <a name="what-the-calculate-button-calculates"></a>O que o "Calculate..." Calcula o botão
O botão **Calculate...** usa a geometria de cena acústica selecionada para preparar sua cena para simulação:

1. Ele usa a geometria das malhas de cena e calcula um volume VOXEL. O volume VOXEL é um volume tridimensional que inclui toda a sua cena e é composto por um pequeno "voxels" cúbico. O tamanho do voxels é determinado pela frequência de simulação, que é definida pela configuração de **resolução de simulação** . Cada voxel é marcado como sendo "Open Air" ou que contém a geometria da cena. Se um VOXEL contiver Geometry, o VOXEL será marcado com o coeficiente de absorção do material atribuído a essa geometria.
2. Ele usa as malhas de navegação para posicionar pontos de investigação de ouvinte. O algoritmo equilibra as preocupações concorrentes da cobertura espacial e do tempo de simulação e do tamanho do arquivo, garantindo que pavimentos e pequenos espaços delimitados sempre tenham alguma intensidade de cobertura. As contagens de pontos de investigação típicas variam de 100 para cenas pequenas a alguns milhares de cenas grandes.

Dependendo do tamanho da sua cena e da velocidade do seu computador, esses cálculos podem levar vários minutos.

### <a name="review-voxel-and-probe-placement"></a>Examinar o Voxel e o posicionamento da investigação
Visualize os dados do Voxel e os locais do ponto de investigação para garantir que você esteja pronto para distortar sua cena. Uma malha de navegação incompleta ou uma geometria acústica ausente ou extra geralmente será rapidamente visível na visualização. O Voxel e o posicionamento da investigação podem ser habilitados ou desabilitados usando o menu utensílios:

![Captura de tela do menu utensílios no Unity](media/gizmos-menu.png)

Voxels contendo geometria acústica são mostrados como cubos verdes. Explore sua cena e verifique se tudo o que deve ser Geometry tem voxels. A câmera de cena deve estar em cerca de 5 metros do objeto para o voxels mostrar.

Se você comparar o voxels criado com alta resolução versus resolução refinada, verá que o voxels grosso é duas vezes maior.

![Captura de tela da visualização de voxels grosso no editor do Unity](media/voxel-cubes-preview.png)

Os resultados da simulação são interpolados entre locais de ponto de investigação de ouvinte no tempo de execução. Verifique se há pontos de investigação perto de qualquer lugar que o jogador deva viajar na cena.

![Captura de tela da visualização de investigações no editor do Unity](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Tome cuidado com renomeações de cena
O nome da cena é usado para conectar a cena a arquivos que armazenam o posicionamento do ponto de teste e voxelization. Se a cena for renomeada depois que os pontos de investigação forem calculados, os dados de inserção e de posicionamento do material serão perdidos e deverão ser executados novamente.

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes da guia investigações
![Captura de tela da guia de investigações acústicas no Unity](media/probes-tab-detail.png)

1. O botão da guia **investigações** usado para abrir esta página
2. Uma breve descrição do que você precisa fazer usando esta página
3. Use-os para escolher uma resolução de simulação grande ou fina. Grande é mais rápido, mas tem certas compensações. Consulte a [resolução](bake-resolution.md) de distorta abaixo para obter detalhes.
4. Escolha o local onde os arquivos de dados acústicos devem ser colocados usando esse campo. Clique no botão com "..." para usar um seletor de pasta. O padrão é **assets/AcousticsData**. Uma subpasta do **Editor** também será criada neste local. Para obter mais informações sobre arquivos de dados, consulte [os arquivos de dados](#Data-Files) abaixo.
5. Os arquivos de dados para essa cena serão nomeados usando o prefixo fornecido aqui. O padrão é "Acoustics_ [Scene Name]".
6. Depois que as investigações forem calculadas, os controles acima serão desabilitados. Clique no botão **limpar** para apagar os cálculos e habilitar os controles para que você possa recalcular usando novas configurações.
7. Clique no botão **Calculate...** para voxelize a cena e calcular os locais do ponto de investigação. Isso é feito localmente em seu computador e deve ser feito antes de fazer uma distorta.

Nesta versão do projeto acústica, as investigações não podem ser colocadas manualmente e devem ser colocadas por meio do processo automatizado fornecido na guia **investigações** .

Consulte a [resolução](bake-resolution.md) de distorta para obter mais detalhes sobre a resolução de alta velocidade versus multas.

## <a name="bake-your-scene-using-azure-batch"></a>Distortar sua cena usando o lote do Azure
Você pode distortar sua cena com um cluster de computação na nuvem usando o serviço de lote do Azure. O plugin do Unity do acústica do projeto se conecta diretamente ao lote do Azure para instanciar, gerenciar e subdividir um cluster do lote do Azure para cada distorta. Na guia **distorta** , insira suas credenciais do Azure, selecione um tipo de máquina de cluster e um tamanho e clique em distortar.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes da guia distorta
![Captura de tela da guia distorta de acústica no Unity](media/bake-tab-details.png)

1. O botão de sobreformação da guia é usado para abrir esta página.
2. Uma breve descrição do que fazer nesta página.
3. Campos para inserir suas credenciais do Azure depois que sua conta do Azure tiver sido criada. Para obter mais informações, consulte [criar uma conta do lote do Azure](create-azure-account.md).
4. Marca de imagem do Docker para o conjunto de ferramentas acústicos.
5. Inicie o portal do Azure para gerenciar suas assinaturas, monitorar o uso e exibir informações de cobrança etc. 
6. Tipo de nó de computação do lote do Azure a ser usado para o cálculo. O tipo de nó deve ser suportado pelo local de data center do Azure. Se não tiver certeza, deixe em **Standard_F8s_v2**.
7. Número de nós a serem usados para este cálculo. O número digitado aqui afeta o tempo para concluir o distorta e é limitado pela sua alocação principal do lote do Azure. A alocação padrão permite apenas dois nós de 8 núcleos ou um nó de núcleo 1 16, mas pode ser expandida. Para obter mais informações sobre as principais restrições de alocação, consulte [criar uma conta do lote do Azure](create-azure-account.md).
8. Marque esta caixa de seleção para configurar seu pool de computação para usar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm um custo muito menor, mas nem sempre podem estar disponíveis ou podem ser admitidos a qualquer momento.
9. A contagem de investigações para sua cena, conforme calculada na guia **investigações** . O número de investigações determina o número de simulações que precisam ser executadas na nuvem. Você não pode especificar mais nós do que há investigações.
10. A quantidade de tempo decorrido que deve levar para que o trabalho seja executado na nuvem. Isso não inclui o tempo de inicialização do nó. Depois que o trabalho começar a ser executado, isso será sobre quanto tempo deve ser antes de você retornar os resultados. Observe que essa é apenas uma estimativa.
11. A quantidade total de tempo de computação necessário para executar as simulações. Esta é a quantidade total de tempo de computação do nó que será usada no Azure. Consulte [estimando o custo de torta](#Estimating-bake-cost) abaixo para obter mais informações sobre como usar esse valor.
12. Essa mensagem informa onde os resultados do torta serão salvos quando o trabalho for concluído.
13. (Somente uso avançado) Se, por alguma razão, você precisar forçar o Unity a se esquecer de um cotorta enviado (por exemplo, você baixou os resultados usando outro computador), clique no botão **limpar estado** para esquecer o trabalho que foi enviado. Observe que isso significa que o arquivo de resultado, quando estiver pronto, **não** será baixado, e **isso não é o mesmo que cancelar o trabalho**. O trabalho, se estiver em execução, continuará a ser executado na nuvem.
14. Clique no botão de distorta para enviar o cotorta para a nuvem. Enquanto um trabalho estiver em execução, isso mostrará **Cancelar trabalho** em vez disso.
15. Prepara para processar [a simulação acústica em seu PC](#Local-bake).
16. Essa área mostra o status do distorta. Quando concluído, ele deverá mostrar **baixado**.

Você sempre pode obter informações completas sobre trabalhos ativos, pools de computação e armazenamento no [portal do Azure](https://portal.azure.com).

Enquanto um trabalho estiver executando as alterações do botão de **torta** para **cancelar o trabalho**. Use esse botão para cancelar o trabalho em andamento. Você será solicitado a confirmar antes que o trabalho seja cancelado. Cancelar um trabalho não pode ser desfeito, nenhum resultado estará disponível e você ainda será cobrado por qualquer tempo de computação do Azure usado.

Depois de iniciar um distorta, você pode fechar o Unity. Dependendo do projeto, do tipo de nó e do número de nós, um distorta de nuvem pode levar várias horas. O status do trabalho de torta será atualizado quando você recarregar o projeto e abrir a janela acústica. Se o trabalho tiver sido concluído, o arquivo de saída será baixado.

As credenciais do Azure são armazenadas com segurança em seu computador local e associadas ao seu editor de Unity. Eles são usados exclusivamente para estabelecer uma conexão segura com o Azure.

### <a name="Estimating-bake-cost"></a>Estimando o custo de torta do Azure

Para estimar o custo de uma determinada torta, use o valor mostrado para o **custo estimado de computação**, que é uma duração e multiplique isso pelo custo por hora em sua moeda local do tipo de **nó de VM** selecionado. O resultado não incluirá o tempo de nó necessário para colocar os nós em funcionamento. Por exemplo, se você selecionar **Standard_F8s_v2** para o tipo de nó, que tem um custo de $0.40/HR, e o custo estimado de computação for de 3 horas e 57 minutos, o custo estimado para executar o trabalho será $0.40 * ~ 4 horas = ~ $1.60. O custo real provavelmente será um pouco maior devido ao tempo extra para iniciar os nós. Você pode encontrar o custo de nó por hora na página de [preços do lote do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (selecione "computação otimizada" ou "computação de alto desempenho" para a categoria).

## <a name="Local-bake"></a>Distortar sua cena em seu PC
Você pode distortar sua cena em seu próprio PC. Isso pode ser útil para experimentar a acústica com pequenas cenas antes de criar uma conta do lote do Azure. Observe que a simulação de acústica pode levar muito tempo, dependendo do tamanho da cena.

### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware
* Um processador x86-64 com pelo menos 8 núcleos e 32 GB de RAM

Por exemplo, em nossos testes em uma máquina com 8 núcleos com Intel Xeon E5-1660 @ 3 GHz e 32 GB de RAM-
* Uma pequena cena com investigações de 100 pode levar cerca de 2 horas para um supertorta ou 32 horas para uma distorta.
* Uma cena de tamanho médio com investigações de 1000 pode levar cerca de 20 horas por um supertorta ou 21 dias para uma supertorta.

### <a name="setup-docker"></a>Configurar o Docker
Instalar e configurar o Docker no PC que processará a simulação-
1. Instale o [conjunto de ferramentas](https://www.docker.com/products/docker-desktop)do Docker.
2. Inicie as configurações do Docker, navegue até as opções "avançadas" e configure recursos para ter pelo menos 8 GB de RAM. Quanto mais CPUs você puder alocar para o Docker, mais rápida será a cotorta. ![Captura de tela das configurações do Docker de exemplo](media/docker-settings.png)
3. Navegue até "unidades compartilhadas" e ative o compartilhamento para a unidade usada para processamento.![Captura de tela das opções de unidade compartilhada do Docker](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>Executar distorta local
1. Clique no botão "preparar a localização local" na guia de **torta** e selecione uma pasta na qual os arquivos de entrada e os scripts de execução serão salvos. Em seguida, você pode executar o distorta em qualquer computador, desde que ele atenda aos requisitos mínimos de hardware e tenha o Docker instalado copiando a pasta para esse computador.
2. Inicie a simulação usando o script "runlocalbake. bat". Esse script buscará a imagem do Docker acústica do projeto com o conjunto de ferramentas necessário para o processamento de simulação e iniciará a simulação. 
3. Depois que a simulação for concluída, copie o arquivo. Ace resultante de volta para seu projeto do Unity. Para garantir que o Unity reconheça isso como um arquivo binário, acrescente ". bytes" à extensão do arquivo (por exemplo, "Scene1. Ace. bytes"). Os logs detalhados para a simulação são armazenados em "AcousticsLog. txt". Se você tiver problemas, compartilhe esse arquivo para auxiliar no diagnóstico.

## <a name="Data-Files"></a>Arquivos de dados adicionados pelo processo de torta

Há quatro arquivos de dados criados durante o processo de distorta. Uma delas contém os resultados da simulação e é fornecida com seu título. Os outros armazenam dados relacionados ao editor Unity.

Resultado da simulação:
* **Ativos/AcousticsData/acústicas\_[scenename]. Ace. bytes**: Esta é a tabela de pesquisa de tempo de execução e contém resultados de simulação e os elementos de cena acústica voxelized. O local e o nome desse arquivo podem ser alterados usando os campos na guia **investigações** .

Tome cuidado para não excluir o arquivo de resultado da simulação. Não é recuperável, exceto por rebaking a cena.

Arquivos de dados do editor:
* **Assets/editor/[scenename\_] AcousticsParameters. ativo**: Esse arquivo armazena os dados inseridos nos campos na interface do usuário acústica. O local e o nome deste arquivo não podem ser alterados.
* **Assets/AcousticsData/editor/Acoustics_ [scenename]. Vox**: Esse arquivo armazena a geometria de voxelized acústicos e as propriedades de material que são computadas usando o botão **Calculate...** na guia investigações. O local e o nome desse arquivo podem ser alterados usando os campos na guia **investigações** .
* **Ativos/AcousticsData/editor/acústica\_[scenename]\_config. xml**: Esse arquivo armazena os parâmetros de simulação computados usando o botão **Calculate...** na guia **investigações** . O local e o nome desse arquivo podem ser alterados usando os campos na guia **investigações** .

## <a name="set-up-the-acoustics-lookup-table"></a>Configurar a tabela de pesquisa acústica
Arraste e solte o **projeto acústica** pré-fabricado do painel de projeto em sua cena:

![Captura de tela de pré-fabricado acústicos no Unity](media/acoustics-prefab.png)

Clique no objeto jogo **ProjectAcoustics** e vá para o painel inspetor. Especifique o local do resultado do cotorta (o. ACE, em **ativos/AcousticsData**) arrastando-o e soltando-o no script do Gerenciador acústicos ou clicando no botão de círculo ao lado da caixa de texto.

![Captura de tela de pré-fabricado do acústica Manager no Unity](media/acoustics-manager.png)  

## <a name="next-steps"></a>Passos Seguintes
* Explorar os [controles de design para o Unity](unity-workflow.md)
* Explore os [conceitos de design acústicos do projeto](design-process.md)

