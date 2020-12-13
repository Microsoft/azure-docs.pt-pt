---
title: 'Tutorial: Criar o modelo preditivo utilizando ML automatizado (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a construir e implementar modelos ML automatizados, para que possa utilizar o melhor modelo para prever os resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370352"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>Tutorial: Power BI integration - crie o modelo preditivo utilizando machine learning automatizado (parte 1 de 2)

Na primeira parte deste tutorial, treina-se e implementa-se um modelo preditivo de aprendizagem automática utilizando machine learning automatizado no estúdio Azure Machine Learning.  Na parte 2, usará o modelo de melhor desempenho para prever os resultados no Microsoft Power BI.

Neste tutorial:

> [!div class="checklist"]
> * Criar um cluster de cálculo de aprendizagem automática Azure
> * Criar um conjunto de dados
> * Criar uma execução ML automatizada
> * Implemente o melhor modelo para um ponto final de pontuação em tempo real


Existem três maneiras diferentes de criar e implementar o modelo que vai utilizar no Power BI.  Este artigo abrange a Opção C: Treinar e implementar modelos utilizando ML automatizado no estúdio.  Esta opção mostra uma experiência de autoria sem código que automatiza totalmente a preparação de dados e a formação de modelos. 

Em vez disso, pode usar:

* [Opção A: Treine e implemente modelos usando Cadernos](tutorial-power-bi-custom-model.md) - uma experiência de autoria de código usando cadernos Jupyter hospedados no estúdio Azure Machine Learning.
* [Opção B: Treine e implemente modelos utilizando designer](tutorial-power-bi-designer-model.md)- uma experiência de autoria de baixo código usando o Designer (uma interface de utilizador de arrastar e largar).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure[(está disponível um teste gratuito).](https://aka.ms/AMLFree) 
- Uma área de trabalho do Azure Machine Learning. Se ainda não tem um espaço de trabalho, siga [como criar um espaço de trabalho para aprendizagem de máquinas Azure.](./how-to-manage-workspace.md#create-a-workspace)

## <a name="create-compute-cluster"></a>Criar cluster de cálculo

O ML automatizado treina automaticamente vários modelos de machine learning diferentes para encontrar o algoritmo e parâmetros "melhores". A Azure Machine Learning paralelamente à execução do modelo de formação sobre um cluster computacional.

No [Azure Machine Learning Studio,](https://ml.azure.com)selecione **Compute** no menu esquerdo seguido do **separador Compute Clusters.** Selecione **Novo**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Screenshot mostrando como criar um cluster de computação":::

No ecrã de **cluster de computação Create:**

1. Selecione um tamanho VM (para efeitos deste tutorial, uma `Standard_D11_v2` máquina está bem).
1. Selecione **Seguinte**
1. Fornecer um nome computacional válido
1. Mantenha **o número mínimo de nós** em 0
1. Alterar **número máximo de nós** para 4
1. Selecione **Criar**

Pode ver que o estado do seu cluster mudou para **Criar.**

>[!NOTE]
> Quando o cluster é criado terá 0 nós, o que significa que não são incorridos custos de cálculo. Só incorrem em custos quando o trabalho automatizado da ML funciona. O cluster voltará a 0 automaticamente para si após 120 segundos de tempo inativo.


## <a name="create-dataset"></a>Criar conjunto de dados

Neste tutorial, utilize o [conjunto de dados diabetes,](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)que é disponibilizado em [Azure Open Datasets.](https://azure.microsoft.com/services/open-datasets/)

Para criar o conjunto de **dados,** selecione datasets menu esquerdo seguido por **Create Dataset** - verá as seguintes opções:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Screenshot mostrando como criar um novo conjunto de dados":::

Selecione **A partir de conjuntos de dados abertos** e, em seguida, em Criar conjunto de dados a partir do ecrã **De conjuntos de dados abertos:**

1. Pesquisa de *diabetes* usando a barra de pesquisa
1. Selecione **Amostra: Diabetes**
1. Selecione **Seguinte**
1. Forneça um nome para o seu conjunto de dados - *diabetes*
1. Selecione **Criar**

Pode explorar os dados selecionando o Conjunto de Dados seguido por **Explore:**

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Screenshot mostrando como explorar conjunto de dados":::

Os dados têm 10 variáveis de entrada de base (como idade, sexo, índice de massa corporal, pressão arterial média e seis medições de soro sanguíneo), e uma variável-alvo chamada **Y** (uma medida quantitativa da progressão da diabetes um ano após a linha de base).

## <a name="create-automated-ml-run"></a>Criar ml automatizado executar

No [Azure Machine Learning Studio](https://ml.azure.com) selecione **ML automatizado** no menu à esquerda seguido de Nova Corrida **ML Automatizada**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Screenshot mostrando como criar uma nova execução automática de ML":::

Em seguida, selecione o conjunto de dados **de diabetes** que criou anteriormente e selecione **Seguinte**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Screenshot mostrando como selecionar um conjunto de dados":::
 
No ecrã de **execução Configure:**

1. Em **nome de experiência,** selecione **Criar novo**
1. Fornecer uma experiência um nome
1. No campo da coluna Target, selecione **Y**
1. No campo **de cluster de computação Select** selecione o cluster de cálculo que criou anteriormente. 

O seu formulário preenchido deve ser semelhante a:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Screenshot mostrando como configurar ML automatizado":::

Finalmente, você precisa selecionar a tarefa de machine learning para executar, que é **Regression**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Screenshot mostrando como configurar tarefa":::

Selecione **Concluir**.

> [!IMPORTANT]
> Levará cerca de 30 minutos para ml automatizado terminar o treino dos 100 modelos diferentes.

## <a name="deploy-the-best-model"></a>Implementar o melhor modelo

Uma vez concluída a execução automática de ML, pode ver a lista de todos os diferentes modelos de machine learning que foram experimentados selecionando o separador **Modelos.** Os modelos são encomendados em ordem de desempenho - o modelo de melhor desempenho será mostrado primeiro. Quando selecionar o melhor modelo, o botão **'Implementar'** estará ativado:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Screenshot mostrando a lista de modelos":::

Selecionando **Implementar**, apresentará um ecrã **de modelo:**

1. Fornecer um nome para o seu serviço de modelo - use **diabetes-modelo**
1. Selecione **O Serviço de Contentores Azure**
1. Selecione **Implementar**

Deve ver uma mensagem que diz que o modelo foi implementado com sucesso.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você viu como treinar e implementar um modelo de machine learning usando ML automatizado. No próximo tutorial é-lhe mostrado como consumir (pontuar) este modelo do Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir modelo em Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
