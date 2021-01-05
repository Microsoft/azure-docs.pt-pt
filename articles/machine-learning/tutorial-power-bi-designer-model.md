---
title: 'Tutorial: Arrastar e largar para criar o modelo preditivo (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a construir e implementar um modelo preditivo de aprendizagem automática utilizando o designer. Mais tarde, pode usá-lo para prever resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814793"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Tutorial: Integração do Power BI - Arrastar e largar para criar o modelo preditivo (parte 1 de 2)

Na parte 1 deste tutorial, treina-se e implementa-se um modelo preditivo de aprendizagem automática utilizando o designer de Machine Learning Azure. O designer é uma interface de utilizador de baixo código. Na parte 2, usará o modelo para prever os resultados no Microsoft Power BI.

Neste tutorial:

> [!div class="checklist"]
> * Crie uma instância computacional de aprendizagem automática Azure.
> * Crie um cluster de inferências Azure Machine Learning.
> * Criar um conjunto de dados.
> * Treine um modelo de regressão.
> * Desloque o modelo para um ponto final de pontuação em tempo real.


Existem três formas de criar e implementar o modelo que vai utilizar no Power BI.  Este artigo abrange "Opção B: Treinar e implementar modelos utilizando o designer."  Esta opção é uma experiência de autoria de baixo código que utiliza a interface do designer.  

Mas em vez disso, podias usar uma das outras opções:

