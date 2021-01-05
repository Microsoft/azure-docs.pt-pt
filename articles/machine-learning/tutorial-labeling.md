---
title: 'Tutorial: Criar um projeto de rotulagem para a classificação de imagem'
titleSuffix: Azure Machine Learning
description: Aprenda a gerir o processo de rotulagem de imagens para que possam ser usadas em modelos de classificação de imagem multi-classes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 238801171d19191bcac505f0c95395320f96d75c
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882471"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Tutorial: Criar um projeto de rotulagem para classificação de imagem multi-classes 


Este tutorial mostra-lhe como gerir o processo de rotulagem (também referido como marcação) para ser usado como dados para construir modelos de machine learning. A rotulagem de dados em Azure Machine Learning está em pré-visualização pública.

Se quiser treinar um modelo de machine learning para classificar imagens, precisa de centenas ou mesmo milhares de imagens que estão corretamente rotuladas.  O Azure Machine Learning ajuda-o a gerir o progresso da sua equipa privada de especialistas em domínios à medida que rotulam os seus dados.
 
Neste tutorial, você usará imagens de gatos e cães.  Uma vez que cada imagem é um gato ou um cão, este é um projeto de rotulagem *multi-classe.* Vai aprender a:

> [!div class="checklist"]
>
> * Crie uma conta de armazenamento Azure e faça upload de imagens para a conta.
> * Criar um espaço de trabalho de aprendizagem automática Azure.
> * Crie um projeto de rotulagem de imagem multi-classe.
> * Marque os seus dados.  Ou você ou os seus rotuladores podem executar esta tarefa.
> * Complete o projeto revendo e exportando os dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de machine learning. Liga a sua subscrição e grupo de recursos Azure a um objeto facilmente consumido no serviço.

Existem muitas [formas de criar um espaço de trabalho.](how-to-manage-workspace.md) Neste tutorial, cria-se um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Inicie um projeto de rotulagem

Em seguida, você vai gerir o projeto de rotulagem de dados no atelier Azure Machine Learning, uma interface consolidada que inclui ferramentas de machine learning para executar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade. O estúdio não é suportado nos navegadores do Internet Explorer.

1. Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com).

1. Selecione a sua subscrição e o espaço de trabalho que criou.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Criar uma loja de dados

As lojas de dados Azure Machine Learning são usadas para armazenar informações de conexão, como o seu ID de subscrição e a autorização simbólica. Aqui utiliza uma loja de dados para ligar à conta de armazenamento que contém as imagens para este tutorial.

1. No lado esquerdo do seu espaço de trabalho, selecione **Datastores**.

1. Selecione **+ Nova loja de dados**.

1. Preencha o formulário com estas definições:

    Campo|Descrição 
    ---|---
    Nome da datastore | Dê um nome à loja de dados.  Aqui **usamos labeling_tutorial.**
    Tipo de datastore | Selecione o tipo de armazenamento.  Aqui utilizamos **o Azure Blob Storage,** o armazenamento preferido para imagens.
    Método de seleção de conta | Selecione **Introduza manualmente**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Tipo de autenticação | Selecione **o token SAS**.
    Chave da conta | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Selecione **Criar** para criar a datastore.

### <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Agora que tem acesso aos dados que pretende ter rotulado, crie o seu projeto de rotulagem.

1. No topo da página, selecione **Projetos.**

1. Selecione **+ Adicionar projeto.**

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Criar um projeto":::

### <a name="project-details"></a>Detalhes do projeto

1. Utilize a seguinte entrada para o formulário de detalhes do **Projeto:**

    Campo|Descrição 
    ---|---
    Nome do projeto | Dê um nome ao seu projeto.  Aqui usaremos **tutorial-gatos-n-dogs.**
    Tipo de tarefa de rotulagem | Selecione **Classificação de Imagem Multi-classe**.  
    
    Selecione **Next** para continuar a criar o projeto.

### <a name="select-or-create-a-dataset"></a>Selecione ou crie um conjunto de dados

1.   No formulário Selecionar ou criar um formulário **conjunto de dados,** selecionar a segunda escolha, **criar um conjunto de dados** e, em seguida, selecionar o link **A partir da datastore**.

1. Utilize a seguinte entrada para o conjunto de dados Criar a partir do formulário **datastore:**

    1. No formulário **de informação básica,** adicione um nome, aqui usaremos **imagens-para-tutorial.**  Adicione uma descrição, se desejar.  Em seguida, selecione **Seguinte**.
    1. No formulário **de seleção datastore,** selecione **datastore previamente criado,** clique no nome da datastore e selecione **Select datastore**.
    1. Na página seguinte, verifique se a datastore atualmente selecionada está correta. Caso contrário, selecione **Datastore previamente criado** e repita o passo anterior.
    1. Em seguida, ainda no formulário **de seleção datastore,** selecione **Browse** e, em seguida, selecione **MultiClass - DogsCats**.  **Selecione Guardar** para usar **/MultiClass - DogsCats** como o caminho.
    1. Selecione **Next** para confirmar detalhes e, em seguida, **criar** para criar o conjunto de dados.
    1. Selecione o círculo ao lado do nome do conjunto de dados na lista, por exemplo **imagens-para-tutorial**.

1. Selecione **Next** para continuar a criar o projeto.

### <a name="incremental-refresh"></a>Atualização incremental

