---
title: Projeto Tutorial de Bolo de Unidade Acústica
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve a acústica a assar com o Projeto Acústica em Unidade.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72243128"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>Projeto Tutorial de Bolo de Unidade Acústica
Este tutorial descreve a cozedura acústica utilizando o Projeto Acústica em Unidade.

Requisitos de software:
* [Unidade 2018.2+](https://unity3d.com) para Windows ou MacOS
* [Project Acoustics plug-in integrado no seu projeto de Unidade](unity-integration.md) ou no conteúdo da amostra de unidade acústica do [projeto](unity-quickstart.md)
* *Opcional:* Uma [conta de Azure Batch](create-azure-account.md) para acelerar os bolos usando a computação em nuvem

## <a name="open-the-project-acoustics-bake-window"></a>Abra a janela de cozedura do Projeto Acústica
Em Unidade, selecione **Acústica** do menu **Janela.**

![O editor da Unidade com a opção Acústica destacada no menu Janela](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>Criar uma malha de navegação
O Project Acoustics usa uma malha de navegação para colocar pontos de sonda de ouvintes para simulação. Você pode usar o fluxo de trabalho de malha de [navegação](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)Unidade . Ou pode usar um pacote de modelação 3D diferente para desenhar a sua própria malha.

## <a name="mark-acoustic-scene-objects"></a>Marque objetos de cena acústica
O Projeto Acústica baseia-se em dois tipos de objetos de cena para simulação:
- Os objetos que refletem e oclum som na simulação
- A malha de navegação do jogador que limita os pontos de sonda ouvinte em simulação

Ambos os tipos de objetos são marcados utilizando o separador **Objetos.**

Uma vez que a marcação de objetos simplesmente adiciona os componentes *De Navegação Acústica* ou Acústica ao objeto, também pode utilizar o fluxo de trabalho padrão do componente [Unidade](https://docs.unity3d.com/Manual/UsingComponents.html) para marcar ou desmarcar *objetos.* Só se pode marcar renderizadores de malha e terrenos. Todos os outros tipos de objetos serão ignorados. As caixas de verificação marcam ou desmarcam todos os objetos afetados.

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>Marque a oclusão acústica e a geometria da reflexão
Abra o separador **Objetos** da janela **Acústica.** Marque quaisquer objetos como *Geometria Acústica* se estes oclusem, reflitam ou absorvam o som. A geometria acústica pode incluir coisas como chão, paredes, telhados, janelas e vidros de janela, tapetes e móveis grandes. Pode utilizar qualquer nível arbitrário de complexidade para estes objetos. Como a cena é voxelizada antes da simulação, malhas altamente detalhadas, como árvores que têm muitas folhas, não são mais dispendiosas para assar do que objetos simplificados.

Não inclua coisas que não devem afetar a acústica, como as maltesinas de colisão invisíveis.

A transformação de um objeto durante o cálculo da sonda (através do separador **Sondas)** é fixada nos resultados do bolo. Se algum objeto marcado na cena for movido mais tarde, o cálculo da sonda e a cozedura têm de ser reserdados.

### <a name="mark-the-navigation-mesh"></a>Marque a malha de navegação
As malsheas de navegação que foram criadas através do fluxo de trabalho da Unidade serão captadas pelo sistema de Acústica. Para usar as suas próprias contas, marque-as a partir do separador **Objects.**

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes do separador Objetos
As partes da página do separador (na imagem após as descrições) são:

1. Os botões de seleção do separador (com o separador **Objetos** selecionados). Use estes botões para mover-se através dos vários passos de uma cozedura acústica, da esquerda para a direita.
1. Uma breve descrição do que pode fazer usando este separador.
1. Filtros disponíveis para a janela da hierarquia. Utilize estas opções para filtrar a janela da hierarquia para objetos do tipo especificado para que possa marcá-los facilmente. Se ainda não marcou nada para a acústica, selecionar as duas últimas opções não lhe mostrará nada. No entanto, estas opções ajudam-no a encontrar objetos depois de marcados.
1. Quando não são selecionados objetos, esta secção mostra o estado de todos os objetos da cena.
    * Total: O número total de objetos ativos e não escondidos.
    * Ignorado: O número de objetos que não são renderizadores de malha ou terrenos.
    * Malha: O número de objetos renderizadores de malha.
    * Terreno: O número de objetos de terreno.
    * Geometria: O número de objetos de malha ou de terreno marcados como **Geometria Acústica**.
    * Navegação: O número de objetos de malha ou de terreno marcados como **Navegação Acústica**. Este número não inclui o NavMesh da Unidade.
1. O número total de objetos "marcadores" na cena, que são apenas renderizadores de malha e terrenos. Utilize as caixas de verificação para marcar (adicione o componente adequado) a esses objetos como geometria ou navegação para a acústica.
1. Quando nada é selecionado, esta nota lembra-lhe selecione objetos para marcação, se necessário. Também pode selecionar uma ou ambas as caixas de verificação para marcar todos os objetos da cena.
1. Quando os objetos são selecionados, esta secção mostra o estado apenas dos objetos selecionados.
1. O número total de objetos selecionados "marcadíveis". Selecionando ou limpando as marcas das caixas de verificação ou não marcas apenas os objetos selecionados.

Se não tiver nada selecionado na sua cena, o separador **Objects** parece a seguinte imagem.

![O separador Objetos Acústicos sem nada selecionado](media/objects-tab-no-selection-detail.png)

Se tiver algo selecionado na sua cena ou janela da hierarquia, o separador parece a seguinte imagem.

![O separador Objetos Acústicos com seleções](media/objects-tab-selection-detail.png)

Se alguns objetos estiverem marcados e alguns não estiverem, as caixas de verificação apropriadas mostram um valor "misto", como a seguinte imagem.

![O separador Objetos Acústicos com uma seleção mista de ícones em destaque](media/mixed-object-selection-detail.png)

Selecione a caixa de verificação para marcar todos os itens. Desmarque-o para desmarcar todos os objetos.

Os objetos podem ser marcados tanto para geometria como para navegação.

## <a name="select-acoustic-materials"></a>Selecione materiais acústicos
Depois de marcados os seus objetos, selecione o botão **Materiais.** Em seguida, atribua materiais acústicos. O sistema de materiais Project Acoustics está ligado ao sistema de materiais visuais da Unidade. Para que dois objetos tenham materiais acústicos separados, devem ter materiais visuais separados.

A seleção de material acústico determina a quantidade de energia sonora que é refletida de cada superfície. O material acústico predefinido tem uma absorção semelhante ao aço. O Projeto Acústica sugere materiais baseados no nome do material visual. Também pode atribuir o material acústico **Personalizado** a um material para ativar um slider de coeficiente de absorção ajustável.

O tempo de reverberação de um determinado material numa sala está inversamente relacionado com o seu coeficiente de absorção. A maioria dos materiais tem valores de absorção num intervalo de 0,01 a 0,20. Os materiais que têm coeficientes de absorção fora desta gama são muito absorventes.

![Um gráfico mostra a correlação negativa do tempo de reverberação e do coeficiente de absorção.](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes do separador Materiais
![O separador materiais de acústica em Unidade](media/materials-tab-detail.png)
1. O botão **Materiais** exibe este separador.
2. Uma breve descrição do que pode fazer usando este separador.
3. Quando esta caixa de verificação é selecionada, apenas são listados materiais utilizados por **objetos marcados como Geometria Acústica.** Caso contrário, todos os materiais que são usados na cena estão listados.
4. Utilize estas opções para alterar a ordem das opções num menu na coluna **Acústica** (#6). Ordenar materiais acústicos por **nome** ou por **absortividade,** de baixo a alto.
5. Uma lista alfabética de materiais que são usados na cena. Se a caixa de verificação **Marcada só** for selecionada (#3), apenas são mostrados materiais utilizados por objetos marcados como **Geometria Acústica.** Selecione um material aqui para selecionar todos os objetos da cena que utilizem esse material.
6. O material acústico a que o material da cena foi atribuído. Selecione qualquer item para alterar o material acústico atribuído a esse material de cena. Para alterar a ordem de triagem destes menus, utilize o **Tipo De Acústica Por** opções (#4).
7. O coeficiente de absorção acústica do material selecionado na coluna à esquerda (#6). Um valor de 0 significa perfeitamente refletor (sem absorção), enquanto 1 significa perfeitamente absorção (sem reflexo). O coeficiente de absorção não pode ser alterado a menos que o material selecionado seja **personalizado.**
8. Para um material marcado como **Custom,** o slider é ativado. Pode mover o slider ou escrever um valor para atribuir um coeficiente de absorção.

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e rever as localizações da sonda de escuta
Depois de atribuir os materiais, mude para o separador **Sondas.** **Selecione Calcular** para colocar pontos de sonda de ouvintes para simulação.

### <a name="what-the-calculate-button-does"></a>O que o botão "Calcular" faz
O botão **Calcular** utiliza a geometria da cena acústica selecionada para preparar a sua cena para simulação:

- Retira a geometria das malhas da cena e calcula um *volume voxel.* O volume voxel é um volume de toda a sua cena composta por pequenos "voxels" cúbicos. O tamanho do Voxel é determinado pela frequência de simulação, que é controlada pela definição de Resolução de **Simulação.** Cada voxel é marcado como "ao ar livre" ou contendo geometria da cena. Se um voxel contiver geometria, o voxel é marcado com o coeficiente de absorção do material que é atribuído a essa geometria.
- Usa as medas de navegação para colocar pontos de sonda de ouvintes. O algoritmo equilibra as preocupações concorrentes da cobertura espacial e do tempo de simulação e do tamanho do ficheiro. Visa garantir que corredores estreitos e espaços pequenos tenham sempre alguma cobertura. As contagens típicas de pontos de sonda variam de 100 para pequenas cenas a alguns milhares para grandes cenas.

Dependendo do tamanho da sua cena e da velocidade da sua máquina, estes cálculos podem demorar vários minutos.

### <a name="review-voxel-and-probe-placement"></a>Rever voxel e colocação de sonda
Pré-visualizar tanto os dados voxel como os locais de ponto de sonda para se certificar de que está pronto para cozer a sua cena. Uma malha de navegação incompleta ou uma geometria acústica em falta ou extra é geralmente facilmente visível na pré-visualização. Ative ou desative a colocação do voxel e da sonda utilizando o menu **Gizmos.**

![O menu Gizmos em Unidade](media/gizmos-menu.png)

Voxels que contêm geometria acústica são mostrados como cubos verdes. Explore a sua cena e verifique se tudo o que deveria ser geometria tem voxels. A câmara da cena tem de estar a cerca de 5 metros do objeto para os voxels mostrarem.

Se comparar voxels criados com resolução grosseira versus resolução fina, verá que os voxels grosseiros são duas vezes maiores.

![A antevisão grosseira voxels no editor da Unidade](media/voxel-cubes-preview.png)

Os resultados da simulação são interpolados entre os locais de ponto de sonda do ouvinte no tempo de execução. Verifique se existem pontos de sonda perto de qualquer lugar onde o jogador é esperado entrar em cena.

![A pré-estreia das sondas no editor da Unidade](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>Tome cuidado com os renomes da cena
O nome da cena é usado para ligar a cena a ficheiros que armazenam a colocação do ponto de sonda e voxelização. Se mudar o nome da cena após a calculação dos pontos de sonda, os dados de atribuição e colocação do material perdem-se e devem ser reexecutados.

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes do separador Sondas
![O separador de sondas acústicas em Unidade](media/probes-tab-detail.png)

1. O botão **sondas** levanta esta conta.
2. Uma breve descrição do que pode fazer neste separador.
3. Utilize estas opções para definir resolução de simulação grossa ou fina. Grosso é mais rápido, mas há trocas. Para mais detalhes, consulte a [resolução do Bolo.](bake-resolution.md)
4. Especifica onde colocar ficheiros de dados de acústica. Selecione o botão "**...**" para aceder a um apanhador de pastas. A localização padrão é *Assets/AcousticsData*. Uma subpasta *editor* também é criada neste local. Para mais informações, consulte [os ficheiros de Dados adicionados pelo processo de cozedura](#Data-Files), mais tarde neste artigo.
5. O prefixo que é especificado aqui é usado para nomear os ficheiros de dados para esta cena. O padrão é "Acoustics_*[Nome da Cena]*".
6. Depois de calcular as sondas, os controlos que acabamos de descrever estão desativados. Selecione o botão **Clear** para apagar os cálculos e ativar os controlos para que possa recalcular com novas definições.
7. Selecione **Calcular** para voxelizar a cena e calcular as localizações do ponto de sonda. O cálculo é feito localmente na sua máquina. Deve ser feito antes de fazer um assado.

Nesta versão do Projeto Acústica, as sondas não podem ser colocadas manualmente. Utilize o processo automatizado no separador **Sondas.**

Para obter mais informações sobre resolução grosseira versus resolução fina, consulte a [resolução do bolo](bake-resolution.md).

## <a name="bake-your-scene-by-using-azure-batch"></a>Asse a sua cena usando o Lote Azure
Você pode assar a sua cena em um cluster de computação na nuvem usando o serviço Azure Batch. O plug-in Project Acoustics Unitity liga-se diretamente ao Lote Azure para instantaneamente, gerir e derrubar um cluster de Lote Azure para cada cozedura. No **separador Assar,** introduza as suas credenciais Azure, selecione um tipo e tamanho de máquina de cluster e, em seguida, selecione **Assar**.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes do separador cozedura
![O separador Acústico Bake em Unidade](media/bake-tab-details.png)

1. O botão **Assar** exibe esta aba.
2. Uma breve descrição do que pode fazer nesta página.
3. Introduza as suas credenciais Azure nestes campos, depois de criada a sua conta Azure. Para mais informações, consulte [Criar uma conta Azure Batch](create-azure-account.md).
4. O campo de etiquetade imagem Docker para o conjunto de ferramentas Acústica.
5. Abre o portal Azure para gerir as suas subscrições, monitorizar o uso e visualizar informações de faturação.
6. Especifica o tipo de nó computacional do Lote Azure para ser utilizado para cálculo. O tipo de nó deve ser suportado pela localização do centro de dados Azure. Se não tem certeza, saia como **Standard_F8s_v2.**
7. O número de nós a utilizar para o cálculo. Este número afeta o tempo de cozedura. É limitado pela sua alocação de núcleo de Lote Azure. A atribuição por defeito só permite dois nós de 8 núcleos ou um nó de 16 núcleos, mas pode ser expandido. Para obter mais informações sobre os constrangimentos de atribuição de núcleos, consulte [Criar uma conta De Lote Azure](create-azure-account.md).
8. Selecione esta caixa de verificação para configurar o seu pool de cálculo para utilizar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nódosos de computação de baixa prioridade têm um custo muito mais baixo. Mas podem nem sempre estar disponíveis ou podem ser antecipados a qualquer momento.
9. A sonda conta para a sua cena, conforme calculado no separador **Sondas.** O número de sondas determina o número de simulações que têm de ser executadas na nuvem. Não pode especificar mais nós do que há sondas.
10. Uma estimativa do tempo que o seu trabalho levará para correr na nuvem, excluindo o tempo de arranque do nó. Depois de o trabalho começar a funcionar, este campo mostra uma estimativa de quanto tempo até recuperar os resultados.
11. A quantidade total de tempo de computação necessária para executar as simulações. Este valor é a quantidade total de tempo de computação do nó que será usado em Azure. Para mais informações, consulte o custo do [bolo Da Estimativa Azure](#Estimating-bake-cost) mais tarde neste artigo.
12. Esta mensagem diz-lhe onde os resultados do bolo serão guardados quando o trabalho estiver terminado.
13. *(Uso avançado apenas:)* Este botão obriga a Unidade a esquecer um assado que submeteu. Por exemplo, se descarregou os resultados utilizando outra máquina, selecione o botão **Clear State** para esquecer esse trabalho. O ficheiro de resultados, quando estiver pronto, *não* será descarregado. *Isto não é o mesmo que cancelar o trabalho. O trabalho, se correr, continuará a correr na nuvem.*
14. Selecione este botão para submeter o assado à nuvem. Enquanto um trabalho está em execução, este botão torna-se **Cancel Job**.
15. Selecione este botão para preparar a [simulação](#Local-bake)acústica no seu PC .
16. Esta área mostra o estado do assado. Quando o bolo está concluído, mostra "Descarregado".

Você pode sempre obter informações completas sobre trabalhos ativos, piscinas de computação e armazenamento no [portal Azure.](https://portal.azure.com)

Enquanto um trabalho está em execução, a etiqueta do botão **assar** muda para **cancelar o trabalho**. Use este botão para cancelar o trabalho que está em andamento. Serão solicitados a confirmar. Cancelar um trabalho não pode ser desfeito. Quando cancelar, não haverá resultados disponíveis, mas ainda será cobrado por qualquer tempo de computação Azure utilizado.

Depois de começar um bolo, pode fechar a Unidade. Dependendo do projeto, do tipo de nó e número de nós, um assado em nuvem pode demorar várias horas. O estado do trabalho no cozimento será atualizado quando recarregar o projeto e abrir a janela acústica. Se o trabalho estiver concluído, o ficheiro de saída será descarregado.

Para segurança, as credenciais Azure são armazenadas na sua máquina local e associadas ao seu editor de Unidade. São usados apenas para estabelecer uma ligação segura com o Azure.

## <a name="find-the-status-of-a-running-job-on-the-azure-portal"></a>Encontre o estado de um trabalho em execução no portal Azure

1. Encontre o ID do trabalho de cozedura no separador **de cozedura.**

    ![O id de trabalho de bake da Unidade realçado no separador Bake](media/unity-job-id.png)  

2. Abra o [portal Azure,](https://portal.azure.com)vá à conta do Lote que foi usada para o assado, e selecione **Jobs**.

    ![A ligação Jobs no portal Azure](media/azure-batch-jobs.png)  

3. Procure a identificação do emprego na lista de empregos.

   ![O estado de trabalho no assado destacado no portal Azure](media/azure-bake-job-status.png)  

4. Selecione o ID de trabalho para ver o estado das tarefas relacionadas e o estado geral do trabalho.

   ![O estado da tarefa de cozer](media/azure-batch-task-state.png)  


### <a name="estimate-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Estimativa do custo do bolo Azure

Para estimar o que um bolo custará, comece com o valor estimado do Custo da **Computação,** que é uma duração. Multiplique esse valor pelo custo horária na sua moeda local para o Tipo de **Nó VM** que seleciona. Note que o resultado não incluirá o tempo de nó que é necessário para pôr os nódosos a funcionar.

Por exemplo, diga que selecione **Standard_F8s_v2** para o seu tipo de nó, que tem um custo de $0,40/h. Se o Custo estimado da **Computação** for de 3 horas e 57 minutos, o custo estimado para executar o trabalho será de $0,40 * ~4 horas = ~$1,60. O custo real será provavelmente um pouco maior devido ao tempo extra para começar os nódosos.

Encontre os nódos de hora em hora a custo de [preços do Lote Azure.](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (Selecione **Compute otimizado** ou **computação** de alto desempenho como categoria.)

## <a name="bake-your-scene-on-your-pc"></a><a name="Local-bake"></a>Asse a sua cena no seu PC
Você também pode assar a sua cena no seu próprio PC. Este método pode ser útil para experimentar acústica para pequenas cenas antes de criar uma conta Azure Batch. Mas note que a simulação de acústica local pode demorar muito tempo dependendo do tamanho da cena.

### <a name="minimum-hardware-requirements"></a>Requisitos mínimos de hardware
* Um processador x86-64 com pelo menos 8 núcleos e 32 GB de RAM
* [Hiper-V habilitado](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) a executar Docker

Como exemplo, nos nossos testes numa máquina de 8 núcleos, intel Xeon E5-1660 @ 3 GHz e 32 GB de RAM:
* Uma pequena cena com 100 sondas demorou cerca de 2 horas para um assado grosso ou 32 horas para um belo assado.
* Uma cena de tamanho médio com 1.000 sondas demorou cerca de 20 horas para um assado grosso ou 21 dias para um belo assado.

### <a name="set-up-docker"></a>Configurar Docker
Instale e configure docker no PC que processará a simulação:
1. Instale [o Docker Desktop](https://www.docker.com/products/docker-desktop).
2. Abra as definições do Docker, vá ao **Advanced**e configure os recursos para pelo menos 8 GB de RAM. Quanto mais CPUs pudera atribuir ao Docker, mais rápido o bolo será concluído.  
![Definições de Amostra Docker](media/docker-settings.png)
1. Vá a **Unidades Partilhadas**e ligue a partilha para a unidade que é usada para o processamento.  
![Docker compartilhou opções de unidade](media/docker-shared-drives.png)

### <a name="run-the-local-bake"></a>Executar o bolo local
1. Selecione o botão preparar o **forno local** no separador **Assar.** Em seguida, selecione uma localização de pasta para guardar os ficheiros de entrada e os scripts de execução para. Em seguida, pode executar o forno em qualquer máquina desde que cumpra os requisitos mínimos de hardware e instale o Docker copiando a pasta para essa máquina.
2. Para iniciar a simulação, execute o script *runlocalbake.bat* no Windows ou o *runlocalbake.sh* script no MacOS. Este script requer a imagem do Project Acoustics Docker com o conjunto de ferramentas necessário para o processamento de simulação e inicia a simulação.
3. Depois de concluída a simulação, copie o ficheiro *.ace* resultante de volta ao seu projeto De Unidade. Para garantir que a Unidade o reconhece como um ficheiro binário, anexa ".bytes" à extensão do ficheiro (por exemplo, "Scene1.ace.bytes"). Os registos detalhados para a simulação são armazenados em *AcousticsLog.txt.* Se tiver algum problema, inspecione este ficheiro para ajudar a diagnosticar o problema.

## <a name="data-files-added-by-the-bake-process"></a><a name="Data-Files"></a>Ficheiros de dados adicionados pelo processo de cozedura

Os quatro ficheiros de dados seguintes são criados durante o processo de cozedura. Um contém os resultados da simulação e as naves com o seu título. Os outros armazenam dados relacionados com editores da Unidade.

Resultado da simulação:
* *Ativos/AcústicaData/Acústica\_[Nome de cena].ace.bytes*: Este ficheiro é a tabela de procuração em tempo de execução. Contém os resultados da simulação e elementos de cena acústica voxelizados. Pode alterar o nome e a localização deste ficheiro no separador **Sondas.**

   *Tenha cuidado para não apagar o ficheiro de resultados da simulação. Não é recuperável a não ser refazer a cena.*

Ficheiros de dados do editor:
* *Ativos/Editor/[Nome de\_Cena] AcousticsParameters.asset*: Este ficheiro armazena os dados que introduz nos campos da UI acústica. Não pode alterar o nome e a localização deste ficheiro.
* *Ativos/AcústicaData/Editor/Acoustics_[SceneName].vox*: Este ficheiro armazena a geometria acústica voxelizada e as propriedades materiais que são calculadas quando seleciona o botão **Calcular** no separador **Sondas.** Pode alterar o nome e a localização deste ficheiro no separador **Sondas.**
* *Ativos/AcústicaData/Editor/Acústica\_[Nome de\_Cena] config.xml*: Este ficheiro armazena parâmetros de simulação que são calculados quando seleciona **Calcular**. Pode alterar o nome e a localização deste ficheiro no separador **Sondas.**

## <a name="set-up-the-acoustics-lookup-table"></a>Configurar a mesa de lookup acústica
Arraste o prefácio do **Projeto Acústica** do painel do projeto para a sua cena:

![O prefácio da Acústica em Unidade](media/acoustics-prefab.png)

Selecione o objeto de jogo **ProjectAcoustics** e vá ao seu painel de inspetores. Especifique a localização do resultado do seu bolo (o ficheiro .ace, em *Assets/AcousticsData*): Arraste-o para o script do Gestor de Acústica ou selecione o botão de círculo ao lado da caixa de texto.

![O Gestor de Acústica prefab em Unidade](media/acoustics-manager.png)

## <a name="next-steps"></a>Passos seguintes
* Explore os controlos de [design da Unidade.](unity-workflow.md)
* Explore conceitos de design de [projeto sústicos.](design-process.md)