* [Opção A: Treine e implemente modelos utilizando cadernos Jupyter](tutorial-power-bi-custom-model.md). Esta experiência de autoria de código usa cadernos Jupyter que estão hospedados no Azure Machine Learning Studio.
* [Opção C: Treine e implemente modelos utilizando machine learning automatizado](tutorial-power-bi-automated-model.md). Esta experiência de autoria sem código automatiza totalmente a preparação de dados e a formação de modelos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se ainda não tiver uma subscrição, pode utilizar um [teste gratuito](https://aka.ms/AMLFree). 
- Uma área de trabalho do Azure Machine Learning. Se ainda não tem um espaço de trabalho, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure.](./how-to-manage-workspace.md#create-a-workspace)
- Conhecimento introdutório de fluxos de trabalho de aprendizagem automática.


## <a name="create-compute-to-train-and-score"></a>Criar cálculo para treinar e pontuar

Nesta secção, cria-se uma *instância computacional.* Os casos de cálculo são usados para treinar modelos de aprendizagem automática. Também cria um *cluster de inferências* para hospedar o modelo implantado para pontuação em tempo real.

Inscreva-se no [Azure Machine Learning Studio.](https://ml.azure.com) No menu à esquerda, **selecione Compute** e, em seguida, **New**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Screenshot mostrando como criar uma instância de computação.":::

Na página **de instância de computação Create,** selecione um tamanho VM. Para este tutorial, selecione um **VM Standard_D11_v2.** Em seguida, selecione **Seguinte**. 

Na página **Definições,** diga o seu caso de computação. Em seguida, selecione **Criar**. 

>[!TIP]
> Também pode usar a instância computacional para criar e executar cadernos.

O seu **estado** de caso de computação está agora **a criar**. A máquina demora cerca de 4 minutos a providenciar. 

Enquanto espera, na página **Compute,** selecione o **separador clusters de Inferência.** Em seguida, selecione **New**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Screenshot mostrando como criar um cluster de inferência.":::

Na página **de cluster de inferência Create,** selecione uma região e um tamanho VM. Para este tutorial, selecione um **VM Standard_D11_v2.** Em seguida, selecione **Seguinte**. 

Na página **Configurações configurações de configuração:**

1. Forneça um nome de computação válido.
1. Selecione **Dev-teste** como o propósito do cluster. Esta opção cria um único nó para hospedar o modelo implantado.
1. Selecione **Criar**.

O seu **estado** de agrupamento de inferência está agora **a criar.** O seu único aglomerado de nó leva cerca de 4 minutos a ser acionado.

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Neste tutorial, você usa o [conjunto de dados diabetes.](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html) Este conjunto de dados está disponível em [Conjuntos de Dados Azure Open](https://azure.microsoft.com/services/open-datasets/).

Para criar o conjunto de dados, no menu à esquerda, selecione **Datasets**. Em seguida, **selecione Criar conjunto de dados**. Veja as seguintes opções:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Screenshot mostrando como criar um novo conjunto de dados.":::

Selecione **A partir de conjuntos de dados abertos**. No **conjunto de dados Criar a partir da página Open Datasets:**

1. Use a barra de pesquisa para encontrar *diabetes.*
1. Selecione **Amostra: Diabetes**.
1. Selecione **Seguinte**.
1. Diga o nome da *diabetes* dataset.
1. Selecione **Criar**.

Para explorar os dados, selecione o conjunto de dados e, em seguida, **selecione Explore**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Screenshot mostrando como explorar um conjunto de dados.":::

Os dados têm 10 variáveis de entrada de base, tais como idade, sexo, índice de massa corporal, pressão arterial média e seis medições de soro sanguíneo. Também tem uma variável alvo, chamada **Y.** Esta variável-alvo é uma medida quantitativa da progressão da diabetes um ano após a linha de base.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Crie um modelo de aprendizagem automática usando o designer

Depois de criar o computação e conjuntos de dados, pode utilizar o designer para criar o modelo de aprendizagem automática. No Azure Machine Learning Studio, selecione **Designer** e, em seguida, **Novo pipeline**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Screenshot mostrando como criar um novo oleoduto.":::

Você vê uma *tela* em branco e um menu **Definições:**

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Screenshot mostrando como selecionar um alvo de computação.":::

No menu **Definições,** escolha **Selecionar o alvo do cálculo**. Selecione a instância de cálculo que criou anteriormente e, em seguida, **selecione Guardar**. Mude o **nome Draft** para algo mais memorável, como o *modelo diabetes.* Finalmente, insira uma descrição.

Na lista de ativos, expanda **os Conjuntos de Dados** e localize o conjunto de dados da **diabetes.** Arraste este componente para a tela:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Screenshot mostrando como arrastar um componente para a tela.":::

Em seguida, arraste os seguintes componentes para a tela:

1. **Regressão Linear** (localizada em **algoritmos de aprendizagem automática)**
1. **Modelo de trem** (localizado em **Model Training)**

Na sua tela, note os círculos na parte superior e inferior dos componentes. Estes círculos são portos.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Screenshot mostrando as portas em componentes não ligados.":::
 
Agora *ligue* os componentes. Selecione a porta na parte inferior do conjunto de dados da **diabetes.** Arraste-o para a porta no lado superior direito do componente **Train Model.** Selecione a porta na parte inferior do componente **de regressão linear.** Arraste-o para a porta no lado superior esquerdo do componente **Train Model.**

Escolha a coluna de conjunto de dados para usar como variável de etiqueta (alvo) para prever. Selecione o componente **Modelo de Comboio** e, em seguida, selecione coluna **Editar**. 

Na caixa de diálogo, selecione **Introduza o nome da coluna**  >  **Y**:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Screenshot mostrando como selecionar uma coluna de etiqueta.":::

Selecione **Guardar**. O seu *fluxo de trabalho de* aprendizagem automática deve ser assim:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Screenshot mostrando componentes ligados.":::

Selecione **Submeter**. Em **Experiência**, selecione **Criar novo**. Nomeie a experiência e, em seguida, **selecione Enviar por isso**.

>[!NOTE]
> A primeira corrida da sua experiência deve demorar cerca de 5 minutos. As execuções subsequentes são muito mais rápidas porque os componentes de caches do designer que foram executados para reduzir a latência.

Quando a experiência terminar, vê-se esta vista:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Screenshot mostrando uma execução completa.":::

Para inspecionar os registos de experiências, selecione **Train Model** e, em seguida, selecione **Outputs + logs**.

## <a name="deploy-the-model"></a>Implementar o modelo

Para implantar o modelo, na parte superior da tela, **selecione Criar o gasoduto** de  >  **inferência em tempo real:**

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Screenshot mostrando onde selecionar um pipeline de inferência em tempo real.":::
 
O gasoduto condensa-se apenas nos componentes necessários para marcar o modelo. Quando marcar os dados, não saberá os valores variáveis-alvo. Para que possa remover **Y** do conjunto de dados. 

Para remover **Y,** adicione uma **colunas selecionadas no** componente Dataset à tela. Ligue o componente para que o conjunto de dados da diabetes seja a entrada. Os resultados são a saída para o componente **Do Modelo de Pontuação:**

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Screenshot mostrando como remover uma coluna.":::

Na tela, selecione as **Colunas Selecionadas no** componente Dataset e, em seguida, selecione **Editar Colunas**. 

Na caixa de diálogo **de colunas Select,** escolha **por nome**. Em seguida, certifique-se de que todas as variáveis de entrada são selecionadas, mas o alvo *não* é selecionado:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Screenshot mostrando como remover as definições da coluna.":::

Selecione **Guardar**. 

Por fim, selecione o componente **'Modelo de Pontuação'** e certifique-se de que as **colunas de pontuação do apêndice para a** caixa de verificação de saída estão limpas. Para reduzir a latência, as previsões são enviadas de volta sem as entradas.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Screenshot mostrando definições para o componente Do Modelo de Pontuação.":::

Na parte superior da tela, **selecione Enviar por favor.**

Depois de executar com sucesso o gasoduto de inferência, pode colocar o modelo no seu cluster de inferências. Selecione **Implementar**. 

Na caixa de diálogo de ponto final de **configuração em tempo real,** selecione **Implementar novo ponto final em tempo real**. Diga o nome do *meu modelo de diabetes.* Selecione a inferência criada anteriormente e, em seguida, **selecione Implementar**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Screenshot mostrando as definições do ponto final em tempo real.":::
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, viu como treinar e implementar um modelo de designer. Na parte seguinte, aprende-se a consumir (pontuar) este modelo no Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir um modelo em Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
