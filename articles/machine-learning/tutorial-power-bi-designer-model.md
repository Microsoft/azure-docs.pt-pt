---
title: 'Tutorial: Arrastar e largar para criar o modelo preditivo (parte 1 de 2)'
titleSuffix: Azure Machine Learning
description: Aprenda a construir e implementar um modelo preditivo de machine learning com designer, para que possa usá-lo para prever resultados no Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370233"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Tutorial: Power BI integration - arrastar e largar para criar o modelo preditivo (parte 1 de 2)

Na primeira parte deste tutorial, treina-se e implementa-se um modelo preditivo de aprendizagem automática utilizando o designer Azure Machine Learning - uma interface de utilizador de baixo código. Na parte 2, usará o modelo para prever os resultados no Microsoft Power BI.

Neste tutorial:

> [!div class="checklist"]
> * Criar um exemplo de cálculo de aprendizagem automática Azure
> * Criar um cluster de inferências Azure Machine Learning
> * Criar um conjunto de dados
> * Preparar um modelo de regressão
> * Implemente o modelo para um ponto final de pontuação em tempo real


Existem três maneiras diferentes de criar e implementar o modelo que vai utilizar no Power BI.  Este artigo abrange a Opção B: Treinar e implementar modelos utilizando designer.  Esta opção mostra uma experiência de autoria de baixo código utilizando o designer (uma interface de utilizador de arrastar e largar).  

Em vez disso, pode usar:

