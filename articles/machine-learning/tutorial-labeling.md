---
title: 'Tutorial: Criar um projeto de rotulagem para classificação de imagem'
titleSuffix: Azure Machine Learning
description: Aprenda a gerir o processo de rotulagem de imagens para que possam ser usadas em modelos de classificação de imagem multi-classe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/02/2020
ms.openlocfilehash: fa33861d86ff8bee3e2a34fb3d93032ac6180880
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879722"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Tutorial: Criar um projeto de rotulagem para classificação de imagem multi-classe 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este tutorial mostra-lhe como gerir o processo de rotulagem (também designado por marcação) imagens a serem usadas como dados para a construção de modelos de aprendizagem automática. A rotulagem de dados em Azure Machine Learning está em pré-visualização pública.

Se quiser treinar um modelo de machine learning para classificar imagens, precisa de centenas ou mesmo milhares de imagens que estejam corretamente rotuladas.  O Azure Machine Learning ajuda-o a gerir o progresso da sua equipa privada de especialistas em domínios à medida que rotulam os seus dados.
 
Neste tutorial, você usará imagens de gatos e cães.  Uma vez que cada imagem é um gato ou um cão, este é um projeto de rotulagem *multi-classe.* Vai aprender a:

> [!div class="checklist"]
>
> * Crie uma conta de armazenamento Azure e faça upload de imagens para a conta.
> * Criar um espaço de trabalho azure machine learning.
> * Crie um projeto de rotulagem de imagem multiclasse.
> * Rotule os seus dados.  Ou você ou os seus rotuladores podem executar esta tarefa.
> * Complete o projeto revendo e exportando os dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://aka.ms/AMLFree)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implementar modelos de aprendizagem automática. Liga o seu grupo de subscrição e recursos Azure a um objeto facilmente consumido no serviço.

Cria um espaço de trabalho através do portal Azure, uma consola baseada na web para gerir os seus recursos Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Inicie um projeto de rotulagem

Em seguida, você vai gerir o projeto de rotulagem de dados no estúdio Azure Machine Learning, uma interface consolidada que inclui ferramentas de machine learning para realizar cenários de ciência de dados para praticantes de ciência de dados de todos os níveis de habilidade. O estúdio não é suportado nos navegadores Do Internet Explorer.

