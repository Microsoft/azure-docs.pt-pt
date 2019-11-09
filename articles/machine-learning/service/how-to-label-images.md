---
title: Como usar a ferramenta de rotulação de dados do Azure Machine Learning
title.suffix: Azure Machine Learning
description: Este artigo ensina como usar as ferramentas de marcação de dados em um projeto de rotulagem de Azure Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 9cbebb905ba39dabaf905be2fa741702bd1a0088
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838945"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>Como marcar imagens em um projeto de rotulagem

Depois que o administrador do projeto tiver criado um projeto de rotulagem no Azure Machine Learning, você poderá usar a ferramenta de rotulagem para preparar rapidamente os dados para um projeto de Machine Learning. 

> [!div class="checklist"]
> * Como acessar seus projetos de rotulagem
> * Ferramentas de rotulagem
> * Como usar as ferramentas para tarefas de rotulamento específicas

## <a name="prerequisites"></a>Pré-requisitos

* A URL do portal de rotulagem para um projeto de rotulagem de dados em execução
* Uma [conta da Microsoft](https://account.microsoft.com/account) ou
* Uma conta de Azure Active Directory para a organização e o projeto

> [!Note]
> O administrador do projeto pode encontrar a URL do portal de rotulagem na guia **detalhes** na página de **detalhes do projeto** . 

## <a name="logging-in-to-the-projects-labeling-portal"></a>Fazendo logon no portal de rotulagem do projeto

Acesse a URL do portal de rotulagem fornecida a você pelo administrador do projeto. 

Faça logon usando a conta de email que o administrador usou para adicioná-lo à equipe. Para a maioria dos usuários, o logon será feito com seu conta Microsoft. Se o projeto de rotulagem usar Azure Active Directory, será assim que você fará logon. 

## <a name="understanding-the-labeling-task"></a>Compreendendo a tarefa de rotulagem

Depois de entrar, você será levado para a página de visão geral do projeto. 

A primeira coisa a fazer é **exibir instruções detalhadas**. Essas instruções são específicas para seu projeto e explicarão o tipo de dados que você está enfrentando, como você deve tomar suas decisões e outras informações relevantes. Quando terminar, retorne à página do projeto e escolha **Iniciar rotulagem**.

## <a name="common-features-of-the-labeling-task"></a>Recursos comuns da tarefa de rotulagem

Todas as tarefas de rotulagem de imagem envolvem a escolha de uma marca ou marcas apropriadas de um conjunto especificado pelo administrador do projeto. Você pode selecionar entre as nove primeiras marcas usando as teclas de número no teclado.  

As tarefas de classificação de imagem permitem que você opte por apresentar várias imagens simultaneamente. Os layouts diferentes podem ser escolhidos usando os ícones acima da área da imagem. Você pode selecionar todas as imagens exibidas simultaneamente pressionando o botão **selecionar tudo** . Selecione fotos individuais usando o botão de seleção circular no canto superior direito da área da imagem. Você deve selecionar pelo menos uma imagem para aplicar uma marca. Se você tiver várias imagens selecionadas, a seleção de uma marca aplicará essa marca a cada uma das fotos selecionadas.

Aqui, escolhemos um layout 2x2 e estamos prestes a aplicar a marca "mamífero" às imagens do urso e da orca. A imagem com o Shark já foi marcada como "peixe Cartilaginous" e o iguana ainda não foi marcado.

![Vários layouts de imagens e seleção](media/how-to-label-images/layouts.png)

> [!Important] 
> Alterne apenas os layouts quando você tiver uma página nova de dados sem rótulo. A alternância de layouts limpa o trabalho de marcação em andamento da página. 

O Azure habilita o botão **Enviar** quando você marcou todas as imagens na página. Pressione **Enviar** para salvar seu trabalho. 

Depois de enviar marcas para os dados em mãos, o Azure atualizará a página com um novo conjunto de imagens da fila de trabalho.  

## <a name="tagging-images-for-multi-class-classification"></a>Marcando imagens para classificação de várias classes

Se o seu projeto for do tipo "classificação de imagem multiclasse", você atribuirá uma única marca à imagem inteira. A qualquer momento, escolha a página **instruções** e navegue para **exibir instruções detalhadas** para ver as diretrizes específicas do projeto. 

Conforme discutido anteriormente, você pode selecionar entre vários layouts para apresentar imagens. Se, depois de selecionar uma imagem e atribuir uma marca a ela, você perceberá que cometeu um erro, poderá corrigi-la. Se, no rótulo exibido abaixo da imagem, você clicar no destino `X`, limpará a marca. Ou, se você selecionar a imagem e escolher outra classe, a marca será alternada para o valor selecionado recentemente.

## <a name="tagging-images-for-multi-label-classification"></a>Marcando imagens para classificação de vários rótulos

Se você estiver trabalhando em um projeto do tipo "vários rótulos de classificação de imagem", você aplicará uma _ou mais_ marcas a uma imagem. A qualquer momento, escolha a página **instruções** e navegue para **exibir instruções detalhadas** para ver as diretrizes específicas do projeto. 

Selecione a imagem que você deseja rotular e, em seguida, clique na marca. Escolher a marca aplica-se a todas as imagens selecionadas e as seleciona. Para aplicar mais marcas, você deve selecionar novamente as imagens. Esta animação mostra uma página de marcação de vários rótulos:

* **Selecionar tudo** é usado para aplicar a marca "oceano"
* Uma única imagem é selecionada e marcada "closeup"
* Três imagens são selecionadas e marcadas "larga Angle"

![Animação mostrando o fluxo multirótulo](media/how-to-label-images/multilabel.gif)

Para corrigir um erro, você pode clicar no `X` para limpar marcas individuais ou selecionar as imagens e escolher a marca, o que limpará a marca de todas as imagens selecionadas. Esse cenário é mostrado aqui, onde clicar em "Land" limpará essa marca das duas imagens selecionadas.

![Captura de tela mostrando várias desmarcações](media/how-to-label-images/multiple-deselection.png)

O Azure só habilitará o botão **Enviar** depois que você aplicar pelo menos uma marca a cada imagem. Pressione **Enviar** para que seu trabalho seja salvo.

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>Marcando imagens e caixas delimitadoras para detecção de objetos

Se o seu projeto for do tipo "identificação de objeto (caixas delimitadas)", você especificará uma ou mais caixas delimitadores na imagem e aplicará uma marca a essa caixa. Cada imagem pode ter várias caixas delimitadoras, cada uma com uma única marca. Use **instruções de exibição detalhadas** para determinar se a adição de várias caixas delimitadas é apropriada ao seu projeto.

1. Selecione uma marca para a caixa delimitadora que você deseja criar
1. Selecione a ferramenta de **caixa retangular** ![ferramenta de caixa retangular](media/how-to-label-images/rectangular-box-tool.png) ou pressione ' R ' 
1. Clique e arraste diagonalmente em seu destino para criar uma caixa delimitadora aproximada
    * Ajuste a caixa delimitadora clicando e arrastando as bordas ou os cantos da caixa

![Captura de tela da criação básica da caixa delimitadora](media/how-to-label-images/bounding-box-sequence.png)

Se você quiser excluir a caixa delimitadora, clique no destino em formato X que aparece ao lado da caixa delimitadora após a criação.

Você não pode reatribuir a marca de uma caixa delimitadora existente. Se você cometer um erro com a atribuição de marca, deverá excluir a caixa delimitadora e criar uma nova com a marca correta.

Por padrão, as caixas delimitadas existentes podem ser editadas. A ferramenta **bloquear/desbloquear regiões** ![ferramenta bloquear/desbloquear regiões](media/how-to-label-images/lock-bounding-boxes-tool.png) ou "L" alterna esse comportamento. Se as regiões estiverem bloqueadas, você só poderá alterar a forma ou o local de uma nova caixa delimitadora.

Use a ferramenta de **manipulação de regiões** ![ferramenta de manipulação de regiões](media/how-to-label-images/regions-tool.png) ou "M" para ajustar uma caixa delimitadora existente. Você pode clicar e arrastar as bordas ou os cantos para ajustar a forma. Se você clicar no interior, poderá arrastar a caixa delimitadora inteira. Se não for possível editar uma região, você provavelmente terá alternado a ferramenta **bloquear/desbloquear regiões** . 

Use a ferramenta de **caixa baseada em modelo** ![ferramenta de caixa de modelo](media/how-to-label-images/template-box-tool.png) ou "t" para criar várias caixas delimitadas do mesmo tamanho. Se a imagem não tiver caixas delimitadoras e você ativar as caixas baseadas em modelo, a ferramenta produzirá caixas de pixels 50x50s. Se você tiver criado uma caixa delimitadora e, em seguida, ativar caixas baseadas em modelo, novas caixas delimitadas serão o tamanho do último que você fez. As caixas baseadas em modelo podem ser redimensionadas após o posicionamento. O redimensionamento de uma caixa baseada em modelo redimensiona apenas a caixa específica. 

Se você quiser excluir _todas as_ caixas delimitadas na imagem atual, poderá escolher a ferramenta **excluir todas as regiões** ![ferramenta excluir regiões](media/how-to-label-images/delete-regions-tool.png). 

Depois de criar as caixas delimitadoras para a imagem, pressione **Enviar** para salvar seu trabalho. Seu trabalho em andamento não será salvo, a menos que você pressione o botão **Enviar** . 

## <a name="finishing-up"></a>Concluindo 

Quando você envia uma página de dados marcados, o Azure atribui novos dados não rotulados de uma fila de trabalho. Se não houver mais dados sem rótulo, você verá uma mensagem para esse efeito, com um link de volta para o portal home page. 

Se você souber que não fará mais rotulagem, escolha seu nome no canto superior direito do portal de rotulagem e escolha **sair**. Se você não sair, eventualmente o Azure irá "cronometrar você" e atribuir seus dados a outro Labeler. 

## <a name="next-steps"></a>Passos Seguintes

* Aprenda a [treinar modelos de classificação de imagem no Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* Leia sobre [a detecção de objetos usando o Azure e a técnica R-CNN mais rápida](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)