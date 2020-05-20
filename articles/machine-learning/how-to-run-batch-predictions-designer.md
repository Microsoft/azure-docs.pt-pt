---
title: Executar predições em lote com o Azure Machine Learning Designer (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a treinar um modelo e instale um pipeline de previsão de lote utilizando o designer. Implemente o gasoduto como um serviço web parametrizado, que pode ser acionado a partir de qualquer biblioteca HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019, designer
ms.openlocfilehash: 5079953994b74378a54a316c288c77d13c3af782
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653671"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Executar predições em lote com o Azure Machine Learning Designer (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, aprende-se a usar o designer para criar um pipeline de previsão de lote. A previsão do lote permite-lhe pontuar continuamente grandes conjuntos de dados a pedido usando um serviço web que pode ser desencadeado a partir de qualquer biblioteca HTTP.

Neste como-fazer, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar e publicar um gasoduto de inferência de lote
> * Consumir um ponto final do oleoduto
> * Gerir versões de ponto final

Para aprender a configurar os serviços de pontuação de lotes utilizando o SDK, consulte o [acompanhamento](how-to-run-batch-predictions.md).

## <a name="prerequisites"></a>Pré-requisitos

Este como assumir que já tem um oleoduto de treino. Para uma introdução guiada ao designer, complete a [parte um do tutorial do designer.](tutorial-designer-automobile-price-train-score.md) 

## <a name="create-a-batch-inference-pipeline"></a>Criar um gasoduto de inferência de lote

O seu gasoduto de treino deve ser executado pelo menos uma vez para poder criar um gasoduto de inferência.

1. Vá ao separador **Designer** no seu espaço de trabalho.

1. Selecione o gasoduto de treino que treina o modelo que pretende utilizar para fazer a previsão.

1. **Submeta** o oleoduto.

    ![Submeter o gasoduto](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Agora que o gasoduto de treino foi executado, pode criar um oleoduto de inferência de lote.

1. Ao lado de **Submeter,** selecione o novo dropdown Criar o gasoduto de **inferência**.

1. Selecione o gasoduto de **inferência**do lote .

    ![Criar gasoduto de inferência de lote](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
O resultado é um gasoduto de inferência de lote padrão. 

### <a name="add-a-pipeline-parameter"></a>Adicione um parâmetro de gasoduto

Para criar previsões sobre novos dados, pode ligar manualmente um conjunto de dados diferente nesta visão de projeto de pipeline ou criar um parâmetro para o seu conjunto de dados. Os parâmetros permitem alterar o comportamento do processo de inferência do lote no tempo de execução.

Nesta secção, cria-se um parâmetro de dataset para especificar um conjunto de dados diferente para fazer previsões.

1. Selecione o módulo de conjunto de dados.

1. Um painel aparecerá à direita da tela. Na parte inferior do painel, selecione **set como parâmetro**de gasoduto .
   
    Introduza um nome para o parâmetro ou aceite o valor predefinido.

## <a name="publish-your-batch-inferencing-pipeline"></a>Publique o seu pipeline de inferência de lote

Agora estás pronto para lançar o oleoduto de inferência. Isto irá implantar o gasoduto e disponibilizá-lo para outros utilizarem.

1. Selecione o botão **Publicar**.

1. No diálogo que aparece, expanda a queda para **PipelineEndpoint**, e selecione **New PipelineEndpoint**.

1. Forneça um nome final e descrição opcional.

    Perto da parte inferior do diálogo, pode ver o parâmetro configurado com um valor predefinido do ID do conjunto de dados utilizado durante o treino.

1. Selecione **Publicar**.

![Publicar um oleoduto](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Consumir um ponto final

Agora, tem um oleoduto publicado com um parâmetro de conjunto de dados. O gasoduto utilizará o modelo treinado criado no pipeline de treino para marcar o conjunto de dados que fornece como parâmetro.

### <a name="submit-a-pipeline-run"></a>Submeter uma execução de gasoduto 

Nesta secção, irá configurar uma execução manual de gasodutos e alterar o parâmetro do gasoduto para obter novos dados. 

1. Depois de concluída a colocação, vá à secção **Pontos Finais.**

1. Selecione **pontos finais do pipeline**.

1. Selecione o nome do ponto final que criou.

![Ligação endpoint](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Selecione **Os oleodutos Publicados**.

    Este ecrã mostra todos os oleodutos publicados sob este ponto final.

1. Selecione o oleoduto que publicou.

    A página de detalhes do pipeline mostra-lhe um histórico detalhado de execução e informações de cordas de ligação para o seu pipeline. 
    
1. Selecione **Submeter** para criar uma execução manual do gasoduto.

    ![Detalhes do gasoduto](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Mude o parâmetro para utilizar um conjunto de dados diferente.
    
1. Selecione **Submeter** para executar o gasoduto.

### <a name="use-the-rest-endpoint"></a>Use o ponto final REST

Pode encontrar informações sobre como consumir pontos finais de gasoduto e oleoduto publicado na secção **Endpoints.**

Pode encontrar o ponto final REST de um ponto final de gasoduto no painel de visão geral de execução. Ao ligar para o ponto final, está a consumir o seu pipeline publicado por defeito.

Também pode consumir um pipeline publicado na página **de pipelines Publicados.** Selecione um pipeline publicado e encontre o ponto final do REST. 

![Detalhes do ponto final do descanso](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Para fazer uma chamada REST, você precisará de um cabeçalho de autenticação tipo OAuth 2.0. Consulte a seguinte [secção tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) para obter mais detalhes sobre a instalação da autenticação no seu espaço de trabalho e fazer uma chamada REST parametrizada.

## <a name="versioning-endpoints"></a>Pontos finais de versão

O designer atribui uma versão a cada pipeline subsequente que publica para um ponto final. Pode especificar a versão do gasoduto que pretende executar como parâmetro na sua chamada REST. Se não especificar um número de versão, o designer utilizará o pipeline predefinido.

Ao publicar um pipeline, pode optar por torná-lo o novo pipeline padrão para esse ponto final.

![Definir o gasoduto padrão](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Também pode definir um novo pipeline predefinido no separador **de gasodutos Publicado do** seu ponto final.

![Definir o gasoduto padrão](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Passos seguintes

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) do designer para treinar e implementar um modelo de regressão.
''