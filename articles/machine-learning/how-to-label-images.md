---
title: Marcar imagens em um projeto de rotulagem
title.suffix: Azure Machine Learning
description: Saiba como usar as ferramentas de marcação de dados em um projeto de rotulagem de Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 1e27fca86613757c36ac664e2e449cabed68d550
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772453"
---
# <a name="tag-images-in-a-labeling-project"></a>Marcar imagens em um projeto de rotulagem

Depois que o administrador do projeto [criar um projeto de rotulagem](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project) no Azure Machine Learning, você poderá usar a ferramenta de rotulagem para preparar rapidamente os dados para um projeto Machine Learning. Este artigo descreve:

> [!div class="checklist"]
> * Como acessar seus projetos de rotulagem
> * As ferramentas de rotulagem
> * Como usar as ferramentas para tarefas de rotulamento específicas

## <a name="prerequisites"></a>Pré-requisitos

* A URL do portal de rotulagem para um projeto de rotulagem de dados em execução
* Uma [conta Microsoft](https://account.microsoft.com/account) ou uma conta de Azure Active Directory para a organização e o projeto

> [!NOTE]
> O administrador do projeto pode encontrar a URL do portal de rotulagem na guia **detalhes** da página de **detalhes do projeto** .

## <a name="sign-in-to-the-projects-labeling-portal"></a>Entre no portal de rotulagem do projeto

Acesse a URL do portal de rotulagem que é fornecida pelo administrador do projeto. Entre usando a conta de email que o administrador usou para adicioná-lo à equipe. Para a maioria dos usuários, será sua conta Microsoft. Se o projeto de rotulagem usar Azure Active Directory, será assim que você entrará.

## <a name="understand-the-labeling-task"></a>Entender a tarefa de rotulagem

Depois de entrar, você verá a página Visão geral do projeto.

Vá para **exibir instruções detalhadas**. Essas instruções são específicas para seu projeto. Eles explicam o tipo de dados que você está enfrentando, como você deve tomar suas decisões e outras informações relevantes. Depois de ler essas informações, retorne à página do projeto e selecione **Iniciar rotulagem**.

## <a name="common-features-of-the-labeling-task"></a>Recursos comuns da tarefa de rotulagem

Em todas as tarefas de rotulagem de imagem, você escolhe uma marca ou marcas apropriadas de um conjunto que é especificado pelo administrador do projeto. Você pode selecionar as nove primeiras marcas usando as teclas de número no teclado.  

Em tarefas de classificação de imagem, você pode optar por exibir várias imagens simultaneamente. Use os ícones acima da área da imagem para selecionar o layout. Para selecionar todas as imagens exibidas simultaneamente, use **selecionar tudo**. Para selecionar imagens individuais, use o botão de seleção circular no canto superior direito da imagem. Você deve selecionar pelo menos uma imagem para aplicar uma marca. Se você selecionar várias imagens, qualquer marca selecionada será aplicada a todas as imagens selecionadas.

Aqui, escolhemos um layout de dois por dois e estamos prestes a aplicar a marca "mamífero" às imagens do urso e da orca. A imagem do Shark já estava marcada como "peixe Cartilaginous", e o iguana ainda não foi marcado.

![Vários layouts de imagens e seleção](./media/how-to-label-images/layouts.png)

> [!Important] 
> Alterne apenas os layouts quando você tiver uma página nova de dados sem rótulo. A alternância de layouts limpa o trabalho de marcação em andamento da página.

O Azure habilita o botão **Enviar** quando você marcou todas as imagens na página. Selecione **Enviar** para salvar seu trabalho.

Depois de enviar marcas para os dados em mãos, o Azure atualiza a página com um novo conjunto de imagens da fila de trabalho.

## <a name="tag-images-for-multi-class-classification"></a>Marcar imagens para classificação de várias classes

Se o seu projeto for do tipo "classificação de imagem multiclasse", você atribuirá uma única marca à imagem inteira. Para examinar as direções a qualquer momento, vá para a página **instruções** e selecione **exibir instruções detalhadas**.

Se você perceber que cometeu um erro depois de atribuir uma marca a uma imagem, você pode corrigi-la. Selecione o "**X**" no rótulo que é exibido abaixo da imagem para limpar a marca. Ou então, selecione a imagem e escolha outra classe. O valor selecionado recentemente substituirá a marca aplicada anteriormente.

## <a name="tag-images-for-multi-label-classification"></a>Imagens de marcas para classificação de vários rótulos

Se você estiver trabalhando em um projeto do tipo "múltiplo rótulo de classificação de imagem", aplicará uma *ou mais* marcas a uma imagem. Para ver as direções específicas do projeto, selecione **instruções** e vá para **exibir instruções detalhadas**.

Selecione a imagem que você deseja rotular e, em seguida, selecione a marca. A marca é aplicada a todas as imagens selecionadas e, em seguida, as imagens são desmarcadas. Para aplicar mais marcas, você deve selecionar novamente as imagens. A animação a seguir mostra a marcação de vários rótulos:

1. **Selecionar tudo** é usado para aplicar a marca "oceano".
1. Uma única imagem é selecionada e marcada "closeup".
1. Três imagens são selecionadas e marcadas "amplas de ângulo".

![Animação mostra o fluxo de multirótulo](./media/how-to-label-images/multilabel.gif)

Para corrigir um erro, clique no "**X**" para limpar uma marca individual ou selecione as imagens e, em seguida, selecione a marca, que limpa a marca de todas as imagens selecionadas. Esse cenário é mostrado aqui. Clicar em "Land" limpará essa marca das duas imagens selecionadas.

![Uma captura de tela mostra várias desmarcações](./media/how-to-label-images/multiple-deselection.png)

O Azure só habilitará o botão **Enviar** depois que você aplicar pelo menos uma marca a cada imagem. Selecione **Enviar** para salvar seu trabalho.

## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Marcar imagens e especificar caixas delimitadoras para detecção de objetos

Se o seu projeto for do tipo "identificação de objeto (caixas delimitadas)", você especificará uma ou mais caixas delimitadores na imagem e aplicará uma marca a cada caixa. As imagens podem ter várias caixas delimitadas, cada uma com uma única marca. Use **instruções de exibição detalhadas** para determinar se várias caixas delimitadas são usadas em seu projeto.

1. Selecione uma marca para a caixa delimitadora que você planeja criar.
1. Selecione a ferramenta de **caixa retangular** ![ferramenta de caixa retangular](./media/how-to-label-images/rectangular-box-tool.png) ou selecione "R".
3. Clique e arraste diagonalmente em seu destino para criar uma caixa delimitadora aproximada. Para ajustar a caixa delimitadora, arraste as bordas ou os cantos.

![Uma captura de tela mostra A criação básica da caixa delimitadora.](./media/how-to-label-images/bounding-box-sequence.png)

Para excluir uma caixa delimitadora, clique no destino em formato X que aparece ao lado da caixa delimitadora após a criação.

Não é possível alterar a marca de uma caixa delimitadora existente. Se você cometer um erro de atribuição de marca, precisará excluir a caixa delimitadora e criar uma nova com a marca correta.

Por padrão, você pode editar caixas delimitadas existentes. A ferramenta **bloquear/desbloquear regiões** ![ferramenta bloquear/desbloquear regiões](./media/how-to-label-images/lock-bounding-boxes-tool.png) ou "L" alterna esse comportamento. Se as regiões estiverem bloqueadas, você só poderá alterar a forma ou o local de uma nova caixa delimitadora.

Use a ferramenta de **manipulação de regiões** ![ferramenta de manipulação de regiões](./media/how-to-label-images/regions-tool.png) ou "M" para ajustar uma caixa delimitadora existente. Arraste as bordas ou os cantos para ajustar a forma. Clique no interior para poder arrastar a caixa delimitadora inteira. Se não for possível editar uma região, você provavelmente terá alternado a ferramenta **bloquear/desbloquear regiões** .

Use a ferramenta de **caixa baseada em modelo** ![ferramenta de caixa de modelo](./media/how-to-label-images/template-box-tool.png) ou "t" para criar várias caixas delimitadas do mesmo tamanho. Se a imagem não tiver caixas delimitadoras e você ativar as caixas baseadas em modelo, a ferramenta produzirá caixas de 50 a 50 pixels. Se você criar uma caixa delimitadora e, em seguida, ativar as caixas baseadas em modelo, todas as novas caixas delimitadas serão o tamanho da última caixa que você criou. As caixas baseadas em modelo podem ser redimensionadas após o posicionamento. O redimensionamento de uma caixa baseada em modelo redimensiona apenas essa caixa específica.

Para excluir *todas as* caixas delimitadas na imagem atual, selecione a ferramenta **excluir todas as regiões** ![ferramenta excluir regiões](./media/how-to-label-images/delete-regions-tool.png).

Depois de criar as caixas delimitadores para uma imagem, selecione **Enviar** para salvar seu trabalho ou seu trabalho em andamento não será salvo.

## <a name="finish-up"></a>Concluir

Quando você envia uma página de dados marcados, o Azure atribui novos dados sem rótulo a você de uma fila de trabalho. Se não houver mais dados sem rótulo disponíveis, você receberá uma mensagem indicando isso junto com um link para o portal home page.

Quando você terminar de rotular, selecione seu nome no canto superior direito do portal de rotulagem e, em seguida, selecione **sair**. Se você não sair, eventualmente o Azure irá "cronometrar você" e atribuir seus dados a outro Labeler.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [treinar modelos de classificação de imagem no Azure](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)
* Leia sobre [a detecção de objetos usando o Azure e a técnica "R-CNN mais rápida"](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)