Se pretender adicionar novas imagens ao seu conjunto de dados, a atualização incremental encontrará estas novas imagens e adicioná-las-á ao seu projeto.  Quando ativar esta funcionalidade, o projeto verificará periodicamente novas imagens.  Não vai adicionar novas imagens à datastore para este tutorial, por isso deixe esta funcionalidade desmarcada.

Selecione **Seguinte** para continuar.

### <a name="label-classes"></a>Aulas de etiquetas

1. No formulário **de classe Label,** digite um nome de etiqueta e, em seguida, selecione **+Adicione a etiqueta** para escrever a etiqueta seguinte.  Para este projeto, os rótulos são **Cat,** **Dog,** e **Uncertain.**

1. Selecione **Next** quando tiver adicionado todas as etiquetas.

### <a name="labeling-instructions"></a>Instruções de rotulagem

1. No formulário **de instruções de rotulagem,** pode fornecer um link para um website que fornece instruções detalhadas para os seus rotuladores.  Vamos deixá-lo em branco para este tutorial.

1. Também pode adicionar uma breve descrição da tarefa diretamente no formulário.  Tipo **Tutorial de Rotulagem - Gatos & Cães.**

1. Selecione **Seguinte**.

1. Na secção **de rotulagem assistida ML,** deixe a caixa de verificação desmarcada. A rotulagem assistida mL requer mais dados do que você vai usar neste tutorial.

1. Selecione **Create project** (Criar projeto).

Esta página não se atualiza automaticamente. Após uma pausa, refresque manualmente a página até que o estado do projeto mude para **Criado**.

## <a name="start-labeling"></a>Comece a rotular

Criou agora os seus recursos Azure e configura um projeto de rotulagem de dados. É hora de adicionar etiquetas aos seus dados.

### <a name="tag-the-images"></a>Marque as imagens

Nesta parte do tutorial, você mudará as funções do administrador do *projeto* para o de um *labeler*.  Qualquer pessoa que tenha acesso ao seu espaço de trabalho pode tornar-se uma etiqueta.

1. No [estúdio Machine Learning,](https://ml.azure.com)selecione **Data labeling** no lado esquerdo para encontrar o seu projeto.  

1. Selecione **a ligação de etiqueta** para o projeto.

1. Leia as instruções e, em seguida, selecione **Tarefas**.

1. Selecione uma imagem de miniatura no direito de exibir o número de imagens que deseja rotular de uma só vez. Tem de rotular todas estas imagens antes de poder seguir em frente. Só mude os layouts quando tiver uma nova página de dados não rotulados. A troca de layouts limpa o trabalho de marcação em curso da página.

1. Selecione uma ou mais imagens e, em seguida, selecione uma etiqueta para aplicar à seleção. A etiqueta aparece abaixo da imagem.  Continue a selecionar e marcar todas as imagens na página.  Para selecionar simultaneamente todas as imagens apresentadas, **selecione Selecione todas**. Selecione pelo menos uma imagem para aplicar uma etiqueta.


    > [!TIP]
    > Pode selecionar as primeiras nove tags utilizando as teclas numerais do seu teclado.

1. Uma vez que todas as imagens na página estejam marcadas, **selecione Submeter** para submeter estas etiquetas.

    ![Identificar imagens](media/tutorial-labeling/catsndogs.gif)

1. Depois de submeter tags para os dados em questão, o Azure atualiza a página com um novo conjunto de imagens da fila de trabalho.

## <a name="complete-the-project"></a>Complete o projeto

Agora vais mudar de função para o administrador do *projeto* para o projeto de rotulagem.

Como gestor, pode querer rever o trabalho do seu rotulador.  

### <a name="review-labeled-data"></a>Rever dados rotulados

1. No [estúdio Machine Learning,](https://ml.azure.com)selecione **Data labeling** no lado esquerdo para encontrar o seu projeto.  

1. Selecione o link de nome do projeto.

1. O Dashboard mostra-lhe o progresso do seu projeto.

1. No topo da página, selecione **Dados.**

1. No lado esquerdo, selecione **dados etiquetados** para ver as suas imagens marcadas.  

1. Quando discordar de uma etiqueta, selecione a imagem e, em seguida, **selecione Rejeitar** na parte inferior da página.  As etiquetas serão removidas e a imagem é colocada de volta na fila de imagens não etiquetadas.

### <a name="export-labeled-data"></a>Exportação de dados rotulados

Pode exportar os dados do rótulo para a experimentação de Machine Learning a qualquer momento. Os utilizadores exportam várias vezes e treinam diferentes modelos, em vez de esperarem que todas as imagens sejam rotuladas.

As etiquetas de imagem podem ser exportadas em [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados de Aprendizagem automática Azure. O formato do conjunto de dados facilita a utilização para a formação em Azure Machine Learning.  

1. No [estúdio Machine Learning,](https://ml.azure.com)selecione **Data labeling** no lado esquerdo para encontrar o seu projeto.  

1. Selecione o link de nome do projeto.

1. Selecione **Export** e escolha **Export como Azure ML Dataset**. 

    O estado da exportação aparece logo abaixo do botão **Exportação.** 

1. Uma vez exportadas com sucesso as etiquetas, selecione **Conjuntos de dados** do lado esquerdo para ver os resultados.

## <a name="clean-up-resources"></a>Limpar os recursos


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, rotulaste imagens.  Agora use os seus dados rotulados:

> [!div class="nextstepaction"]
> [Treine um modelo de reconhecimento de imagem de aprendizagem automática.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)
