---
title: Projeto Tutorial de Cozedura Irreal
titlesuffix: Azure Cognitive Services
description: Este documento descreve o processo de apresentação de uma cozedura acústica utilizando a extensão do editor irreal.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 7a868a5f9b06499e23710399733b0659d97f900d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854895"
---
# <a name="project-acoustics-unreal-bake-tutorial"></a>Projeto Tutorial de Cozedura Irreal
Este documento descreve o processo de apresentação de uma cozedura acústica utilizando a extensão do editor irreal.

Há cinco passos para fazer um assado:

1. Crie ou marque a malha de navegação do seu jogador
2. Geometria acústica de etiqueta
3. Atribuir propriedades de materiais acústicos à geometria
4. Colocação da sonda de pré-visualização
5. Assar

## <a name="open-the-project-acoustics-editor-mode"></a>Abra o modo editor do Projeto Acústica

Importe o pacote de plugin do Projeto Acústica para o seu projeto. Para obter ajuda, consulte o tema [da Integração Irreal.](unreal-integration.md) Assim que o plugin estiver integrado, abra o UI da Acústica clicando no novo ícone do Modo Acústico.

![Screenshot da opção Modo Acústica de Editor Irreal](media/acoustics-mode.png)

## <a name="tag-actors-for-acoustics"></a>Tag actors para acústica

O separador de objetos é o primeiro separador que é exibido quando abre o Modo Acústica. Utilize este separador para marcar atores ao seu nível, o que adiciona as **tags Acústica de Geometria** ou **Acústica** aos atores.

Selecione um ou mais objetos no World Outliner ou utilize a secção **de Seleção** a Granel para ajudar a selecionar todos os objetos de uma categoria específica. Uma vez selecionados objetos, utilize a secção **de marcação** para aplicar a etiqueta desejada nos objetos selecionados.

Se algo não tiver **a Acústica geometria** nem a etiqueta **Acústica de Navegação,** será ignorado na simulação. Apenas maltratas estáticas, malshes de navegação e paisagens são apoiadas. Se marcar mais alguma coisa, será ignorado.

### <a name="for-reference-the-objects-tab-parts"></a>Para referência: As partes do separador Objetos

![Screenshot do separador de objetos acústicos em irreal](media/unreal-objects-tab-details.png)

1. Os botões de seleção do separador (separador**de objetos** selecionados). Use estes botões para percorrer os vários passos de fazer uma cozedura acústica, de cima para baixo.
2. Uma breve descrição do que precisa fazer usando esta página.
3. Selecionadores disponíveis para atores no nível.
4. Clicar **Selecione** todos os objetos do nível que correspondam a pelo menos um dos tipos de atores verificados.
5. Clique em **Desseleccionar tudo** irá limpar a seleção atual. Isto é o mesmo que bater na chave de fuga.
6. Utilize estes botões de rádio para escolher se aplica a etiqueta geometria ou de navegação aos atores selecionados.
7. Clicar na **Tag** irá adicionar a etiqueta selecionada a todos os atores atualmente selecionados.
8. Clicar **em Untag** removerá a etiqueta selecionada de todos os atores atualmente selecionados.
9. Clicar em **Select Tagged** limpará a seleção atual e selecionará todos os intervenientes com a etiqueta atualmente selecionada.
10. Estas estatísticas mostram quantos atores estão marcados com cada tipo de etiqueta.

### <a name="tag-acoustics-occlusion-and-reflection-geometry"></a>Tag acústica oclusão e geometria de reflexão

Abra o separador Objetos da janela Acústica. Marque quaisquer objetos como Geometria Acústica se estes oclusem, reflitam ou absorvam o som. A geometria acústica pode incluir coisas como chão, paredes, telhados, janelas & vidros de janela, tapetes e móveis grandes. Pode utilizar qualquer nível arbitrário de complexidade para estes objetos. Como a cena é voxelizada antes da simulação, malhas altamente detalhadas, como árvores com muitas folhas pequenas, não são mais dispendiosas para assar do que objetos simplificados.

Não inclua coisas que não devem afetar a acústica, como as maltesinas de colisão invisíveis.

A transformação de um objeto no momento do cálculo da sonda (através do separador Sonda, abaixo) é fixada nos resultados do bolo. Mover qualquer um dos objetos marcados na cena exigirá refazer o cálculo da sonda e recozer a cena.

### <a name="create-or-tag-a-navigation-mesh"></a>Criar ou marcar uma malha de navegação

