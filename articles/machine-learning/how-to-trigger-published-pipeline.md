---
title: Pipeline ML de gatilho para novos dados
titleSuffix: Azure Machine Learning
description: Aprenda a desencadear o funcionamento de um oleoduto ML utilizando aplicações lógicas Azure.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4
ms.openlocfilehash: 2e3544bee5158a855467f8cb142f176df2187ef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318301"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Desencadear uma corrida de um pipeline de Machine Learning a partir de uma App Lógica

Desencadeie o funcionamento do seu Pipeline de Aprendizagem de Máquinas Azure quando surgirem novos dados. Por exemplo, é melhor ativar o pipeline para treinar um novo modelo quando surgirem novos dados na conta de armazenamento do blob. Configurar o gatilho com [apps Azure Logic](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* O ponto final DO REST para um pipeline de Machine Learning publicado. [Crie e publique o seu pipeline.](how-to-create-your-first-pipeline.md) Em seguida, encontre o ponto final REST da sua Linha De Risco Publicada utilizando o ID do gasoduto:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Armazenamento de bolhas Azure](../storage/blobs/storage-blobs-overview.md) para armazenar os seus dados.
* [Uma loja de dados](how-to-access-data.md) no seu espaço de trabalho que contém os detalhes da sua conta de armazenamento de bolhas.

## <a name="create-a-logic-app"></a>Criar uma aplicação do Logic Apps

Agora crie uma instância [da Azure Logic App.](../logic-apps/logic-apps-overview.md) Se desejar, [utilize um ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e crie uma chave gerida pelo [cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) para utilização pela sua App Lógica.

Uma vez que a sua App Lógica tenha sido aprovisionada, utilize estes passos para configurar um gatilho para o seu pipeline:

1. [Crie uma identidade gerida atribuída ao sistema](../logic-apps/create-managed-service-identity.md) para dar à app acesso ao seu espaço de trabalho de aprendizagem de máquinas Azure.

1. Navegue para a vista do Designer de Aplicativos Lógicas lógica e selecione o modelo de App Blank Logic. 
    > [!div class="mx-imgBorder"]
    > ![Modelo em branco](media/how-to-trigger-published-pipeline/blank-template.png)

1. No Designer, procure **por bolhas.** Selecione o gatilho **Quando uma bolha é adicionada ou modificada (apenas propriedades)** e adicione este gatilho à sua App Lógica.
    > [!div class="mx-imgBorder"]
    > ![Adicionar acionador](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Preencha as informações de ligação para a conta de armazenamento Blob que deseja monitorizar para adições ou modificações blob. Selecione o Recipiente para monitorizar. 
 
    Escolha o **Intervalo** e **a Frequência** para fazer sondagem para obter atualizações que funcionem para si.  

    > [!NOTE]
    > Este gatilho monitorizará o recipiente selecionado, mas não monitorizará as sub-dobradeiras.

1. Adicione uma ação HTTP que será executada quando for detetada uma bolha nova ou modificada. Selecione **+ Novo Passo,** em seguida, procure e selecione a ação HTTP.

  > [!div class="mx-imgBorder"]
  > ![Pesquisa rumo à ação HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Utilize as seguintes definições para configurar a sua ação:

  | Definição | Valor | 
  |---|---|
  | Ação HTTP | POST |
  | URI |o ponto final para o oleoduto publicado que encontrou como um [Pré-requisito](#prerequisites) |
  | Modo de autenticação | Identidade Gerida |

1. Desagram o seu horário para definir o valor de quaisquer [PipelineParameters dataPath](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) que possa ter:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    Utilize o `DataStoreName` seu espaço de trabalho como [pré-requisito.](#prerequisites)
     
    > [!div class="mx-imgBorder"]
    > ![Definições de HTTP](media/how-to-trigger-published-pipeline/http-settings.png)

1. **Selecione Save** e a sua agenda está agora pronta.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte:

> [!div class="nextstepaction"]
> [Utilize gasodutos de aprendizagem de máquinas Azure para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md)

* Saiba mais sobre [os oleodutos](concept-ml-pipelines.md)
* Saiba mais sobre [a exploração da Azure Machine Learning com o Jupyter](samples-notebooks.md)

