---
title: Identifie imagens em um projeto de rotulagem
titleSuffix: Azure Machine Learning
description: Saiba como utilizar as ferramentas de marcação de dados num projeto de rotulagem Azure Machine Learning.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: cd015e952f93655a7bdba8c947fac6e9a8f6546c
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518265"
---
# <a name="tag-images-in-a-labeling-project"></a>Identifie imagens em um projeto de rotulagem 

Depois de o administrador do seu projeto [criar um projeto de rotulagem](./how-to-create-labeling-projects.md#create-a-labeling-project) em Azure Machine Learning, pode utilizar a ferramenta de rotulagem (pré-visualização pública) para preparar rapidamente dados para um projeto de Machine Learning. Este artigo descreve:

> [!div class="checklist"]
> * Como aceder aos seus projetos de rotulagem
> * As ferramentas de rotulagem
> * Como utilizar as ferramentas para tarefas específicas de rotulagem

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta microsoft](https://account.microsoft.com/account) ou uma conta de Azure Ative Directory para a organização e projeto
* Acesso ao nível do contribuinte ao espaço de trabalho que contém o projeto de rotulagem.

## <a name="sign-in-to-the-workspace"></a>Inscreva-se no espaço de trabalho

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. Selecione a subscrição e o espaço de trabalho que contém o projeto de rotulagem.  Obtenha esta informação do seu administrador do projeto.

1. Selecione **data labeling** no lado esquerdo para encontrar o projeto.  

## <a name="understand-the-labeling-task"></a>Compreender a tarefa de rotulagem

Na tabela de projetos de rotulagem de dados, **selecione Label link** para o seu projeto.

Você vê instruções específicas para o seu projeto. Eles explicam o tipo de dados que está a enfrentar, como deve tomar as suas decisões, e outras informações relevantes. Depois de ler esta informação, no topo da página selecione **Tarefas**.  Ou na parte inferior da página, selecione **Iniciar a rotulagem**.

## <a name="common-features-of-the-labeling-task"></a>Características comuns da tarefa de rotulagem

Em todas as tarefas de rotulagem de imagem, você escolhe uma etiqueta ou etiquetas apropriadas de um conjunto especificado pelo administrador do projeto. Pode selecionar as primeiras nove tags utilizando as teclas numerais do seu teclado.  

Nas tarefas de classificação de imagem, pode optar por visualizar várias imagens simultaneamente. Utilize os ícones acima da área de imagem para selecionar o layout. 

Para selecionar simultaneamente todas as imagens apresentadas, utilize **Selecione todas**. Para selecionar imagens individuais, utilize o botão de seleção circular no canto superior direito da imagem. Deve selecionar pelo menos uma imagem para aplicar uma etiqueta. Se selecionar várias imagens, qualquer tag que selecione será aplicada a todas as imagens selecionadas.

Aqui escolhemos um layout de dois por dois e estamos prestes a aplicar a etiqueta "Mamífero" às imagens do urso e da orca. A imagem do tubarão já estava marcada como "Peixe Cartilaginoso", e a iguana ainda não foi marcada.

![Vários layouts de imagem e seleção](./media/how-to-label-images/layouts.png)

> [!Important] 
> Só mude os layouts quando tiver uma nova página de dados não rotulados. A troca de layouts limpa o trabalho de marcação em curso da página.

O Azure ativa o botão **Enviar** quando tiver marcado todas as imagens na página. **Selecione Enviar** para salvar o seu trabalho.

Depois de submeter tags para os dados em questão, o Azure atualiza a página com um novo conjunto de imagens da fila de trabalho.

### <a name="assisted-machine-learning-preview"></a>Aprendizagem assistida de máquinas (pré-visualização) 

> [!IMPORTANT]
> A aprendizagem de máquinas assistida está atualmente em visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Os algoritmos de aprendizagem automática podem ser desencadeados. Se estes algoritmos estiverem ativados no seu projeto, poderá ver o seguinte:

* Depois de alguma quantidade de imagens terem sido rotuladas, poderá ver **Tasks agrupadas** no topo do seu ecrã ao lado do nome do projeto.  Isto significa que as imagens são agrupadas para apresentar imagens semelhantes na mesma página.  Em caso afirmativo, mude para uma das múltiplas vistas de imagem para aproveitar o agrupamento.  

* Mais tarde, poderá ver **Tasks pré-etiquetadas** ao lado do nome do projeto.  As imagens aparecerão então com um rótulo sugerido que provém de um modelo de classificação de machine learning. Nenhum modelo de aprendizagem automática tem 100% de precisão. Embora utilizemos apenas imagens para as quais o modelo está confiante, estas imagens ainda podem estar incorretamente pré-etiquetadas.  Quando vir estas etiquetas, corrija as etiquetas erradas antes de enviar a página.  

* Para modelos de deteção de objetos, pode ver caixas e etiquetas já presentes.  Corrija qualquer que esteja incorreta antes de enviar a página.

Especialmente no início de um projeto de rotulagem, o modelo de machine learning só pode ser preciso o suficiente para pré-lobel um pequeno subconjunto de imagens. Uma vez rotuladas estas imagens, o projeto de rotulagem voltará à rotulagem manual para recolher mais dados para a próxima ronda de formação de modelos. Com o passar do tempo, o modelo ficará mais confiante sobre uma maior proporção de imagens, resultando em tarefas mais pré-escritas mais tarde no projeto.

## <a name="tag-images-for-multi-class-classification"></a>Etiquetar imagens para classificação de várias classes

Se o seu projeto for do tipo "Classificação de Imagem Multi-Class", atribuirá uma única etiqueta a toda a imagem. Para rever as instruções a qualquer momento, vá à página **Instruções** e selecione **Ver instruções detalhadas**.

Se perceber que cometeu um erro depois de atribuir uma etiqueta a uma imagem, pode consertá-la. Selecione o " **X** " na etiqueta que é exibida abaixo da imagem para limpar a etiqueta. Ou, selecione a imagem e escolha outra classe. O valor recentemente selecionado substituirá a etiqueta previamente aplicada.

## <a name="tag-images-for-multi-label-classification"></a>Etiquetar imagens para classificação de várias etiquetas

Se estiver a trabalhar num projeto do tipo "Image Classification Multi-Label", aplicará uma *ou mais* etiquetas a uma imagem. Para ver as instruções específicas do projeto, selecione **Instruções** e vá **ao Ver instruções detalhadas**.

Selecione a imagem que pretende rotular e, em seguida, selecione a etiqueta. A etiqueta é aplicada a todas as imagens selecionadas e, em seguida, as imagens são desmarcadas. Para aplicar mais etiquetas, tem de reseleler as imagens. A seguinte animação mostra a marcação multi-etiqueta:

1. **Selecione tudo** é usado para aplicar a etiqueta "Ocean".
1. Uma única imagem é selecionada e marcada como "Closeup".
1. Três imagens são selecionadas e marcadas como "Grande angular".

![Animação mostra fluxo multilabel](./media/how-to-label-images/multilabel.gif)

Para corrigir um erro, clique no " **X** " para limpar uma etiqueta individual ou selecionar as imagens e, em seguida, selecione a etiqueta, que limpa a etiqueta de todas as imagens selecionadas. Este cenário é mostrado aqui. Clicar em "Land" limpará a etiqueta das duas imagens selecionadas.

![Uma imagem mostra vários deselecções](./media/how-to-label-images/multiple-deselection.png)

O Azure só ativará o botão **Enviar** depois de ter aplicado pelo menos uma etiqueta em cada imagem. **Selecione Enviar** para salvar o seu trabalho.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Etiquetar imagens e especificar caixas delimitadoras para deteção de objetos

Se o seu projeto for do tipo "Identificação de Objetos (Caixas Delimitadoras)", especificará uma ou mais caixas de delimitação na imagem e aplicará uma etiqueta em cada caixa. As imagens podem ter várias caixas de delimitação, cada uma com uma única etiqueta. Utilize **instruções detalhadas** para determinar se são utilizadas várias caixas de delimitação no seu projeto.

1. Selecione uma etiqueta para a caixa de delimitação que pretende criar.
1. Selecione a ferramenta **caixa retangular** ![ ferramenta caixa retangular ](./media/how-to-label-images/rectangular-box-tool.png) ou selecione "R".
3. Clique e arraste diagonalmente através do seu alvo para criar uma caixa de delimitação áspera. Para ajustar a caixa de delimitação, arraste as bordas ou os cantos.

![Criação de caixa de limites](./media/how-to-label-images/bounding-box-sequence.png)

Para eliminar uma caixa de delimitação, clique no alvo em forma de X que aparece ao lado da caixa de delimitação após a criação.

Não pode mudar a etiqueta de uma caixa de limites existente. Se cometer um erro de atribuição de etiquetas, tem de apagar a caixa de delimitação e criar uma nova com a etiqueta correta.

Por predefinição, pode editar as caixas existentes. A ferramenta **lock/desbloqueie regiões** ![ Bloquear/desbloquear regiões ](./media/how-to-label-images/lock-bounding-boxes-tool.png) ferramenta ou "L" alterna esse comportamento. Se as regiões estiverem bloqueadas, só pode alterar a forma ou a localização de uma nova caixa de delimitação.

Utilize a ferramenta **de manipulação regiões** ![ Ferramenta de manipulação regiões ](./media/how-to-label-images/regions-tool.png) ou "M" para ajustar uma caixa de delimitação existente. Arraste as bordas ou os cantos para ajustar a forma. Clique no interior para poder arrastar toda a caixa de delimitação. Se não consegue editar uma região, provavelmente alterou a ferramenta **de regiões de bloqueio/desbloqueio.**

Utilize a ferramenta **de caixa baseada no modelo** ferramenta ferramenta ferramenta ![ modelo-box ](./media/how-to-label-images/template-box-tool.png) ou "T" para criar caixas de delimitação múltiplas do mesmo tamanho. Se a imagem não tiver caixas de delimitação e ativar caixas baseadas em modelos, a ferramenta produzirá caixas de 50 por 50 pixels. Se criar uma caixa de delimitação e, em seguida, ativar caixas baseadas em modelos, quaisquer novas caixas de delimitação serão do tamanho da última caixa que criou. As caixas baseadas em modelos podem ser redimensionadas após a colocação. Redimensionar uma caixa baseada em modelo só redimensiona essa caixa em particular.

Para eliminar *todas as* caixas de delimitação na imagem atual, selecione a ferramenta Eliminar todas **as regiões** ![ Eliminar regiões ](./media/how-to-label-images/delete-regions-tool.png) .

Depois de criar as caixas de delimitação para uma imagem, **selecione Submeter-se** para salvar o seu trabalho, ou o seu trabalho em curso não será guardado.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Marque imagens e especifique polígonos para segmentação de imagem 

Se o seu projeto for do tipo "Segmentação de Exemplo (Polygon)", especificará um ou mais polígonos na imagem e aplicará uma etiqueta a cada polígono. As imagens podem ter vários polígonos delimitadores, cada um com uma única etiqueta. Utilize **instruções detalhadas** para determinar se são utilizados vários polígonos de delimitação no seu projeto.

1. Selecione uma etiqueta para o polígono que pretende criar.
1. Selecione a ferramenta **da região do poligão Desenhe** ![ a ferramenta da região do polígono ](./media/how-to-label-images/polygon-tool.png) ou selecione "P".
3. Clique para cada ponto no polígono.  Quando tiver completado a forma, clique duas vezes para terminar.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Criar polígonos para Gato e Cão":::

Para eliminar um polígono, clique no alvo em forma de X que aparece ao lado do polígono após a criação.

Se pretender alterar a etiqueta para um polígono, selecione a ferramenta **Move region,** clique no polígono e selecione a etiqueta correta.

Pode editar os polígonos existentes. A ferramenta **lock/desbloqueie regiões** ![ Bloquear/desbloquear regiões ](./media/how-to-label-images/lock-bounding-boxes-tool.png) ferramenta ou "L" alterna esse comportamento. Se as regiões estiverem bloqueadas, só pode alterar a forma ou a localização de um novo polígono.

Utilize a ferramenta Adicionar ou remover pontos **de poligão** ![ Ferramenta de manipulação de regiões ](./media/how-to-label-images/add-remove-points-tool.png) ou "U" para ajustar um polígono existente. Clique no polígono para adicionar ou remover um ponto. Se não consegue editar uma região, provavelmente alterou a ferramenta **de regiões de bloqueio/desbloqueio.**

Para eliminar *todos os* polígonos na imagem atual, selecione a ferramenta Eliminar todas **as regiões** ![ Eliminar regiões ](./media/how-to-label-images/delete-regions-tool.png) .

Depois de criar os polígonos para uma imagem, **selecione Submeter-se** para salvar o seu trabalho, ou o seu trabalho em curso não será guardado.

## <a name="finish-up"></a>Conclusão

Quando envia uma página de dados marcados, o Azure atribui-lhe novos dados não identificados a partir de uma fila de trabalho. Se não houver mais dados não rotulados disponíveis, receberá uma mensagem a notar isto juntamente com um link para a página inicial do portal.

Quando terminar a rotulagem, selecione o seu nome no canto superior direito do portal de rotulagem e, em seguida, selecione **a sing-out**. Se não assinar, eventualmente, o Azure irá "cronometrar" e atribuir os seus dados a outro labeler.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [treinar modelos de classificação de imagem em Azure](./tutorial-train-models-with-aml.md)