1. Inscreva-se no [estúdio Azure Machine Learning.](https://ml.azure.com)

1. Selecione a sua subscrição e o espaço de trabalho que criou.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Criar uma loja de dados

As lojas de dados Azure Machine Learning são usadas para armazenar informações de ligação, como o seu ID de subscrição e autorização de ficha. Aqui utiliza uma loja de dados para se ligar à conta de armazenamento que contém as imagens para este tutorial.

1. No lado esquerdo do seu espaço de trabalho, selecione **Datastores**.

1. Selecione **+ Nova loja**de dados .

1. Preencha o formulário com estas definições:

    Campo|Descrição 
    ---|---
    Nome da loja de dados | Dê um nome à loja de dados.  Aqui usamos **labeling_tutorial.**
    Tipo de datastore | Selecione o tipo de armazenamento.  Aqui utilizamos **o Armazenamento Azure Blob,** o armazenamento preferido para imagens.
    Método de seleção de contas | Selecione **Introduzir manualmente**.
    do IdP | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Tipo de autenticação | Selecione **token SAS**.
    Chave de conta | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Selecione **Criar** para criar a loja de dados.

### <a name="add-labelers-to-workspace"></a>Adicione etiquetadores ao espaço de trabalho

Instale o seu espaço de trabalho para incluir todas as pessoas que irão rotular dados para qualquer um dos seus projetos.  Mais tarde, irá adicionar estes rótulos ao seu projeto de rotulagem específico.

1. Do lado esquerdo, selecione a **rotulagem de Dados**.

1. No topo da página, selecione **Labelers**.

1. Selecione **Adicionar etiquetador** para adicionar o endereço de e-mail de um rotulador.

1. Continue a adicionar mais etiquetadores até terminar.

### <a name="create-a-labeling-project"></a>Criar um projeto de rotulagem

Agora que tem a sua lista de rotuladores e acesso aos dados que pretende ter rotulados, crie o seu projeto de rotulagem.

1. No topo da página, selecione **Projetos.**

1. Selecione **+ Adicionar projeto**.

    ![Criar um projeto](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>Detalhes do projeto

1. Utilize a seguinte entrada para o formulário de detalhes do **Projeto:**

    Campo|Descrição 
    ---|---
    Nome do projeto | Dê um nome ao seu projeto.  Aqui usaremos **tutoriais-gatos-n-cães.**
    Tipo de tarefa de rotulagem | Selecione **Classificação de Imagem Multi-classe**.  
    
    Selecione **Next** para continuar a criar o projeto.

### <a name="select-or-create-a-dataset"></a>Selecione ou crie um conjunto de dados

1.   No **Select ou criar um** formulário de conjunto de dados, selecione a segunda escolha, **crie um conjunto de dados**e, em seguida, selecione o link A partir da **datastore**.

1. Utilize a seguinte entrada para o conjunto de dados Criar a partir do formulário **datastore:**

    1. No formulário **de informação Básica,** adicione um nome, aqui usaremos **imagens-para-tutorial**.  Adicione uma descrição, se desejar.  Em seguida, selecione **Seguinte**.
    1. No formulário de **seleção da Datastore,** utilize o dropdown para selecionar a sua loja de **dados previamente criada,** por **exemplo, tutorial_images (Armazenamento De Blob Azure)**
    1. Em seguida, ainda no formulário de **seleção da Datastore,** selecione **Navegar** e, em seguida, selecione **MultiClass - DogsCats**.  Selecione **Guardar** para utilizar **/MultiClass - DogsCats** como caminho.
    1. Selecione **Next** para confirmar detalhes e, em seguida, **Criar** para criar o conjunto de dados.
    1. Selecione o círculo ao lado do nome do conjunto de dados na lista, por exemplo **imagens-para-tutorial**.

1. Selecione **Next** para continuar a criar o projeto.

### <a name="label-classes"></a>Aulas de etiquetas

1. No formulário de **etiquetas,** digite um nome de etiqueta e, em seguida, **selecione +Adicionar etiqueta** para escrever a próxima etiqueta.  Para este projeto, os rótulos são **Cat,** **Dog**e **Uncertain.**

1. Selecione **Seguinte** quando tiver adicionado todas as etiquetas.

### <a name="labeling-instructions"></a>Instruções de rotulagem

1. No formulário de instruções de **rotulagem,** pode fornecer um link para um site que fornece instruções detalhadas para os seus rotuladores.  Vamos deixá-lo em branco para este tutorial.

1. Também pode adicionar uma breve descrição da tarefa diretamente no formulário.  Tutorial de rotulagem de tipo **- Gatos & Cães.**

1. Selecione **Next**.

1. No formulário de **rotulagem assistida ML,** deixe a caixa de verificação desmarcada. A rotulagem assistida ml requer mais dados do que irá utilizar neste tutorial.

1. Selecione **Create project** (Criar projeto).

Esta página não atualiza automaticamente. Após uma pausa, refresque manualmente a página até que o estado do projeto mude para **Created**.

### <a name="add-labelers-to-your-project"></a>Adicione etiquetadores ao seu projeto

Adicione alguns ou todos os seus rótulos a este projeto.

1. Selecione o nome do projeto para abrir o projeto.  

1. No topo da página, selecione **Equipas**.

1. Selecione a ligação **labeling_tutorial Default Team.**

1. Utilize agora **etiquetadores de asign para** adicionar os rótulos que pretende participar neste projeto. 

1. Selecione na lista de etiquetadores que criou anteriormente.  Uma vez selecionado todos os rótulos que deseja utilizar, selecione **etiquetadores de Assign** para adicioná-los à sua equipa de projeto padrão.

## <a name="start-labeling"></a>Começar a rotulagem

Criou agora os seus recursos Azure e configurou um projeto de rotulagem de dados. É hora de adicionar rótulos aos seus dados.

### <a name="notify-labelers"></a>Notificar os rótulos

Se tem muitas imagens para rotular, espero que também tenha muitos rótulos para completar a tarefa.  Agora vai querer enviar-lhes instruções para que possam aceder aos dados e começar a rotular.

1. No [estúdio Machine Learning,](https://ml.azure.com)selecione a **rotulagem** de Dados no lado esquerdo para encontrar o seu projeto.  

1. Selecione o link de nome do projeto.

1. No topo da página, selecione **Detalhes**.  Vê um resumo do seu projeto.

    ![Detalhes do projeto](media/tutorial-labeling/project-details.png)

1. Copie o link URL do **portal de rotulagem** para enviar aos seus rotuladores.

1. Agora selecione **Team** no topo para encontrar a sua equipa de rotulagem.  

1. Selecione o link de nome da equipa.

1. No topo da página, selecione **equipa de e-mail** para iniciar o seu email.  Pasta no url do portal de rotulagem que acabou de copiar.  

Cada vez que um rotulador vai para o portal URL, eles serão apresentados com mais imagens para rotular, até que a fila esteja vazia.  

### <a name="tag-the-images"></a>Marque as imagens

Nesta parte do tutorial, você mudará as funções do administrador do *projeto* para o de um *rotulador*.  Use o URL que enviou para a equipa.  Este URL leva-o ao portal de rotulagem para o seu projeto.  Se tivesse satisfeito instruções, veria-as aqui quando chegasse à página.

1. No topo da página, selecione **Tarefas** para começar a rotular.

1. Selecione uma imagem de miniatura à direita para exibir o número de imagens que pretende rotular de uma só vez. Tem de rotular todas estas imagens antes de poder seguir em frente. Só altere os layouts quando tiver uma página nova de dados não rotulados. Mudar os layouts iliba o trabalho de marcação em curso da página.

1. Selecione uma ou mais imagens e, em seguida, selecione uma etiqueta para aplicar à seleção. A etiqueta aparece abaixo da imagem.  Continue a selecionar e marque todas as imagens na página.  Para selecionar todas as imagens exibidas simultaneamente, **selecione todas**as . Selecione pelo menos uma imagem para aplicar uma etiqueta.


    > [!TIP]
    > Pode selecionar as primeiras nove etiquetas utilizando as teclas de número no teclado.

1. Uma vez marcadas todas as imagens da página, selecione **Submeter** para submeter estes rótulos.

    ![Identificar imagens](media/tutorial-labeling/catsndogs.gif)

1. Depois de submeter etiquetas para os dados disponíveis, o Azure atualiza a página com um novo conjunto de imagens da fila de trabalho.

## <a name="complete-the-project"></a>Complete o projeto

Agora vai mudar as funções de volta para o administrador do *projeto* para o projeto de rotulagem.

Como gerente, talvez queira rever o trabalho do seu rotulador.  

### <a name="review-labeled-data"></a>Rever dados rotulados

1. No [estúdio Machine Learning,](https://ml.azure.com)selecione a **rotulagem** de Dados no lado esquerdo para encontrar o seu projeto.  

1. Selecione o link de nome do projeto.

1. O Dashboard mostra-lhe o progresso do seu projeto.

1. No topo da página, selecione **Dados**.

1. Do lado esquerdo, selecione **dados rotulados** para ver as imagens marcadas.  

1. Quando discordar de uma etiqueta, selecione a imagem e, em seguida, selecione **Rejeitar** na parte inferior da página.  As etiquetas serão removidas e a imagem é colocada de volta na fila de imagens não marcadas.

### <a name="export-labeled-data"></a>Dados rotulados de exportação

Pode exportar os dados do rótulo para experimentação de Machine Learning a qualquer momento. Os utilizadores exportam frequentemente várias vezes e treinam diferentes modelos, em vez de esperarem que todas as imagens sejam rotuladas.

As etiquetas de imagem podem ser exportadas em [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados de Aprendizagem automática Azure. O formato dataset facilita a utilização para a formação em Azure Machine Learning.  

1. No [estúdio Machine Learning,](https://ml.azure.com)selecione a **rotulagem** de Dados no lado esquerdo para encontrar o seu projeto.  

1. Selecione o link de nome do projeto.

1. Selecione **Exportar** e escolha exportação como Conjunto de **Dados Azure ML**. 

    O estado da exportação aparece logo abaixo do botão **exportação.** 

1. Uma vez exportadas com sucesso as etiquetas, selecione **Datasets** no lado esquerdo para ver os resultados.

## <a name="clean-up-resources"></a>Limpar recursos


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, rotulou as imagens.  Agora use os seus dados rotulados:

> [!div class="nextstepaction"]
> [Treine um modelo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)de reconhecimento de imagem de aprendizagem automática.
