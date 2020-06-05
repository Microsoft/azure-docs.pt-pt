---
title: Executar predições em lote com o Azure Machine Learning Designer (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a treinar um modelo e crie um pipeline de previsão de lote utilizando o designer. Implemente o oleoduto como um serviço web parametrizado, que pode ser acionado a partir de qualquer biblioteca HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019, designer
ms.openlocfilehash: a464ab001eec877ffc6dc0ab5e33e82493c226ff
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84429941"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Executar predições em lote com o Azure Machine Learning Designer (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, aprende-se a usar o designer para criar um pipeline de previsão de lote. A previsão do lote permite-lhe pontuar continuamente grandes conjuntos de dados a pedido usando um serviço web que pode ser acionado a partir de qualquer biblioteca HTTP.

Neste como fazer, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar e publicar um pipeline de inferência de lote
> * Consumir um ponto final de gasoduto
> * Gerir versões de ponto final

Para aprender a configurar serviços de pontuação de lotes utilizando o SDK, consulte o [como acompanhar](how-to-run-batch-predictions.md).

## <a name="prerequisites"></a>Pré-requisitos

Este como assumir que já tem um oleoduto de treino. Para uma introdução guiada ao designer, complete [a primeira parte do tutorial do designer.](tutorial-designer-automobile-price-train-score.md) 

## <a name="create-a-batch-inference-pipeline"></a>Criar um pipeline de inferência de lote

O seu gasoduto de treino deve ser executado pelo menos uma vez para poder criar um gasoduto de inferenção.

1. Vá ao **separador Designer** no seu espaço de trabalho.

1. Selecione o gasoduto de treino que treina o modelo que pretende utilizar para fazer a previsão.

1. **Envie** o oleoduto.

    ![Enviar o oleoduto](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Agora que o gasoduto de treino foi executado, pode criar um pipeline de inferência de lote.

1. Ao lado de **Enviar**, selecione o novo pipeline de **inferências Dedesemesta .**

1. Selecione **pipeline de inferência do lote**.

    ![Criar pipeline de inferência de lote](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
O resultado é um pipeline de inferência de lote predefinido. 

### <a name="add-a-pipeline-parameter"></a>Adicione um parâmetro de pipeline

Para criar previsões sobre novos dados, pode ligar manualmente um conjunto de dados diferente nesta visão de projeto de pipeline ou criar um parâmetro para o seu conjunto de dados. Os parâmetros permitem alterar o comportamento do processo de inferenculação do lote em tempo de execução.

Nesta secção, cria-se um parâmetro de conjunto de dados para especificar um conjunto de dados diferente para fazer previsões.

1. Selecione o módulo de conjunto de dados.

1. Um painel aparecerá à direita da tela. Na parte inferior do painel, selecione **Definir como parâmetro de tubagem**.
   
    Introduza um nome para o parâmetro ou aceite o valor predefinido.

## <a name="publish-your-batch-inferencing-pipeline"></a>Publique o seu pipeline de inferenculação de lote

Agora estás pronto para implantar o oleoduto de inferencing. Isto irá implantar o gasoduto e disponibilizá-lo para que outros utilizem.

1. Selecione o botão **Publicar**.

1. No diálogo que aparece, expanda a entrega para **PipelineEndpoint**e selecione **New PipelineEndpoint**.

1. Forneça um nome final e descrição opcional.

    Perto da parte inferior do diálogo, pode ver o parâmetro configurado com um valor predefinido do ID do conjunto de dados utilizado durante o treino.

1. Selecione **Publicar**.

![Publicar um oleoduto](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Consumir um ponto final

Agora, tem um oleoduto publicado com um parâmetro de conjunto de dados. O pipeline utilizará o modelo treinado criado no pipeline de treino para marcar o conjunto de dados que fornece como parâmetro.

### <a name="submit-a-pipeline-run"></a>Submeter uma corrida de gasoduto 

Nesta secção, irá configurar uma corrida manual de gasodutos e alterar o parâmetro do gasoduto para obter novos dados. 

1. Depois de concluída a colocação, aceda à secção **Endpoints.**

1. Selecione **pontos finais do Pipeline**.

1. Selecione o nome do ponto final que criou.

![Ligação de ponto final](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Selecione **gasodutos publicados**.

    Este ecrã mostra todos os oleodutos publicados sob este ponto final.

1. Selecione o pipeline que publicou.

    A página de detalhes do pipeline mostra-lhe um histórico detalhado de execução e informações de cadeia de ligação para o seu pipeline. 
    
1. **Selecione Enviar** para criar uma execução manual do oleoduto.

    ![Detalhes do gasoduto](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Altere o parâmetro para utilizar um conjunto de dados diferente.
    
1. **Selecione Enviar** para executar o gasoduto.

### <a name="use-the-rest-endpoint"></a>Use o ponto final REST

Pode encontrar informações sobre como consumir pontos finais de gasodutos e gasodutos publicados na secção **Endpoints.**

Pode encontrar o ponto final REST de um ponto final do gasoduto no painel de visão geral de execução. Ao ligar para o ponto final, está a consumir o seu oleoduto publicado por defeito.

Também pode consumir um oleoduto publicado na página de **gasodutos Publicados.** Selecione um oleoduto publicado e encontre o ponto final REST do mesmo. 

![Detalhes do ponto final de descanso](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Para fazer uma chamada REST, necessitará de um cabeçalho de autenticação do tipo portador OAuth 2.0. Consulte a [seguinte secção tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) para obter mais detalhes sobre a configuração da autenticação no seu espaço de trabalho e fazer uma chamada de REST parametrizada.

## <a name="versioning-endpoints"></a>Pontos finais de versão

O designer atribui uma versão a cada oleoduto subsequente que publica para um ponto final. Pode especificar a versão do pipeline que pretende executar como parâmetro na sua chamada REST. Se não especificar um número de versão, o designer utilizará o pipeline predefinido.

Ao publicar um oleoduto, pode optar por torná-lo o novo pipeline padrão para esse ponto final.

![Definir o gasoduto predefinido](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Também pode definir um novo pipeline padrão no **separador de gasodutos Publicado do** seu ponto final.

![Definir o gasoduto predefinido](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Passos seguintes

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) do designer para treinar e implementar um modelo de regressão.
''