* [Opção A: Treine e implemente modelos usando Cadernos](tutorial-power-bi-custom-model.md) - uma experiência de autoria de código usando cadernos Jupyter hospedados no estúdio Azure Machine Learning.
* [Opção C: Treine e implemente modelos utilizando ML automatizado](tutorial-power-bi-automated-model.md) - uma experiência de autoria sem código que automatiza totalmente a preparação de dados e a formação de modelos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure[(está disponível um teste gratuito).](https://aka.ms/AMLFree) 
- Uma área de trabalho do Azure Machine Learning. Se ainda não tem um espaço de trabalho, siga [como criar um espaço de trabalho para aprendizagem de máquinas Azure.](./how-to-manage-workspace.md#create-a-workspace)
- Conhecimento introdutório de fluxos de trabalho de aprendizagem automática.


## <a name="create-compute-for-training-and-scoring"></a>Criar computação para treino e pontuação

Nesta secção, cria-se uma *instância computacional,* que é usada para treinar modelos de aprendizagem automática. Além disso, cria um *cluster de inferências* que será usado para hospedar o modelo implantado para pontuação em tempo real.

Inicie sessão no [estúdio Azure Machine Learning](https://ml.azure.com) e selecione **Compute** a partir do menu da esquerda seguido por **New**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Screenshot mostrando como criar instância computacional":::

No ecrã de **instância computacional** resultante, selecione um tamanho VM (para este tutorial, selecione a `Standard_D11_v2` ) seguido por **Next**. Na página 'Definição', forneça um nome válido para a sua instância de computação seguida da seleção **criar**. 

>[!TIP]
> A instância computacional também pode ser usada para criar e executar cadernos.

Agora pode ver a sua instância de computação **O Estado** está **a criar** - levará cerca de 4 minutos para a máquina ser a provisionada. Enquanto espera, selecione o **separador Inference Cluster** na página do cálculo seguido de **New**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Screenshot mostrando como criar um cluster de inferência":::

Na página de **cluster de inferências** resultante, selecione uma região seguida de um tamanho VM (para este tutorial, selecione `Standard_D11_v2` a), em seguida, selecione **Next**. Na página **Configurações configurações de configuração:**

1. Fornecer um nome computacional válido
1. Selecione **Dev-test** como o propósito do cluster (cria um único nó para hospedar o modelo implantado)
1. Selecione **Criar**

Agora pode ver o seu cluster de inferência **Status** está **a criar** - levará cerca de 4 minutos para o seu único aglomerado de nós ser implantado.

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Neste tutorial, utilize o [conjunto de dados diabetes,](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)que é disponibilizado em [Azure Open Datasets.](https://azure.microsoft.com/services/open-datasets/)

Para criar o conjunto de dados, no menu esquerdo selecione **Datasets**, em **seguida, Criar Conjunto de Dados** - verá as seguintes opções:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Screenshot mostrando como criar um novo conjunto de dados":::

Selecione **A partir de conjuntos de dados abertos** e, em seguida, em Criar conjunto de dados a partir do ecrã **De conjuntos de dados abertos:**

1. Pesquisa de *diabetes* usando a barra de pesquisa
1. Selecione **Amostra: Diabetes**
1. Selecione **Seguinte**
1. Forneça um nome para o seu conjunto de dados - *diabetes*
1. Selecione **Criar**

Pode explorar os dados selecionando o Conjunto de Dados seguido por **Explore:**

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Screenshot mostrando como explorar dataset":::

Os dados têm 10 variáveis de entrada de base (como idade, sexo, índice de massa corporal, pressão arterial média e seis medições de soro sanguíneo), e uma variável-alvo chamada **Y** (uma medida quantitativa da progressão da diabetes um ano após a linha de base).

## <a name="create-a-machine-learning-model-using-designer"></a>Criar um modelo de machine learning usando designer

Uma vez criado o computação e conjuntos de dados, pode passar a criar o modelo de machine learning utilizando o designer. No estúdio Azure Machine Learning, selecione **Designer** seguido de **New Pipeline**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Screenshot mostrando como criar um novo oleoduto":::

Você vê uma *tela* em branco onde também pode ver um **menu Definições**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Screenshot mostrando como selecionar um alvo de computação":::

No **menu Definições**, **selecione** o alvo do cálculo e, em seguida, selecione a instância de cálculo que criou anteriormente seguida por **Save**. Mude o nome do seu **Rascunho** para algo mais memorável (por *exemplo, diabetes-modelo)* e introduza uma descrição.

Em seguida, em ativos listados expandir **Datasets** e localizar o conjunto de dados da **diabetes** - arrastar e largar este módulo na tela:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Screenshot mostrando como arrastar um componente em":::

Em seguida, arraste e deixe cair os seguintes componentes sobre a tela:

1. Regressão linear (localizada em **algoritmos de aprendizagem automática)**
1. Modelo de comboio (localizado em **Model Training)**

A sua tela deve parecer (note que a parte superior e inferior dos componentes tem pequenos círculos chamados portas - realçados a vermelho abaixo):

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Screenshot mostrando como os componentes não conectados":::
 
Em seguida, tens de *ligar* estes componentes. Selecione a porta na parte inferior do conjunto de dados da **diabetes** e arraste-a para a porta direita na parte superior do componente **do modelo Train.** Selecione a porta na parte inferior do componente linear de **regressão** e arraste para a porta esquerda no topo da porta **modelo Train.**

Escolha a coluna no conjunto de dados para ser usada como a variável de etiqueta (alvo) para prever. Selecione o componente **do modelo Train** seguido da coluna **Editar**. A partir da caixa de diálogo - Selecione o **nome da Coluna de Entrada** seguida de **Y** na lista de drop-down:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Coluna de etiqueta seletiva screenshot":::

Selecione **Guardar**. O seu *fluxo de trabalho de* aprendizagem automática deve ser o seguinte:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Screenshot mostrando como os componentes conectados":::

**Selecione Submeter** e, em seguida, **Criar novo** em experiência. Fornecer um nome para a experiência seguida por **Submit**.

>[!NOTE]
> Deve levar cerca de 5 minutos para a sua experiência completar na primeira corrida. As execuções subsequentes são muito mais rápidas - caches de design já executam componentes para reduzir a latência.

Quando a experiência estiver concluída, vê-se:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Screenshot mostrando execução completa":::

Pode inspecionar os registos da experiência selecionando o **modelo Train** seguido por **Outputs + logs**.

## <a name="deploy-the-model"></a>Implementar o modelo

Para implementar o modelo, selecione **Create Inference Pipeline** (localizado na parte superior da tela) seguido por **pipeline de inferência em tempo real**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Screenshot mostrando o gasoduto de inferência em tempo real":::
 
O gasoduto condensa-se apenas nos componentes necessários para fazer a pontuação do modelo. Quando marcar os dados não conhecerá os valores variáveis-alvo, portanto podemos remover **Y** do conjunto de dados. Para remover, adicione à tela uma **colunas Selecione no componente Dataset.** Ligue o componente para que o conjunto de dados da diabetes seja a entrada, e os resultados são a saída para o componente **do Modelo de Pontuação:**

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Screenshot mostrando a remoção de uma coluna":::

Selecione **as Colunas Selecionadas no** componente Dataset na tela seguida de **Colunas de Edição**. No diálogo de colunas Select, selecione **por nome** e, em seguida, certifique-se de que todas as variáveis de entrada são selecionadas, mas **não** o alvo:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Screenshot mostrando a remoção de uma coluna definições":::

Selecione **Guardar**. Por fim, selecione o componente **'Modelo de Pontuação'** e certifique-se de que as **colunas de pontuação do apêndice para a** caixa de verificação de saída não são controladas (apenas as previsões são enviadas de volta, em vez das entradas *e* previsões, reduzindo a latência):

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Screenshot mostrando definições de componente do modelo de pontuação":::

**Selecione Submeter-se** na parte superior da tela.

Quando tiver executado com sucesso o gasoduto de inferência, pode então colocar o modelo no seu cluster de inferências. Selecione **Implementar**, que mostrará a caixa de diálogo **de ponto final em tempo real.** **Selecione Implementar novo ponto final** em tempo real , nomeie o meu modelo de **ponta-ponto,** selecione a inferência que criou anteriormente, selecione **Implementar**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Screenshot mostrando definições de ponto final em tempo real":::
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, viu como treinar e implementar um modelo de designer. Na parte seguinte, aprende-se a consumir (pontuar) este modelo a partir do Power BI.

> [!div class="nextstepaction"]
> [Tutorial: Consumir modelo em Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
