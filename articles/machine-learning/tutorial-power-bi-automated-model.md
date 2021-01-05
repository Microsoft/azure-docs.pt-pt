---
title: 'Tutorial: Criar o modelo preditivo utilizando ML automatizado (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a construir e implementar modelos automatizados de aprendizagem automática para que possa utilizar o melhor modelo para prever os resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814810"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Tutorial: Integração do Power BI - Criar o modelo preditivo utilizando machine learning automatizado (parte 1 de 2)

Na primeira parte deste tutorial, treina-se e implementa-se um modelo preditivo de aprendizagem automática. Utiliza machine learning automatizado (ML) no Azure Machine Learning Studio.  Na parte 2, usará o modelo de melhor desempenho para prever os resultados no Microsoft Power BI.

Neste tutorial:

> [!div class="checklist"]
> * Crie um cluster de cálculo de aprendizagem automática Azure.
> * Criar um conjunto de dados.
> * Crie uma corrida automatizada de aprendizagem automática.
> * Desloque o melhor modelo para um ponto final de pontuação em tempo real.


Existem três formas de criar e implementar o modelo que vai utilizar no Power BI.  Este artigo abrange "Opção C: Treinar e implementar modelos utilizando machine learning automatizado no estúdio."  Esta opção é uma experiência de autoria sem código. Automatiza totalmente a preparação de dados e a formação de modelos. 

Mas em vez disso, podias usar uma das outras opções:

* [Opção A: Treine e implemente modelos utilizando cadernos Jupyter](tutorial-power-bi-custom-model.md). Esta experiência de autoria de código usa cadernos Jupyter que estão hospedados no Azure Machine Learning Studio.
* [Opção B: Treine e implemente modelos utilizando o designer de aprendizagem automática Azure](tutorial-power-bi-designer-model.md). Esta experiência de autoria de código baixo utiliza uma interface de utilizador de arrastar e largar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se ainda não tiver uma subscrição, pode utilizar um [teste gratuito](https://aka.ms/AMLFree). 
- Uma área de trabalho do Azure Machine Learning. Se ainda não tem um espaço de trabalho, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure.](./how-to-manage-workspace.md#create-a-workspace)

## <a name="create-a-compute-cluster"></a>Criar um cluster computacional

A aprendizagem automática de máquinas treina muitos modelos de machine learning para encontrar o algoritmo e parâmetros "melhores". A Azure Machine Learning paraleliza o funcionamento do modelo de formação sobre um cluster computacional.

Para começar, no [Azure Machine Learning Studio,](https://ml.azure.com)no menu à esquerda, selecione **Compute**. Abra o **separador de clusters Compute.** Em seguida, selecione **New**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Screenshot mostrando como criar um cluster de computação.":::

Na página **de cluster de computação Create:**

1. Selecione um tamanho de VM. Para este tutorial, uma máquina **Standard_D11_v2** está bem.
1. Selecione **Seguinte**.
1. Forneça um nome de computação válido.
1. Mantenha **o número mínimo de nós** em `0` .
1. Alterar **o número máximo de nós** para `4` .
1. Selecione **Criar**.

O estado do seu cluster muda para **criar**.

>[!NOTE]
> O novo cluster tem 0 nós, por isso não são incorridos custos computativos. Só incorrem em custos quando o trabalho automatizado de aprendizagem automática funciona. O cluster recua para 0 automaticamente após 120 segundos de tempo inativo.


## <a name="create-a-dataset"></a>Criar um conjunto de dados

Neste tutorial, você usa o [conjunto de dados diabetes.](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html) Este conjunto de dados está disponível em [Conjuntos de Dados Azure Open](https://azure.microsoft.com/services/open-datasets/).

Para criar o conjunto de dados, no menu à esquerda, selecione **Datasets**. Em seguida, **selecione Criar conjunto de dados**. Veja as seguintes opções:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Screenshot mostrando como criar um novo conjunto de dados.":::

Selecione **A partir de conjuntos de dados abertos**. Em seguida, no conjunto de dados Criar a partir da página **Open Datasets:**

1. Use a barra de pesquisa para encontrar *diabetes.*
1. Selecione **Amostra: Diabetes**.
1. Selecione **Seguinte**.
1. Diga o nome da *diabetes* dataset.
1. Selecione **Criar**.

Para explorar os dados, selecione o conjunto de dados e, em seguida, **selecione Explore**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Screenshot mostrando como explorar um conjunto de dados.":::

Os dados têm 10 variáveis de entrada de base, tais como idade, sexo, índice de massa corporal, pressão arterial média e seis medições de soro sanguíneo. Também tem uma variável alvo, chamada **Y.** Esta variável-alvo é uma medida quantitativa da progressão da diabetes um ano após a linha de base.

## <a name="create-an-automated-machine-learning-run"></a>Crie uma corrida automatizada de aprendizagem automática

No [Azure Machine Learning Studio,](https://ml.azure.com)no menu à esquerda, selecione **ML Automatizado**. Em seguida, selecione **nova execução de ML automatizado**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Screenshot mostrando como criar uma nova corrida automatizada de aprendizagem automática de máquinas.":::

Em seguida, selecione o conjunto de dados da **diabetes** que criou mais cedo. Em seguida, selecione **Seguinte**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Screenshot mostrando como selecionar um conjunto de dados.":::
 
Na página de **execução Configure:**

1. Em **nome de experimento,** selecione **Criar novo**.
1. Diga o nome da experiência.
1. No campo **da coluna Target,** selecione **Y**.
1. No campo **de cluster de computação Select,** selecione o cluster de cálculo que criou anteriormente. 

O seu formulário preenchido deve ser assim:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Screenshot mostrando como configurar aprendizagem automática de máquinas.":::

Finalmente, selecione uma tarefa de aprendizagem automática. Neste caso, a tarefa é **regressão:**

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Screenshot mostrando como configurar uma tarefa.":::

Selecione **Concluir**.

> [!IMPORTANT]
> A aprendizagem automática de máquinas demora cerca de 30 minutos a terminar o treino dos 100 modelos.

## <a name="deploy-the-best-model"></a>Implementar o melhor modelo

Quando a aprendizagem automática termina, pode ver todos os modelos de machine learning que foram experimentados selecionando o separador **Modelos.** Os modelos são encomendados por desempenho; o modelo com melhor desempenho é mostrado primeiro. Depois de selecionar o melhor modelo, o botão **'Implementar'** está ativado:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Screenshot mostrando a lista de modelos.":::

Selecione **Implementar** para abrir uma janela **do modelo:**

1. Nomeie o seu *modelo de diabetes-modelo.*
1. Selecione **O Serviço de Contentores Azure**.
1. Selecione **Implementar**.

Deve ver uma mensagem que diz que o modelo foi implementado com sucesso.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você viu como treinar e implementar um modelo de machine learning usando machine learning automatizado. No próximo tutorial, aprenderás a consumir (pontuar) este modelo no Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir um modelo em Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
