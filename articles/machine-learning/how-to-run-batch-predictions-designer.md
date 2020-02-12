---
title: Executar previsões de lote utilizando o designer de machine learning Azure (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a treinar um modelo e instale um pipeline de previsão de lote utilizando o designer. Implemente o gasoduto como um serviço web parametrizado, que pode ser acionado a partir de qualquer biblioteca HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 01/13/2020
ms.custom: Ignite2019
ms.openlocfilehash: 95a3c1b892cacd802f359fdc03de74fa60a1e118
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138115"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Executar previsões de lote usando azure machine learning designer
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a usar o designer para criar um pipeline de previsão do lote. A previsão de lote permite pontuar continuamente conjuntos de grandes volumes sob demanda usando um serviço Web que pode ser disparado de qualquer biblioteca HTTP.

Neste "como", você aprenderá a executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar e publicar um pipeline de inferência de lote
> * Consumir um ponto de extremidade de pipeline
> * Gerenciar versões de ponto de extremidade

Para aprender a configurar os serviços de pontuação de lotes utilizando o SDK, consulte o [acompanhamento](how-to-run-batch-predictions.md).

## <a name="prerequisites"></a>Pré-requisitos

Este "como" pressupõe que você já tenha um pipeline de treinamento. Para uma introdução guiada ao designer, complete a [parte um do tutorial do designer.](tutorial-designer-automobile-price-train-score.md) 

## <a name="create-a-batch-inference-pipeline"></a>Criar um gasoduto de inferência de lote

Seu pipeline de treinamento deve ser executado pelo menos uma vez para poder criar um pipeline inferência.

1. Vá ao separador **Designer** no seu espaço de trabalho.

1. Selecione o gasoduto de treino que treina o modelo que pretende utilizar para fazer a previsão.

1. **Corre** o oleoduto.

    ![Executar o pipeline](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Agora que o pipeline de treinamento foi executado, você pode criar um pipeline de inferência de lote.

1. Ao lado de **Run**, selecione o novo dropdown Criar o gasoduto de **inferência**.

1. Selecione o gasoduto de **inferência**do lote .

    ![Criar gasoduto de inferência de lote](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
O resultado é um gasoduto de inferência de lote padrão. 

### <a name="add-a-pipeline-parameter"></a>Adicionar um parâmetro de pipeline

Para criar previsões sobre novos dados, você pode conectar-se manualmente a um DataSet diferente nesta exibição de rascunho do pipeline ou criar um parâmetro para seu conjunto de dados. Os parâmetros permitem alterar o comportamento do processo de inferência do lote em tempo de execução.

Nesta seção, você cria um parâmetro de conjunto de um para especificar um conjunto de diferentes para fazer previsões.

1. Selecione o módulo DataSet.

1. Um painel será exibido à direita da tela. Na parte inferior do painel, selecione **set como parâmetro**de gasoduto .
   
    Insira um nome para o parâmetro ou aceite o valor padrão.

## <a name="publish-your-batch-inferencing-pipeline"></a>Publicar o pipeline inferência do lote

Agora você está pronto para implantar o pipeline do inferência. Isso implantará o pipeline e o tornará disponível para outras pessoas usarem.

1. Selecione o botão **Publicar**.

1. No diálogo que aparece, expanda a queda para **PipelineEndpoint**, e selecione **New PipelineEndpoint**.

1. Forneça um nome de ponto de extremidade e uma descrição opcional.

    Próximo à parte inferior da caixa de diálogo, você pode ver o parâmetro configurado com um valor padrão da ID do conjunto de resultados usado durante o treinamento.

1. Selecione **Publicar**.

![Publicar um pipeline](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Consumir um ponto de extremidade

Agora, você tem um pipeline publicado com um parâmetro DataSet. O pipeline usará o modelo treinado criado no pipeline de treinamento para pontuar o conjunto de um que você fornece como um parâmetro.

### <a name="submit-a-pipeline-run"></a>Enviar uma execução de pipeline 

Nesta seção, você irá configurar uma execução de pipeline manual e alterar o parâmetro de pipeline para pontuar novos dados. 

1. Depois de concluída a colocação, vá à secção **Pontos Finais.**

1. Selecione **pontos finais do pipeline**.

1. Selecione o nome do ponto de extremidade que você criou.

![Ligação endpoint](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Selecione **Os oleodutos Publicados**.

    Esta tela mostra todos os pipelines publicados publicados nesse ponto de extremidade.

1. Selecione o pipeline que você publicou.

    A página detalhes do pipeline mostra um histórico de execução detalhado e informações de cadeia de conexão para seu pipeline. 
    
1. Selecione **Executar** para criar uma execução manual do gasoduto.

    ![Detalhes do gasoduto](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Altere o parâmetro para usar um conjunto de um diferente.
    
1. Selecione **Executar** para executar o gasoduto.

### <a name="use-the-rest-endpoint"></a>Usar o ponto de extremidade REST

Pode encontrar informações sobre como consumir pontos finais de gasoduto e oleoduto publicado na secção **Endpoints.**

Você pode encontrar o ponto de extremidade REST de um ponto de extremidade de pipeline no painel de visão geral de execução. Ao chamar o ponto de extremidade, você está consumindo seu pipeline publicado padrão.

Também pode consumir um pipeline publicado na página **de pipelines Publicados.** Selecione um pipeline publicado e localize o ponto de extremidade REST. 

![Detalhes do ponto de extremidade REST](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Para fazer uma chamada REST, você precisará de um cabeçalho de autenticação do tipo portador OAuth 2,0. Consulte a seguinte [secção tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) para obter mais detalhes sobre a instalação da autenticação no seu espaço de trabalho e fazer uma chamada REST parametrizada.

## <a name="versioning-endpoints"></a>Pontos de extremidade de controle de versão

O Designer atribui uma versão a cada pipeline subsequente que você publica em um ponto de extremidade. Você pode especificar a versão do pipeline que deseja executar como um parâmetro em sua chamada REST. Se você não especificar um número de versão, o designer usará o pipeline padrão.

Ao publicar um pipeline, você pode optar por torná-lo o novo pipeline padrão para esse ponto de extremidade.

![Definir pipeline padrão](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Também pode definir um novo pipeline predefinido no separador **de gasodutos Publicado do** seu ponto final.

![Definir pipeline padrão](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Passos seguintes

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) do designer para treinar e implementar um modelo de regressão.