Uma malha de navegação é usada para colocar pontos de sonda para simulação. Pode utilizar o volume de limites de malha de malha de [navegação](https://api.unrealengine.com/INT/Engine/AI/BehaviorTrees/QuickStart/2/index.html)da Unreal, ou pode especificar a sua própria malha de navegação. Deve marcar pelo menos um objeto como **Navegação Acústica**. Se utilizar a malha de navegação da Unreal, certifique-se de que a tem construída primeiro.

### <a name="acoustics-volumes"></a>Volumes de Acústica ###

Há ainda uma personalização avançada que pode fazer nas suas áreas de navegação com **Volumes De Acústica**. **Os volumes de acústica** são atores que pode adicionar à sua cena que lhe permitem selecionar áreas para incluir e ignorar da malha de navegação. O ator expõe uma propriedade que pode ser trocada entre "Incluir" e "Excluir". Os volumes "Incluir" asseguram que apenas as áreas da malha de navegação no seu interior são consideradas e os volumes "Excluir" marcam essas áreas a serem ignoradas. Os volumes "Excluir" são sempre aplicados após volumes "Incluir". Certifique-se de marcar **Volumes Acústicos** como **Navegação Acústica** através do processo habitual no separador Objetos. Estes atores ***não*** são automaticamente marcados.

![Screenshot das propriedades do volume de acústica em Irreal](media/unreal-acoustics-volume-properties.png)

Os volumes "excluir" destinam-se principalmente a dar um controlo fino sobre onde não colocar sondas para a perto da utilização dos recursos.

![Screenshot de Excluir Volume Acústico em Irreal](media/unreal-acoustics-volume-exclude.png)

Os volumes "Incluir" são úteis para criar secções manuais de uma cena, como se quiser dividir a sua cena em várias zonas acústicas. Por exemplo, se você tem uma grande cena, muitos quilómetros ao quadrado, e você tem duas áreas de interesse que você quer assar acústica em. Pode desenhar dois grandes volumes "Incluir" na cena e produzir ficheiros ACE para cada um deles um de cada vez. Em seguida, no jogo, pode utilizar volumes de gatilho combinados com chamadas de plantas para carregar o ficheiro ACE apropriado quando o leitor se aproxima de cada azulejo.

**Os volumes de acústica** limitam apenas a navegação e ***não*** a geometria. Cada sonda dentro de um **volume de acústica** "Incluir" ainda puxará toda a geometria necessária fora do volume ao realizar simulações de ondas. Portanto, não deve haver descontinuidades na oclusão ou outra acústica resultanteda da travessia do jogador de uma secção para outra.

## <a name="select-acoustic-materials"></a>Selecione materiais acústicos

Assim que os seus objetos estiverem marcados, clique no botão **Materiais** para ir ao Separador de Materiais. Este separador será utilizado para especificar as propriedades do material para cada material no nível. Antes que os atores sejam marcados, ficará em branco.

Os materiais acústicos controlam a quantidade de energia sonora refletida de cada superfície. O material acústico padrão tem absorção semelhante ao betão. O Projeto Acústica sugere materiais baseados no nome do material da cena.

O tempo de reverberação de um determinado material numa sala está inversamente relacionado com o seu coeficiente de absorção, com a maioria dos materiais a ter valores de absorção na gama 0,01 a 0,20. Os materiais com coeficientes de absorção acima desta gama são muito absorventes. Por exemplo, se uma sala soar demasiado reverberante, mude o material acústico das paredes, do chão ou do teto para algo de maior absortividade. A atribuição de material acústico aplica-se a todos os intervenientes que usam esse material de cena.

![Gráfico mostrando correlação negativa do tempo de reverberação com coeficiente de absorção](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>Para referência: Partes do separador Materiais

![Screenshot do separador de objetos acústicos em irreal](media/unreal-materials-tab-details.png)

1. O botão de separador **Materiais,** usado para trazer esta página.
2. Uma breve descrição do que precisa fazer usando esta página.
3. A lista de materiais utilizados no nível, retirados dos atores marcados como **AcousticsGeometry.** Clicar num material aqui irá selecionar todos os objetos da cena que usam esse material.
4. Mostra o material acústico a que o material da cena foi atribuído. Clique numa queda para reatribuir um material de cena a um material acústico diferente.
5. Mostra o coeficiente de absorção acústica do material selecionado na coluna anterior. Um valor de zero significa perfeitamente refletor (sem absorção), enquanto um valor de 1 significa perfeitamente absorvente (sem reflexão). A alteração deste valor atualizará o Material de Acústica (passo #4) para **Custom**.

Se fizer alterações nos materiais da sua cena, terá de trocar os separadores no plugin project Acoustics para ver essas alterações refletidas no separador **Materiais.**

## <a name="calculate-and-review-listener-probe-locations"></a>Calcular e rever as localizações da sonda de escuta

Depois de atribuir os materiais, mude para o separador **Sondas.**

### <a name="for-reference-parts-of-the-probes-tab"></a>Para referência: Partes do separador Sondas

![Screenshot do separador sonda acústica em irreal](media/unreal-probes-tab-details.png)

1. O botão de separador **Sondas** usado para trazer esta página
2. Uma breve descrição do que precisa fazer usando esta página
3. Utilize isto para escolher uma resolução de simulação grosseira ou fina. Grosso é mais rápido, mas tem certas trocas. Consulte a [Resolução do Bolo](bake-resolution.md) abaixo para mais detalhes.
4. Escolha o local onde os ficheiros de dados de acústica devem ser colocados utilizando este campo. Clique no botão com "..." para usar um apanhador de pastas. Para obter mais informações sobre ficheiros de dados, consulte [ficheiros de dados](#Data-Files) abaixo.
5. Os ficheiros de dados desta cena serão nomeados usando o prefixo fornecido aqui. O padrão é "[Nome de nível]_AcousticsData".
6. Clique no botão **Calcular** para voxelizar a cena e calcular as localizações do ponto de sonda. Isto é feito localmente na sua máquina, e deve ser feito antes de fazer um assado. Depois de calculadas as sondas, os controlos acima serão desativados, e este botão mudará para dizer **Clear**. Clique no botão **Clear** para apagar os cálculos e ativar os controlos para que possa recalcular utilizando novas definições.

As sondas devem ser colocadas através do processo automatizado fornecido no separador **Sondas.**


### <a name="what-the-calculate-button-calculates"></a>O que o botão "Calcular" calcula

O botão **Calcular** leva todos os dados que forneceu até agora (geometria, navegação, materiais e a configuração grosseira/fina) e passa por vários passos:

1. Retira a geometria das malhas da cena e calcula um volume voxel. O volume voxel é um volume tridimensional que encerra toda a sua cena, e é composto por pequenos "voxels" cúbicos. O tamanho dos voxels é determinado pela frequência de simulação, que é definida pela definição de Resolução de **Simulação.** Cada voxel é marcado como sendo "ao ar livre" ou contendo geometria da cena. Se um voxel contiver geometria, então o voxel é marcado com o coeficiente de absorção do material atribuído a essa geometria.
2. Em seguida, utiliza os dados de navegação para calcular locais acústicos interessantes para onde o leitor pode ir. Tenta encontrar um conjunto razoavelmente pequeno destes locais que inclua áreas mais pequenas, como portas e corredores, e depois para quartos, para espaços abertos. Para pequenas cenas, este normalmente é menos de 100 locais, enquanto grandes cenas podem ter até mil.
3. Para cada um dos locais finais de ouvintes que calcula, determina uma série de parâmetros como o quão "aberto" é o espaço, o tamanho da sala em que se encontra, etc.
4. Os resultados destes cálculos são armazenados em ficheiros no local que especifica (Ver [Ficheiros](#Data-Files) de Dados abaixo)

Dependendo do tamanho da sua cena e da velocidade da sua máquina, estes cálculos podem demorar vários minutos.

Uma vez concluídos estes cálculos, pode pré-visualizar os dados do voxel e os locais de ponto de sonda para ajudar a garantir que o bolo lhe dará bons resultados. Coisas como uma má malha de navegação ou uma geometria em falta/extra serão normalmente visíveis rapidamente na pré-visualização para que possa corrigi-la.


## <a name="debug-display"></a>Exibição de depuração

Após o cálculo da sonda ser concluído, um novo ator aparecerá no World Outliner chamado **AcousticsDebugRenderer**. A verificação das caixas de verificação **Render Probes** e **Render Voxels** ativará o ecrã de depuração no interior do viewport do editor.

![Screenshot mostrando ator renderizador de Debug Acústica em Editor Irreal](media/acoustics-debug-renderer.png)

Se não vir voxels ou sondas sobrepostas ao seu nível, certifique-se de que a renderização em tempo real está ativada no viewport.

![Screenshot da opção de renderização em tempo real em Unreal](media/unreal-real-time-rendering.png)

### <a name="voxels"></a>Voxels

Voxels são mostrados na janela da cena como cubos verdes em torno da geometria participante. Voxels que contêm apenas ar não são mostrados. Há uma grande caixa verde em toda a sua cena que denota o volume completo de voxel que será usado na simulação.
Mova-se ao redor da sua cena e verifique se a geometria acústico-occluding tem voxels. Além disso, verifique se objetos não acústicos, como as maltes de colisão, não foram voxelizados. A câmara da cena tem de estar a cerca de 5 metros do objeto para os voxels mostrarem.

Se compararmos os voxels criados com resolução grosseira vs resolução fina, verá que os voxels grosseiros são duas vezes maiores.

![Screenshot da pré-estreia da Acústica voxels em editor irreal](media/unreal-voxel-preview.png)

### <a name="probe-points"></a>Pontos de sonda

Os pontos de sonda são sinónimo de possíveis localizações de jogadores (ouvintes). Ao cozer, a simulação calcula a acústica que liga todas as possíveis localizações de origem a cada ponto de sonda. No tempo de execução, a localização do ouvinte é interpolada entre os pontos de sonda nas proximidades.

É importante verificar se os pontos de sonda existem em qualquer lugar onde o jogador deve viajar no local. Os pontos de sonda são colocados na malha de navegação pelo motor Project Acoustics e não podem ser movidos ou editados, por isso certifique-se de que a malha de navegação cobre todos os locais possíveis do jogador inspecionando os pontos de sonda.

![Screenshot das sondas acústicas pré-visualização em Unreal](media/unreal-probes-preview.png)

Consulte a [Resolução de Bolos](bake-resolution.md) para obter mais detalhes sobre resolução grosseira vs fina.

## <a name="bake-your-level-using-azure-batch"></a>Asse o seu nível usando o Lote Azure

Você pode assar a sua cena com um cluster de computação na nuvem usando o serviço Azure Batch. O plugin Project Acoustics Unreal liga-se diretamente ao Lote Azure para instantaneamente, gerir e derrubar um cluster de Lote Azure para cada cozedura. No separador Assar, introduza as suas credenciais Azure, selecione um tipo e tamanho de máquina de cluster e clique em Cozer.

### <a name="for-reference-parts-of-the-bake-tab"></a>Para referência: Partes do separador de cozedura

![Screenshot do separador de cozedura acústica em unreal](media/unreal-bake-tab-details.png)

1. O botão Do Guia de Cozedura usado para trazer esta página.
2. Uma breve descrição do que fazer nesta página.
3. Campos para introduzir as suas Credenciais Azure uma vez criada a sua conta Azure. Para mais informações, consulte [Criar uma conta de lote azure](create-azure-account.md).
4. Lance o portal Azure para gerir as suas subscrições, monitorizar o uso e visualizar informações de faturação, etc. 
5. Tipo de nó de computação de lote azure para utilizar para o cálculo. O tipo de nó deve ser suportado pela localização do centro de dados Azure. Se não tiver a certeza, saia em **Standard_F8s_v2.**
6. Número de nós para usar para este cálculo. O número de entrada aqui afeta o tempo para completar o bolo e é limitado pela sua atribuição de núcleo de lote Azure. A atribuição por defeito só permite dois nós de 8 núcleos ou um nó de 16 núcleos, mas pode ser expandido. Para obter mais informações sobre os constrangimentos de atribuição de núcleos, consulte [Criar uma conta de lote Azure](create-azure-account.md).
7. Selecione esta caixa de verificação para configurar o seu pool de cálculo para utilizar [nós de baixa prioridade](https://docs.microsoft.com/azure/batch/batch-low-pri-vms). Os nódos os computação de baixa prioridade têm um custo muito mais baixo, mas podem nem sempre estar disponíveis ou podem ser antecipados a qualquer momento.
8. A quantidade de tempo decorrido que se espera que leve para o seu trabalho correr na nuvem. Isto não inclui o tempo de arranque do nó. Uma vez que o trabalho começa a funcionar, isto é sobre quanto tempo deve demorar até recuperar os resultados. Note que esta é apenas uma estimativa.
9. A quantidade total de tempo de computação necessária para executar as simulações. Esta é a quantidade total de tempo de computação do nó que será usado em Azure. Consulte estimar o custo do [bolo](#Estimating-bake-cost) abaixo para obter mais informações sobre a utilização deste valor.
10. Clique no botão Assar para submeter o assado à nuvem. Enquanto um trabalho está a decorrer, isto mostra **o Cancelamento de Trabalho.** Se houver erros neste separador, ou se o fluxo de trabalho no separador **Sondas** não tiver sido concluído, este botão será desativado.
11. A sonda conta para a sua cena, conforme calculado no separador **Sondas.** O número de sondas determina o número de simulações que precisam de ser executadas na nuvem. Não pode especificar mais nós do que há sondas.
12. Esta mensagem diz-lhe o estado atual do trabalho, ou se houver algum erro neste separador, quais são esses erros.

Você pode sempre obter informações completas sobre trabalhos ativos, piscinas de computação e armazenamento no [portal Azure.](https://portal.azure.com)

Enquanto um trabalho está a executar as alterações do botão **de cozedura** para cancelar **o trabalho**. Use este botão para cancelar o trabalho em curso. Cancelar um trabalho não pode ser desfeito, não haverá resultados disponíveis, e ainda será cobrado por qualquer tempo de computação Azure usado antes do cancelamento.

Uma vez que você começa um bolo, você pode fechar Unreal. Dependendo do projeto, do tipo de nó e número de nós, um assado em nuvem pode demorar várias horas. O estado do trabalho no cozimento será atualizado quando recarregar o projeto e abrir a janela acústica. Se o trabalho estiver concluído, o ficheiro de saída será descarregado.

As credenciais Azure são armazenadas de forma segura na sua máquina local e associadas ao seu projeto Unreal. São utilizados exclusivamente para estabelecer uma ligação segura ao Azure.

### <a name="estimating-azure-bake-cost"></a><a name="Estimating-bake-cost"></a>Estimativa do custo do bolo Azure

Para estimar o custo de um determinado bolo, pegue no valor mostrado para o Custo Estimado da **Computação**, que é uma duração, e multiplique-o pelo custo horário na sua moeda local do Tipo de **Nó VM** que selecionou. O resultado não incluirá o tempo necessário para pôr os nódosos a funcionar. Por exemplo, se selecionar **Standard_F8s_v2** para o seu tipo de nó, que tem um custo de $0,40/h, e o Custo estimado da Computação é de 3 horas e 57 minutos, o custo estimado para executar o trabalho será de $0,40 * ~4 horas = ~$1,60. O custo real será provavelmente um pouco maior devido ao tempo extra para começar os nódosos. Pode encontrar o custo do nó horária na página de preços do [lote Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux) (selecione "Compute otimizado" ou "computação de alto desempenho" para a categoria).

### <a name="reviewing-the-bake-results"></a>Rever os resultados do bolo

Depois de o forno estar concluído, verifique se os voxels e os pontos de sonda estão nos locais esperados, executando o plugin de tempo de funcionamento.

## <a name="data-files"></a><a name="Data-Files"></a>Ficheiros de dados

Existem quatro ficheiros de dados criados por este plugin em vários pontos. Apenas um deles é necessário no prazo de funcionação e é colocado na pasta Conteúdo/Acústica do seu projeto, que é automaticamente adicionada ao caminho de embalagem do seu projeto. Os outros três estão dentro da pasta Dados da Acústica e não estão embalados.

* **[Projeto]/Config/ProjectAcoustics.cfg**: Este ficheiro armazena os dados que introduz nos campos do Modo Acústico UI. A localização e o nome deste ficheiro não podem ser alterados. Existem outros valores armazenados neste ficheiro que afetam o bolo, mas são para utilizadores avançados e não devem ser alterados.
* **[Projeto]/Conteúdo/Acústica/[Nome de\_Nível] AcústicaData.ace**: Este ficheiro é o que é criado durante a simulação do bolo, e contém os dados de procura utilizados pelo tempo de execução para renderizar a acústica da sua cena. A localização e o nome deste ficheiro podem ser alterados utilizando os campos no Separador **de Sondas.** Se pretender renomear este ficheiro depois de ter sido criado, elimine o UAsset do seu projeto Unreal, mude o nome do ficheiro fora do Unreal no File Explorer e, em seguida, reimporte este ficheiro para Unreal para produzir um novo UAsset. Renomear o UAsset por si só não vai funcionar.
* **[Projeto]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData.vox**: Este ficheiro armazena a geometria acústica voxelizada e as propriedades do material. Computada utilizando o botão **Calcular** no separador **de sondas.** A localização e o nome deste ficheiro podem ser alterados utilizando os campos no Separador **de Sondas.**
* **[Projeto]/Plugins/ProjectAcoustics/AcousticsData/[LevelName]\_AcousticsData\_config.xml**: Este ficheiro armazena parâmetros computados utilizando o botão **Calcular** no separador **sondas.** A localização e o nome deste ficheiro podem ser alterados utilizando os campos no Separador **de Sondas.**

Tome cuidado para não apagar o ficheiro *.ace descarregado do Azure. Este ficheiro não é recuperável a não ser refazer a cena.

## <a name="next-steps"></a>Passos seguintes
* Explore os controlos de [design para Unreal](unreal-workflow.md)
* Explore os [conceitos](design-process.md) de design do Projeto Acústica

