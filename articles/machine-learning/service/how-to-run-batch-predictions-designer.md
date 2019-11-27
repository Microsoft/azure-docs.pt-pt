---
title: Executar previsões de lote usando Azure Machine Learning designer (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como treinar um modelo e configurar um pipeline de previsão do lote usando o designer. Implante o pipeline como um serviço Web com parâmetros, que pode ser disparado de qualquer biblioteca HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: a44ac821271cc07a790c380287391f41650ced19
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276745"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Executar previsões de lote usando Azure Machine Learning designer
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste "como", você aprende a usar o designer para treinar um modelo e configurar um pipeline de previsão do lote e um serviço Web. A previsão de lote permite a pontuação contínua e sob demanda de modelos treinados em conjuntos de dados grandes, opcionalmente configurada como um serviço Web que pode ser disparado de qualquer biblioteca HTTP. 

Para configurar os serviços de Pontuação de lote usando o SDK, consulte o artigo [como](how-to-run-batch-predictions.md).

Neste "como", você aprende as seguintes tarefas:

> [!div class="checklist"]
> * Criar um experimento de ML básico em um pipeline
> * Criar um pipeline de inferência de lote parametrizado
> * Gerenciar e executar pipelines manualmente ou de um ponto de extremidade REST

## <a name="prerequisites"></a>Pré-requisitos

1. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

1. Crie um [espaço de trabalho](tutorial-1st-experiment-sdk-setup.md).

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com/).

Este "como" pressupõe o conhecimento básico da criação de um pipeline simples no designer. Para obter uma introdução guiada ao designer, conclua o [tutorial](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-pipeline"></a>Criar um pipeline

Para criar um pipeline de inferência de lote, primeiro você precisa de um experimento de aprendizado de máquina. Para criar um, navegue até a guia **Designer** em seu espaço de trabalho e crie um novo pipeline selecionando a opção de **módulos predefinidos fácil de usar** .

![Página inicial do designer](media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

Veja a seguir um modelo de aprendizado de máquina simples para fins de demonstração. Os dados são um DataSet registrado criado a partir dos dados de diabetes do Azure Open DataSets. Consulte a [seção de instruções](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) para registrar conjuntos de registros de conjuntos de de Azure abertos. Os dados são divididos em conjuntos de treinamento e validação, e uma árvore de decisão aumentada é treinada e pontuada. O pipeline deve ser executado pelo menos uma vez para poder criar um pipeline inferência. Clique no botão **executar** para executar o pipeline.

![Criar experimento simples](media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Criar um pipeline de inferência de lote

Agora que o pipeline foi executado, há uma nova opção disponível ao lado de **executar** e **publicar** chamado **pipeline de inferência de criação**. Clique na lista suspensa e selecione **pipeline de inferência de lote**.

![Criar pipeline de inferência de lote](media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

O resultado é um pipeline de inferência de lote padrão. Isso inclui um nó para a configuração do experimento do seu pipeline original, um nó para dados brutos para pontuação e um nó para pontuar os dados brutos em relação ao seu pipeline original.

![Pipeline de inferência de lote padrão](media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

Você pode adicionar outros nós para alterar o comportamento do processo de inferência do lote. Neste exemplo, você adiciona um nó para amostragem aleatória dos dados de entrada antes da pontuação. Crie uma **partição e** um nó de exemplo e coloque-o entre os dados brutos e os nós de pontuação. Em seguida, clique no nó **partição e exemplo** para obter acesso às configurações e aos parâmetros.

![Novo nó](media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

A *taxa de parâmetro de amostragem* controla a porcentagem do conjunto de dados original do qual obter um exemplo aleatório. Esse é um parâmetro que será útil para ser ajustado com frequência, portanto, você o habilita como um parâmetro de pipeline. Os parâmetros de pipeline podem ser alterados em tempo de execução e podem ser especificados em um objeto de carga ao executar novamente o pipeline de um ponto de extremidade REST. 

Para habilitar esse campo como um parâmetro de pipeline, clique nas reticências acima do campo e, em seguida, clique em **Adicionar ao parâmetro de pipeline**. 

![Configurações de exemplo](media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

Em seguida, dê ao parâmetro um nome e um valor padrão. O nome será usado para identificar o parâmetro e especificá-lo em uma chamada REST.

![Parâmetro do pipeline](media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Implantar pipeline inferência do lote

Agora você está pronto para implantar o pipeline. Clique no botão **implantar** , que abre a interface para configurar um ponto de extremidade. Clique na lista suspensa e selecione **novo PipelineEndpoint**.

![Implantação de pipeline](media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

Dê ao ponto de extremidade um nome e uma descrição opcional. Próximo à parte inferior, você verá o parâmetro `sample-rate` configurado com um valor padrão de 0,8. Quando estiver pronto, clique em **implantar**.

![Ponto de extremidade de instalação](media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Gerir pontos finais 

Depois que a implantação for concluída, vá para a guia **pontos** de extremidade e clique no nome do ponto que você acabou de criar.

![Link do ponto de extremidade](media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

Esta tela mostra todos os pipelines publicados sob o ponto de extremidade específico. Clique em seu pipeline do inferência.

![Pipeline de inferência](media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

A página detalhes do pipeline mostra o histórico de execução detalhado e informações de cadeia de conexão para seu pipeline. Clique no botão **executar** para criar uma execução manual do pipeline.

![Detalhes do pipeline](media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

Na instalação de executar, você pode fornecer uma descrição para a execução e alterar o valor de qualquer parâmetro de pipeline. Desta vez, execute novamente o pipeline inferência com uma taxa de amostragem de 0,9. Clique em **executar** para executar o pipeline.

![Execução de pipeline](media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

A guia **consumir** contém o ponto de extremidade REST para executar novamente seu pipeline. Para fazer uma chamada REST, você precisará de um cabeçalho de autenticação do tipo portador OAuth 2,0. Consulte a seguinte [seção do tutorial](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) para obter mais detalhes sobre como configurar a autenticação para seu espaço de trabalho e fazer uma chamada REST com parâmetros.

## <a name="next-steps"></a>Passos Seguintes

Siga o [tutorial](tutorial-designer-automobile-price-train-score.md) do designer para treinar e implantar um modelo de regressão.
