---
title: Marque imagens em um projeto de rotulagem
title.suffix: Azure Machine Learning
description: Aprenda a utilizar as ferramentas de marcação de dados num projeto de rotulagem de Machine Learning Azure.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6d0a9bf172039adcaa756660d38acc1547e91b49
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898687"
---
# <a name="tag-images-in-a-labeling-project"></a>Marque imagens em um projeto de rotulagem

Depois de o administrador do projeto criar um projeto de [rotulagem](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) no Azure Machine Learning, pode utilizar a ferramenta de rotulagem para preparar rapidamente dados para um projeto de Machine Learning. Este artigo descreve:

> [!div class="checklist"]
> * Como aceder aos seus projetos de rotulagem
> * As ferramentas de rotulagem
> * Como utilizar as ferramentas para tarefas específicas de rotulagem

## <a name="prerequisites"></a>Pré-requisitos

* O URL do portal de rotulagem para um projeto de rotulagem de dados em execução
* Uma [conta Microsoft](https://account.microsoft.com/account) ou uma conta Azure Ative Diretório para a organização e projeto

> [!NOTE]
> O administrador do projeto pode encontrar o URL do portal de rotulagem no separador **Detalhes** da página de detalhes do **Projeto.**

## <a name="sign-in-to-the-projects-labeling-portal"></a>Inscreva-se no portal de rotulagem do projeto

Vá ao URL do portal de rotulagem que é fornecido pelo administrador do projeto. Faça o insto utilizando a conta de e-mail que o administrador usou para o adicionar à equipa. Para a maioria dos utilizadores, será a sua conta Microsoft. Se o projeto de rotulagem usar o Diretório Ativo Azure, é assim que vai sintetizar.

## <a name="understand-the-labeling-task"></a>Compreender a tarefa de rotulagem

Depois de iniciar sessão, verá a página geral do projeto.

Vá ver **instruções detalhadas**. Estas instruções são específicas do seu projeto. Eles explicam o tipo de dados que está a enfrentar, como deve tomar as suas decisões, e outras informações relevantes. Depois de ler esta informação, volte à página do projeto e selecione **Iniciar a rotulagem**.

## <a name="common-features-of-the-labeling-task"></a>Características comuns da tarefa de rotulagem

Em todas as tarefas de rotulagem de imagem, você escolhe uma etiqueta ou etiquetas apropriadas de um conjunto especificado pelo administrador do projeto. Pode selecionar as primeiras nove etiquetas utilizando as teclas de número no teclado.  

Em tarefas de classificação de imagem, pode optar por ver várias imagens simultaneamente. Utilize os ícones acima da área de imagem para selecionar o layout. Para selecionar todas as imagens exibidas simultaneamente, utilize **Selecione todas**. Para selecionar imagens individuais, utilize o botão de seleção circular no canto superior direito da imagem. Deve selecionar pelo menos uma imagem para aplicar uma etiqueta. Se selecionar várias imagens, qualquer etiqueta que selecionar será aplicada a todas as imagens selecionadas.

Aqui escolhemos um layout de dois por dois e estamos prestes a aplicar a etiqueta "Mamífero" às imagens do urso e da orca. A imagem do tubarão já estava marcada como "Peixe Cartilaginoso", e a iguana ainda não foi marcada.

![Vários layouts de imagem e seleção](./media/how-to-label-images/layouts.png)

> [!Important] 
> Só altere os layouts quando tiver uma página nova de dados não rotulados. Mudar os layouts iliba o trabalho de marcação em curso da página.

O Azure ativa o botão **Enviar** quando tiver marcado todas as imagens da página. Selecione **Submeter** para salvar o seu trabalho.

Depois de submeter etiquetas para os dados disponíveis, o Azure atualiza a página com um novo conjunto de imagens da fila de trabalho.

### <a name="assisted-machine-learning"></a>Aprendizagem automática assistida 

Os algoritmos de aprendizagem automática podem ser desencadeados durante uma tarefa de classificação multi-classe ou multi-etiqueta. Se estes algoritmos estiverem ativados no seu projeto, poderá ver o seguinte:

* Depois de alguma quantidade de imagens terem sido rotulados, poderá ver **tarefas agrupadas** na parte superior do ecrã ao lado do nome do projeto.  Isto significa que as imagens são agrunadas para apresentar imagens semelhantes na mesma página.  Em caso afirmativo, mude para uma das múltiplas vistas de imagem para tirar partido do agrupamento.  

* Num ponto posterior, poderá ver **tarefas pré-rotuladas** ao lado do nome do projeto.  As imagens aparecerão então com um rótulo sugerido que provém de um modelo de classificação de machine learning. Nenhum modelo de aprendizagem automática tem 100% de precisão. Embora utilizemos apenas imagens para as quais o modelo está confiante, estas imagens podem ainda ser incorretamente pré-rotuladas.  Quando vir estas etiquetas, corrija as etiquetas erradas antes de enviar a página.  

Especialmente no início de um projeto de rotulagem, o modelo de aprendizagem automática só pode ser preciso o suficiente para pré-rotular um pequeno subconjunto de imagens. Uma vez rotulados estas imagens, o projeto de rotulagem voltará à rotulagem manual para recolher mais dados para a próxima ronda de formação de modelos. Com o tempo, o modelo tornar-se-á mais confiante sobre uma maior proporção de imagens, resultando em tarefas mais pré-rotuladas mais tarde no projeto.

## <a name="tag-images-for-multi-class-classification"></a>Marque imagens para classificação multi-classe

Se o seu projeto for de tipo "Classificação de Imagem Multiclasse", atribuirá uma única etiqueta a toda a imagem. Para rever as instruções a qualquer momento, vá à página **instruções** e selecione **Ver instruções detalhadas**.

Se perceber que cometeu um erro depois de atribuir uma etiqueta a uma imagem, pode consertá-la. Selecione o "**X**" na etiqueta que é exibida abaixo da imagem para limpar a etiqueta. Ou, selecione a imagem e escolha outra classe. O valor recentemente selecionado substituirá a etiqueta anteriormente aplicada.

## <a name="tag-images-for-multi-label-classification"></a>Marque imagens para classificação multi-etiqueta

Se estiver a trabalhar num projeto de tipo "Image Classification Multi-Label", aplicará uma *ou mais* etiquetas a uma imagem. Para ver as instruções específicas do projeto, selecione **Instruções** e vá ver **instruções detalhadas**.

Selecione a imagem que pretende rotular e, em seguida, selecione a etiqueta. A etiqueta é aplicada a todas as imagens selecionadas e, em seguida, as imagens são desselecionadas. Para aplicar mais tags, deve voltar a selecionar as imagens. A seguinte animação mostra marcação multi-etiqueta:

1. **Selecione tudo** é usado para aplicar a etiqueta "Ocean".
1. Uma única imagem é selecionada e marcada como "Closeup".
1. Três imagens são selecionadas e marcadas como "Grande angular".

![Animação mostra fluxo multirótulo](./media/how-to-label-images/multilabel.gif)

Para corrigir um erro, clique no "**X**" para limpar uma etiqueta individual ou selecionar as imagens e, em seguida, selecionar a etiqueta, que limpa a etiqueta de todas as imagens selecionadas. Este cenário é mostrado aqui. Clicar em "Land" limpará essa etiqueta a partir das duas imagens selecionadas.

![Uma imagem mostra várias desselecções](./media/how-to-label-images/multiple-deselection.png)

O Azure só ativará o botão **Enviar** depois de ter aplicado pelo menos uma etiqueta em cada imagem. Selecione **Submeter** para salvar o seu trabalho.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Marque imagens e especifique caixas de delimitação para deteção de objetos

Se o seu projeto for do tipo "Identificação de Objetos (Caixas de delimitação)", especifique uma ou mais caixas de delimitação na imagem e aplique uma etiqueta em cada caixa. As imagens podem ter várias caixas de delimitação, cada uma com uma única etiqueta. Utilização **Ver instruções detalhadas** para determinar se várias caixas de delimitação são usadas no seu projeto.

1. Selecione uma etiqueta para a caixa de delimitação que pretende criar.
1. Selecione a ferramenta de **caixa retangular** ![ferramenta de caixa retangular](./media/how-to-label-images/rectangular-box-tool.png) ou selecione "R".
3. Clique e arraste na diagonal através do seu alvo para criar uma caixa de delimitação áspera. Para ajustar a caixa de delimitação, arraste as bordas ou cantos.

![Uma imagem mostra a criação básica de caixa de limitador.](./media/how-to-label-images/bounding-box-sequence.png)

Para eliminar uma caixa de delimitação, clique no alvo em forma de X que aparece ao lado da caixa de delimitação após a criação.

Não pode supor a etiqueta de uma caixa de delimitação existente. Se cometer um erro de atribuição de etiquetas, tem de apagar a caixa de delimitação e criar uma nova com a etiqueta correta.

Por padrão, pode editar as caixas de delimitação existentes. A ferramenta **lock/unlock regions** ![lock/unlock regions ferramenta](./media/how-to-label-images/lock-bounding-boxes-tool.png) ou "L" alterna esse comportamento. Se as regiões estiverem bloqueadas, só pode alterar a forma ou a localização de uma nova caixa de delimitação.

Utilize a ferramenta de manipulação das **regiões** ![ferramenta de manipulação das regiões](./media/how-to-label-images/regions-tool.png) ou "M" para ajustar uma caixa de delimitação existente. Arraste as bordas ou cantos para ajustar a forma. Clique no interior para poder arrastar toda a caixa de limitadora. Se não consegue editar uma região, provavelmente alterou a ferramenta **de regiões Lock/unlock.**

Utilize a ferramenta de **caixa baseada em modelo![** ferramenta de caixa de modelo](./media/how-to-label-images/template-box-tool.png) ou "T" para criar várias caixas de delimitação do mesmo tamanho. Se a imagem não tiver caixas de delimitação e ativar caixas baseadas em modelos, a ferramenta produzirá caixas de 50 por 50 pixels. Se criar uma caixa de delimitação e, em seguida, ativar caixas baseadas em modelos, quaisquer novas caixas de delimitação serão do tamanho da última caixa que criou. As caixas baseadas em modelos podem ser redimensionadas após a colocação. Redimensionar uma caixa baseada em modelo somente apenas redimensiona essa caixa em particular.

Para eliminar *todas as* caixas de delimitação na imagem atual, selecione a ferramenta Eliminar todas **as regiões** ![Eliminar as regiões](./media/how-to-label-images/delete-regions-tool.png).

Depois de criar as caixas de delimitação para uma imagem, selecione **Enviar** para salvar o seu trabalho, ou o seu trabalho em curso não será guardado.

## <a name="finish-up"></a>Termine

Ao submeter uma página de dados marcados, o Azure atribui-lhe novos dados não rotulados a partir de uma fila de trabalho. Se não houver mais dados não rotulados disponíveis, receberá uma mensagem a notar isso juntamente com um link para a página inicial do portal.

Quando terminar a rotulagem, selecione o seu nome no canto superior direito do portal de rotulagem e, em seguida, selecione o **sign-out**. Se não assinar, eventualmente o Azure irá "cronometrar- te" e atribuir os seus dados a outro rotulador.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a treinar modelos de classificação de [imagem em Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)
* Leia sobre deteção de [objetos usando Azure e a técnica "R-CNN mais rápida"](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)
