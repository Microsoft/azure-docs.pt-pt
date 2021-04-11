---
title: Executar previsões de lote usando O designer de aprendizagem de máquinas Azure
titleSuffix: Azure Machine Learning
description: Aprenda a criar um pipeline de previsão de lote. Implemente o gasoduto como um serviço web parametrizado e desencadeie-o a partir de qualquer biblioteca HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 02/05/2021
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 34d0d31296214355b85c52e4564e9bf6658b2005
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962939"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Executar previsões de lote usando O designer de aprendizagem de máquinas Azure


Neste artigo, aprende-se a usar o designer para criar um pipeline de previsão de lote. A previsão do lote permite-lhe pontuar continuamente grandes conjuntos de dados a pedido usando um serviço web que pode ser acionado a partir de qualquer biblioteca HTTP.

Neste como fazer, aprende-se a fazer as seguintes tarefas:

> [!div class="checklist"]
> * Criar e publicar um pipeline de inferência de lote
> * Consumir um ponto final de gasoduto
> * Gerir versões de ponto final

Para aprender a configurar serviços de pontuação de lotes utilizando o SDK, consulte o [como acompanhar](./tutorial-pipeline-batch-scoring-classification.md).

## <a name="prerequisites"></a>Pré-requisitos

Este como assumir que já tem um oleoduto de treino. Para uma introdução guiada ao designer, complete [a primeira parte do tutorial do designer.](tutorial-designer-automobile-price-train-score.md) 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

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

    > [!div class="mx-imgBorder"]
    > ![Conjunto de dados como parâmetro de pipeline](./media/how-to-run-batch-predictions-designer/set-dataset-as-pipeline-parameter.png)

## <a name="publish-your-batch-inference-pipeline"></a>Publique o seu pipeline de inferência de lote

Agora está pronto para implantar o oleoduto de inferência. Isto irá implantar o gasoduto e disponibilizá-lo para que outros utilizem.

1. Selecione o botão **Publicar**.

1. No diálogo que aparece, expanda a entrega para **PipelineEndpoint** e selecione **New PipelineEndpoint**.

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

Também pode consumir um oleoduto publicado na página de **gasodutos Publicados.** Selecione um pipeline publicado e poderá encontrar o ponto final REST do mesmo no painel **de visão geral** do gasoduto publicado à direita do gráfico. 

Para fazer uma chamada REST, necessitará de um cabeçalho de autenticação do tipo portador OAuth 2.0. Consulte a [seguinte secção tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) para obter mais detalhes sobre a configuração da autenticação no seu espaço de trabalho e fazer uma chamada de REST parametrizada.

## <a name="versioning-endpoints"></a>Pontos finais de versão

O designer atribui uma versão a cada oleoduto subsequente que publica para um ponto final. Pode especificar a versão do pipeline que pretende executar como parâmetro na sua chamada REST. Se não especificar um número de versão, o designer utilizará o pipeline predefinido.

Ao publicar um oleoduto, pode optar por torná-lo o novo pipeline padrão para esse ponto final.

![Definir o gasoduto predefinido](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Também pode definir um novo pipeline padrão no **separador de gasodutos Publicado do** seu ponto final.

![Desafine o gasoduto predefinido na página de pipeline publicada](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="limitations"></a>Limitações

Se fizer algumas modificações no seu gasoduto de treino, deverá reenucam o gasoduto de treino, **atualizar**  o gasoduto de inferência e voltar a executar o gasoduto de inferência.

Note que apenas os modelos serão atualizados no pipeline de inferência, enquanto a transformação de dados não será atualizada.

Para utilizar a transformação atualizada no pipeline de inferência, é necessário registar a saída de transformação do módulo de transformação como conjunto de dados.

![Screenshot mostrando como registar conjunto de dados de transformação](./media/how-to-run-batch-predictions-designer/register-transformation-dataset.png)

Em seguida, substitua manualmente o módulo **TD** no pipeline de inferência pelo conjunto de dados registado.

![Screenshot mostrando como substituir módulo de transformação](./media/how-to-run-batch-predictions-designer/replace-td-module-batch-inference-pipeline.png)

Em seguida, pode submeter o pipeline de inferência com o modelo e transformação atualizados, e publicar.

## <a name="next-steps"></a>Passos seguintes

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) do designer para treinar e implementar um modelo de regressão.

Para como publicar e executar um oleoduto publicado utilizando a SDK, consulte [este artigo](how-to-deploy-pipelines.md).
