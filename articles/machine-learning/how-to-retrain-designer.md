---
title: Retre modelos utilizando o designer de machine learning Azure (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como retreinar modelos com oleodutos publicados no designer de Machine Learning Azure (pré-visualização).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.custom: designer
ms.openlocfilehash: 7a132734c3ccb6d5d143053b83723950d154d74c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653688"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Preparar novamente modelos com o Azure Machine Learning Designer (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo de como fazer, aprende-se a usar o designer de Machine Learning Azure para retreinar um modelo de aprendizagem automática. Utilizará oleodutos publicados para automatizar o seu fluxo de trabalho e definir parâmetros para treinar o seu modelo em novos dados. 

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Treine um modelo de aprendizagem automática.
> * Crie um parâmetro de gasoduto.
> * Publique o seu oleoduto de treino.
> * Retrete o seu modelo com novos parâmetros.

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho azure machine learning com a Enterprise SKU.
* Complete a parte 1 desta série de como fazer, [Transforme os dados no designer.](how-to-designer-transform-data.md)

Este artigo também assume que você tem conhecimento básico de construção de oleodutos no designer. Para uma introdução guiada, complete o [tutorial.](tutorial-designer-automobile-price-train-score.md) 

### <a name="sample-pipeline"></a>Gasoduto de amostra

O gasoduto utilizado neste artigo é uma versão alterada da [Amostra 3: Previsão](samples-designer.md#classification)do rendimento . O pipeline utiliza o módulo [Dados de Importação](algorithm-module-reference/import-data.md) em vez do conjunto de dados da amostra para mostrar como treinar modelos usando os seus próprios dados.

![Screenshot que mostra o pipeline de amostra modificado com uma caixa realçando o módulo de dados de importação](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Criar um parâmetro de gasoduto

Crie parâmetros de gasoduto para definir variáveis dinâmicamente no tempo de execução. Para este exemplo, irá alterar o caminho dos dados de treino de um valor fixo para um parâmetro, para que possa retreinar o seu modelo em diferentes dados.

1. Selecione o módulo **dados de importação.**

    > [!NOTE]
    > Este exemplo utiliza o módulo Dados de Importação para aceder a dados numa loja de dados registada. No entanto, pode seguir passos semelhantes se utilizar padrões alternativos de acesso a dados.

1. No painel de detalhes do módulo, à direita da tela, selecione a sua fonte de dados.

1. Insira o caminho para os seus dados. Também pode selecionar o **caminho de Navegação** para navegar na sua árvore de arquivo. 

1. Mouseover the **Path** field, e selecione as elipses acima do campo **Caminho** que aparecem.

    ![Screenshot que mostra como criar um parâmetro de gasoduto](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. **Selecione Adicionar ao parâmetro do gasoduto**.

1. Forneça um nome de parâmetro e um valor predefinido.

   > [!NOTE]
   > Pode inspecionar e editar os parâmetros do gasoduto selecionando o ícone de engrenagem **Definições** ao lado do título da sua corrente de gasoduto. 

1. Selecione **Guardar**.

1. Submeta a execução do oleoduto.

## <a name="find-a-trained-model"></a>Encontre um modelo treinado

O designer guarda toda a saída do pipeline, incluindo modelos treinados, para a conta de armazenamento padrão do espaço de trabalho. Também pode aceder a modelos treinados diretamente no designer:

1. Espere que o oleoduto termine de funcionar.
1. Selecione o módulo **Modelo de Comboio.**
1. No painel de detalhes do módulo, à direita da tela, selecione **Saídas + troncos**.
1. Pode encontrar o seu modelo em **Outras saídas** juntamente com registos de execução.
1. Em alternativa, selecione o ícone de **saída Do Ver.** A partir daqui, pode seguir as instruções no diálogo para navegar diretamente na sua loja de dados. 

![Screenshot que mostra como descarregar o modelo treinado](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publicar um pipeline de formação

Publique um pipeline num ponto final de gasoduto para reutilizar facilmente os seus oleodutos no futuro. Um ponto final do gasoduto cria um ponto final REST para invocar o oleoduto no futuro. Neste exemplo, o seu ponto final de pipeline permite-lhe reutilizar o seu pipeline para retreinar um modelo em diferentes dados.

1. **Selecione Publicar** acima da tela do designer.
1. Selecione ou crie um ponto final de gasoduto.

   > [!NOTE]
   > Pode publicar vários oleodutos num único ponto final. Cada gasoduto num determinado ponto final recebe um número de versão, que pode especificar quando ligar para o ponto final do gasoduto.

1. Selecione **Publicar**.

## <a name="retrain-your-model"></a>Retrete o seu modelo

Agora que tem um pipeline de treino publicado, pode usá-lo para retreinar o seu modelo em novos dados. Pode submeter execuções a partir de um ponto final de pipeline a partir do espaço de trabalho do estúdio ou programáticamente.

### <a name="submit-runs-by-using-the-designer"></a>Submeter corre usando o designer

Utilize os seguintes passos para submeter um ponto final de gasoduto parametrizado do designer:

1. Vá à página **Endpoints** no seu espaço de trabalho de estúdio.
1. Selecione o separador de **pontos finais do Pipeline.** Em seguida, selecione o ponto final do gasoduto.
1. Selecione o separador **de gasodutos Publicados.** Em seguida, selecione a versão do pipeline que pretende executar.
1. Selecione **Submeter**.
1. Na caixa de diálogo de configuração, pode especificar os valores dos parâmetros para a execução. Para este exemplo, atualize o caminho de dados para treinar o seu modelo utilizando um conjunto de dados não americano.

![Screenshot que mostra como configurar um pipeline parametrizado executado no designer](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Submeter corre usando código

Pode encontrar o ponto final REST de um oleoduto publicado no painel de visão geral. Ao ligar para o ponto final, pode retreinar o oleoduto publicado.

Para fazer uma chamada REST, você precisa de um cabeçalho de autenticação tipo OAuth 2.0. Para obter informações sobre a instalação da autenticação no seu espaço de trabalho e fazer uma chamada REST parametrizada, consulte [Construir um pipeline azure machine learning para pontuação](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)de lotes .

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar um pipeline de treino parametrizado com o designer.

Para uma completa passagem de como pode implementar um modelo para fazer previsões, consulte o tutorial do [designer](tutorial-designer-automobile-price-train-score.md) para treinar e implementar um modelo de regressão.
