---
title: Tutorial de sobreprojeção de projetos acústicos não reais
titlesuffix: Azure Cognitive Services
description: Este documento descreve o processo de envio de um estortamento acústico usando a extensão de editor inreal.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: michem
ROBOTS: NOINDEX
ms.openlocfilehash: 47946570db305ff3d54dfed9ea6f698e5deb7b72
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704795"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Tutorial de sobreprojeção de projetos acústicos não reais
Este documento descreve o processo de envio de um estortamento acústico usando a extensão de editor inreal.

Há cinco etapas para fazer um distorta:

1. Criar ou marcar sua malha de navegação do Player
2. Geometria de marca acústica
3. Atribuir propriedades de materiais acústicos à geometria
4. Visualizar o posicionamento da investigação
5. Festival

## <a name="open-the-project-acoustics-editor-mode"></a>Abrir o modo do editor de acústica do projeto

Importe o pacote plugin acústicos do projeto para seu projeto. Para obter ajuda com isso, consulte o tópico [integração inreal](unreal-integration.md) . Quando o plug-in estiver integrado, abra a interface do usuário acústica clicando no ícone novo modo acústicos.

![Captura de tela da opção de modo acústico de editor inreal](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Marcar atores para acústicos

A guia objetos é a primeira guia que é exibida quando você abre o modo acústicos. Use essa guia para marcar atores em seu nível, o que adiciona as marcas **AcousticsGeometry** ou **AcousticsNavigation** aos atores.

Selecione um ou mais objetos no contorno mundial ou use a seção **seleção em massa** para ajudar a selecionar todos os objetos de uma categoria específica. Depois que os objetos forem selecionados, use a seção **marcação** para aplicar a marca desejada aos objetos selecionados.

Se algo não tiver nenhuma marca **AcousticsGeometry** nem **AcousticsNavigation** , ele será ignorado na simulação. Há suporte apenas para malhas estáticas, malhas e cenários de navegação. Se você marcar qualquer outra coisa, ela será ignorada.

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes da guia objetos

![Captura de tela da guia objetos acústicos em não real](media/unreal-objects-tab-details.png)

1. Os botões de seleção de guia (guia**objetos** selecionados). Use esses botões para percorrer as várias etapas de fazer um sobrefestival de acústica, de cima para baixo.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. Seletores disponíveis para atores no nível.
4. Clicar em **selecionar** selecionará todos os objetos no nível que correspondam a pelo menos um dos tipos de ator marcados.
5. Clicar em desmarcar **tudo** limpará a seleção atual. Isso é o mesmo que pressionar a tecla escape.
6. Use esses botões de opção para escolher se deseja aplicar a marcação de geometria ou de navegação aos atores selecionados.
7. Clicar na **marca** adicionará a marca selecionada a todos os atores selecionados no momento.
8. Clicar **em** desaparecerá para remover a marca selecionada de todos os atores atualmente selecionados.
9. Clicar em **selecionar marcado** limpará a seleção atual e selecionará todos os atores com a marca atualmente selecionada.
10. Essas estatísticas mostram quantos atores são marcados com cada tipo de marca.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Marcação acústica oclusão e geometria de reflexo

Abra a guia objetos da janela acústica. Marque todos os objetos como acústicos Geometry se eles devem occluder, refletir ou absorver o som. A geometria acústica pode incluir coisas como aterramento, paredes, telhados, Windows & o vidro da janela, Rugs e móveis grandes. Você pode usar qualquer nível arbitrário de complexidade para esses objetos. Como a cena é voxelized antes da simulação, malhas altamente detalhadas, como árvores com muitas folhas pequenas, não são mais dispendiosas de serem distortas do que objetos simplificados.

Não inclua coisas que não devem afetar os acústicos, como as malhas de colisão invisíveis.

A transformação de um objeto no momento do cálculo da investigação (por meio da guia investigações, abaixo) é corrigida nos resultados da distorta. Mover qualquer um dos objetos marcados na cena exigirá a refazer o cálculo da investigação e rebaking a cena.

### <a name="create-or-tag-a-navigation-mesh"></a>Criar ou marcar uma malha de navegação

Uma malha de navegação é usada para posicionar pontos de investigação para simulação. Você pode usar o volume de [limites de malha de NAV não](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)real ou pode especificar sua própria malha de navegação. Você deve marcar pelo menos um objeto como **navegação acústica**. Se você usar a malha de navegação inreal, certifique-se de que você a criou primeiro.

### <a name="acoustics-volumes"></a>Volumes acústicos ###

Há ainda mais personalização avançada que você pode fazer em suas áreas de navegação com **volumes acústicos**. Os **volumes acústicos** são atores que você pode adicionar à sua cena que permitem selecionar áreas a serem incluídas e ignoradas na malha de navegação. O ator expõe uma propriedade que pode ser alternada entre "include" e "exclude". Os volumes "include" garantem que apenas as áreas da malha de navegação dentro deles sejam consideradas e "excluir" os volumes marquem essas áreas para serem ignoradas. Os volumes "exclude" sempre são aplicados após volumes "include". Certifique-se de marcar **volumes acústicos** como uma **navegação acústica** por meio do processo usual na guia objetos. Esses atores ***não*** são marcados automaticamente.

![Captura de tela de propriedades de volume acústicos em não real](media/unreal-acoustics-volume-properties.png)

Os volumes "exclude", principalmente, destinam-se a fornecer um controle refinado sobre onde não colocar investigações para restringir o uso de recursos.

![Captura de tela de excluir volume acústicos em inreal](media/unreal-acoustics-volume-exclude.png)

Os volumes "include" são úteis para criar seções manuais de uma cena, como se você quiser dividir sua cena em várias zonas acústicas. Por exemplo, se você tiver uma grande cena, muitos quilômetros ao quadrado e tiver duas áreas de interesse em que deseja distortar acústicos. Você pode desenhar dois volumes "include" grandes na cena e produzir arquivos ACE para cada um deles por vez. Em seguida, no jogo, você pode usar os volumes de gatilho combinados com as chamadas de plano gráfico para carregar o arquivo ACE apropriado quando o jogador se aproximar de cada bloco.

Os **volumes acústicos** só restringem a navegação e ***não*** a geometria. Cada investigação dentro de um **volume acústicos** "include" ainda efetuará pull de toda a geometria necessária fora do volume ao executar simulações de onda. Portanto, não deve haver nenhum descontinuidades em oclusão ou outros acústicos resultantes do jogador cruzando de uma seção para outra.

## <a name="select-acoustic-materials"></a>Selecionar materiais acústicos

Depois que os objetos forem marcados, clique no botão **materiais** para ir para a guia materiais. Esta guia será usada para especificar as propriedades do material para cada material no nível. Antes que qualquer atores seja marcado, ele ficará em branco.

Os materiais acústicos controlam a quantidade de energia de som refletida de volta de cada superfície. O material acústico padrão tem absorção semelhante ao concreto. O projeto acústicos sugere materiais com base no nome do material da cena.

O tempo de reverberation de um determinado material em uma sala está relacionado inversamente ao seu coeficiente de absorção, com a maioria dos materiais com valores de absorção no intervalo de 0, 1 a 0,20. Os materiais com coeficientes de absorção acima desse intervalo são muito absorbents. Por exemplo, se uma sala soar muito reverberant, altere o material acústico das paredes, piso ou teto para algo de Absorptivity superior. A atribuição de material acústico se aplica a todos os atores que usam esse material de cena.

![Gráfico mostrando a correlação negativa do tempo de reverberation com o coeficiente de absorção](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes da guia materiais

![Captura de tela da guia objetos acústicos em não real](media/unreal-materials-tab-details.png)

1. O botão da guia **materiais** , usado para abrir esta página.
2. Uma breve descrição do que você precisa fazer usando esta página.
3. A lista de materiais usada no nível, extraída dos atores marcados como **AcousticsGeometry**. Clicar em um material aqui selecionará todos os objetos na cena que usam esse material.
4. Mostra o material acústico ao qual o material da cena foi atribuído. Clique em uma lista suspensa para reatribuir um material de cena a um material acústico diferente.
5. Mostra o coeficiente de absorção acústica do material selecionado na coluna anterior. Um valor igual A zero significa perfeitamente reflexivo (sem absorção), enquanto um valor de 1 significa perfeitamente absorptive (sem reflexão). A alteração desse valor atualizará o material acústicos (etapa #4) para **personalizado**.

Se você fizer alterações nos materiais em sua cena, será necessário alternar as guias no plug-in de acústica do projeto para ver essas alterações refletidas na guia **materiais** .

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e revisar locais de investigação de ouvinte

Depois de atribuir os materiais, alterne para a guia **investigações** .

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes da guia investigações

![Captura de tela da guia de investigações acústicas em não real](media/unreal-probes-tab-details.png)

1. O botão da guia **investigações** usado para abrir esta página
2. Uma breve descrição do que você precisa fazer usando esta página
3. Use isso para escolher uma resolução de simulação grande ou fina. Grande é mais rápido, mas tem certas compensações. Consulte a [resolução](bake-resolution.md) de distorta abaixo para obter detalhes.
4. Escolha o local onde os arquivos de dados acústicos devem ser colocados usando esse campo. Clique no botão com "..." para usar um seletor de pasta. Para obter mais informações sobre arquivos de dados, consulte [os arquivos de dados](#Data-Files) abaixo.
5. Os arquivos de dados para essa cena serão nomeados usando o prefixo fornecido aqui. O padrão é "[nome do nível] _AcousticsData".
6. Clique no botão **Calculate** para voxelize a cena e calcular os locais de pontos de investigação. Isso é feito localmente em seu computador e deve ser feito antes de fazer uma distorta. Depois que as investigações forem calculadas, os controles acima serão desabilitados e esse botão será alterado para dizer **claro**. Clique no botão **limpar** para apagar os cálculos e habilitar os controles para que você possa recalcular usando novas configurações.

As investigações devem ser colocadas por meio do processo automatizado fornecido na guia **investigações** .


### <a name="what-the-calculate-button-calculates"></a>O que o botão "Calculate" calcula

O botão **Calculate** Obtém todos os dados que você forneceu até agora (geometria, navegação, materiais e configuração grande/fina) e passa por várias etapas:

1. Ele usa a geometria das malhas de cena e calcula um volume VOXEL. O volume VOXEL é um volume tridimensional que inclui toda a sua cena e é composto por um pequeno "voxels" cúbico. O tamanho do voxels é determinado pela frequência de simulação, que é definida pela configuração de **resolução de simulação** . Cada voxel é marcado como sendo "Open Air" ou que contém a geometria da cena. Se um VOXEL contiver Geometry, o VOXEL será marcado com o coeficiente de absorção do material atribuído a essa geometria.
2. Em seguida, ele usa os dados de navegação para calcular locais de interesse acústicos onde o jogador pode ir. Ele tenta encontrar um conjunto razoavelmente pequeno desses locais que inclui áreas menores, como doorways e corredores, e, em seguida, para salas, para abrir espaços. Para cenas pequenas, isso geralmente é inferior a 100 locais, enquanto grandes cenas podem ter até 1000.
3. Para cada um dos locais de ouvinte finais que ele computa, ele determina um número de parâmetros, como como "Open" é o espaço, o tamanho da sala em que se encontra, etc.
4. Os resultados desses cálculos são armazenados em arquivos no local especificado (consulte [os arquivos de dados](#Data-Files) abaixo)

Dependendo do tamanho da sua cena e da velocidade do seu computador, esses cálculos podem levar vários minutos.

Depois que esses cálculos forem concluídos, você poderá visualizar os dados do Voxel e os locais do ponto de investigação para ajudar a garantir que o torta lhe dará bons resultados. Coisas como uma malha de navegação incorreta ou geometria ausente/extra normalmente serão rapidamente visíveis na visualização para que você possa corrigi-la.


## <a name="debug-display"></a>Exibição de depuração

Após a conclusão do cálculo da investigação, um novo ator aparecerá na contorno mundial chamada **AcousticsDebugRenderer**. Marcar as caixas de seleção **renderizar testes** e **renderizar voxels** habilitará a exibição de depuração dentro do visor do editor.

![Captura de tela mostrando o ator do processador de depuração acústica no editor inreal](media/acoustics-debug-renderer.png)

Se você não vir nenhum voxels ou investigação sobreposto ao seu nível, certifique-se de que a renderização em tempo real esteja habilitada no visor.

![Captura de tela da opção de renderização em tempo real de](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels são mostrados na janela cena como cubos verdes em relação à geometria participante. Voxels que contêm apenas ar não são mostrados. Há uma grande caixa verde em toda a sua cena que denota o volume VOXEL completo que será usado na simulação.
Mova-se para a sua cena e verifique se a geometria acústica occluding tem voxels. Além disso, verifique se objetos não acústicos, como malhas de colisão, não foram voxelizeds. A câmera de cena deve estar em cerca de 5 metros do objeto para o voxels mostrar.

Se você comparar o voxels criado com alta resolução versus resolução refinada, verá que o voxels grosso é duas vezes maior.

![Captura de tela da visualização acústica voxels no editor inreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Pontos de investigação

Os pontos de investigação são sinônimos de possíveis locais de Player (ouvinte). Quando trazendo, a simulação calcula os acústicos que conectam todos os possíveis locais de origem a cada ponto de investigação. Em tempo de execução, o local do ouvinte é interpolado entre pontos de investigação próximos.

É importante verificar se os pontos de investigação existem em qualquer lugar em que o Player deve viajar na cena. Os pontos de investigação são colocados na malha de navegação pelo mecanismo acústicos do projeto e não podem ser movidos ou editados, portanto, verifique se a malha de navegação abrange todos os locais possíveis do Player inspecionando os pontos de investigação.

![Captura de tela da visualização de investigações acústicas em modo não real](media/unreal-probes-preview.png)

Consulte a [resolução](bake-resolution.md) de distorta para obter mais detalhes sobre a resolução de alta velocidade versus multas.

## <a name="bake-your-level-using-azure-batch"></a>Distortar seu nível usando o lote do Azure

Você pode distortar sua cena com um cluster de computação na nuvem usando o serviço de lote do Azure. O plug-in acústico inreal do projeto se conecta diretamente ao lote do Azure para instanciar, gerenciar e subdividir um cluster do lote do Azure para cada distorta. Na guia distorta, insira suas credenciais do Azure, selecione um tipo de máquina de cluster e um tamanho e clique em distortar.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes da guia distorta

![Captura de tela da guia estortar acústicos em não real](media/unreal-bake-tab-details.png)

1. O botão de sobreformação da guia é usado para abrir esta página.
2. Uma breve descrição do que fazer nesta página.
3. Campos para inserir suas credenciais do Azure depois que sua conta do Azure tiver sido criada. Para obter mais informações, consulte [criar uma conta do lote do Azure](create-azure-account.md).
4. Inicie o portal do Azure para gerenciar suas assinaturas, monitorar o uso e exibir informações de cobrança etc. 
5. Tipo de nó de computação do lote do Azure a ser usado para o cálculo. O tipo de nó deve ser suportado pelo local de data center do Azure. Se não tiver certeza, deixe em **Standard_F8s_v2**.
6. Número de nós a serem usados para este cálculo. O número digitado aqui afeta o tempo para concluir o distorta e é limitado pela sua alocação principal do lote do Azure. A alocação padrão permite apenas dois nós de 8 núcleos ou um nó de núcleo 1 16, mas pode ser expandida. Para obter mais informações sobre as principais restrições de alocação, consulte [criar uma conta do lote do Azure](create-azure-account.md).
7. Marque esta caixa de seleção para configurar seu pool de computação para usar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nós de computação de baixa prioridade têm um custo muito menor, mas nem sempre podem estar disponíveis ou podem ser admitidos a qualquer momento.
8. A quantidade de tempo decorrido que deve levar para que o trabalho seja executado na nuvem. Isso não inclui o tempo de inicialização do nó. Depois que o trabalho começar a ser executado, isso será sobre quanto tempo deve ser antes de você retornar os resultados. Observe que essa é apenas uma estimativa.
9. A quantidade total de tempo de computação necessário para executar as simulações. Esta é a quantidade total de tempo de computação do nó que será usada no Azure. Consulte [estimando o custo de torta](#Estimating-bake-cost) abaixo para obter mais informações sobre como usar esse valor.
10. Clique no botão de distorta para enviar o cotorta para a nuvem. Enquanto um trabalho estiver em execução, isso mostrará **Cancelar trabalho** em vez disso. Se houver erros nessa guia ou se o fluxo de trabalho na guia **investigações** não tiver sido concluído, esse botão será desabilitado.
11. A contagem de investigações para sua cena, conforme calculada na guia **investigações** . O número de investigações determina o número de simulações que precisam ser executadas na nuvem. Você não pode especificar mais nós do que há investigações.
12. Essa mensagem informa o status atual do trabalho ou se há erros nessa guia, quais são esses erros.

Você sempre pode obter informações completas sobre trabalhos ativos, pools de computação e armazenamento no [portal do Azure](https://portal.azure.com).

Enquanto um trabalho estiver executando as alterações do botão de **torta** para **cancelar o trabalho**. Use esse botão para cancelar o trabalho em andamento. O cancelamento de um trabalho não pode ser desfeito, nenhum resultado estará disponível e você ainda será cobrado por qualquer tempo de computação do Azure usado antes do cancelamento.

Depois de começar uma distorta, você pode fechar o inreal. Dependendo do projeto, do tipo de nó e do número de nós, um distorta de nuvem pode levar várias horas. O status do trabalho de torta será atualizado quando você recarregar o projeto e abrir a janela acústica. Se o trabalho tiver sido concluído, o arquivo de saída será baixado.

As credenciais do Azure são armazenadas com segurança em seu computador local e associadas a seu projeto inreal. Eles são usados exclusivamente para estabelecer uma conexão segura com o Azure.

### <a name="Estimating-bake-cost"></a>Estimando o custo de torta do Azure

Para estimar o custo de uma determinada torta, use o valor mostrado para o **custo estimado de computação**, que é uma duração e multiplique isso pelo custo por hora em sua moeda local do tipo de **nó de VM** selecionado. O resultado não incluirá o tempo de nó necessário para colocar os nós em funcionamento. Por exemplo, se você selecionar **Standard_F8s_v2** para o tipo de nó, que tem um custo de $0.40/HR, e o custo estimado de computação for de 3 horas e 57 minutos, o custo estimado para executar o trabalho será $0.40 * ~ 4 horas = ~ $1.60. O custo real provavelmente será um pouco maior devido ao tempo extra para iniciar os nós. Você pode encontrar o custo de nó por hora na página de [preços do lote do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (selecione "computação otimizada" ou "computação de alto desempenho" para a categoria).

### <a name="reviewing-the-bake-results"></a>Revisando os resultados da distorta

Depois que a distorta for concluída, verifique se os pontos de voxels e investigação estão em seus locais esperados executando o plug-in de tempo de execução.

## <a name="Data-Files"></a>Arquivos de dados

Há quatro arquivos de dados criados por esse plug-in em vários pontos. Apenas uma delas é necessária em tempo de execução e é colocada na pasta Content/acústicas do projeto, que é automaticamente adicionada ao caminho de empacotamento do seu projeto. Os outros três estão dentro da pasta de dados acústicas e não são empacotados.

* **[Project]/Config/ProjectAcoustics.cfg**: Esse arquivo armazena os dados inseridos nos campos na interface do usuário do modo acústicos. O local e o nome deste arquivo não podem ser alterados. Há outros valores armazenados nesse arquivo que afetam o distorta, mas eles são para usuários avançados e não devem ser alterados.
* **[Projeto]/Content/Acoustics/[nívelname]\_AcousticsData. Ace**: Esse arquivo é o que é criado durante a simulação de distorta e contém os dados de pesquisa usados pelo tempo de execução para renderizar os acústicos de sua cena. O local e o nome desse arquivo podem ser alterados usando os campos na guia **investigações** . Se você quiser renomear esse arquivo depois que ele tiver sido criado, exclua o UAsset de seu projeto inreal, renomeie o arquivo fora de inreal no explorador de arquivos e, em seguida, importe novamente esse arquivo para inreal para produzir um novo UAsset. Renomear o UAsset por si só não funcionará.
* **[Projeto]/plugins/ProjectAcoustics/AcousticsData/[nívelname]\_AcousticsData. Vox**: Esse arquivo armazena a geometria de voxelized acústicos e as propriedades de material. Calculado usando o botão **Calculate** na guia **investigações** . O local e o nome desse arquivo podem ser alterados usando os campos na guia **investigações** .
* **[Projeto]/plugins/ProjectAcoustics/AcousticsData/[nívelname]\_AcousticsData\_config. xml**: Esse arquivo armazena os parâmetros computados usando o botão **Calculate** na guia **investigações** . O local e o nome desse arquivo podem ser alterados usando os campos na guia **investigações** .

Tome cuidado para não excluir o arquivo *. Ace baixado do Azure. Esse arquivo não é recuperável, exceto por rebaking a cena.

## <a name="next-steps"></a>Passos Seguintes
* Explore os [controles de design para](unreal-workflow.md) que não sejam reais
* Explore os [conceitos de design acústicos do projeto](design-process.md)

