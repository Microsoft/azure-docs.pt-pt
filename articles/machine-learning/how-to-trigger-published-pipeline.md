---
title: Desencadear o gasoduto ML para novos dados
titleSuffix: Azure Machine Learning
description: Aprenda a desencadear a execução de um oleoduto ML utilizando aplicações da Lógica Azure.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: contperfq4
ms.openlocfilehash: 32b3e153a98ca7de91e0dd05258414780b39dec4
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857973"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Desencadear uma série de um oleoduto de Machine Learning a partir de uma Aplicação Lógica

Desencadeie a execução do seu Pipeline de Aprendizagem automática Azure quando surgirem novos dados. Por exemplo, pode querer acionar o oleoduto para treinar um novo modelo quando novos dados aparecerem na conta de armazenamento blob. Configurar o gatilho com [aplicações lógicas azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md).

* O ponto final do REST para um oleoduto de Machine Learning publicado. [Crie e publique o seu pipeline.](how-to-create-your-first-pipeline.md) Em seguida, encontre o ponto final REST do seu PublishedPipeline utilizando o ID do pipeline:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Armazenamento de blob azure](../storage/blobs/storage-blobs-overview.md) para armazenar os seus dados.
* [Uma loja](how-to-access-data.md) de dados no seu espaço de trabalho que contém os detalhes da sua conta de armazenamento blob.

## <a name="create-a-logic-app"></a>Criar uma Aplicação Lógica

Agora crie uma instância de [App Azure Logic.](../logic-apps/logic-apps-overview.md) Se desejar, utilize um ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) e [instale uma chave gerida pelo cliente](../logic-apps/customer-managed-keys-integration-service-environment.md) para utilização pela sua App Lógica.

Uma vez que a sua Aplicação Lógica tenha sido disponibilizada, utilize estes passos para configurar um gatilho para o seu pipeline:

1. [Crie uma identidade gerida atribuída](../logic-apps/create-managed-service-identity.md) ao sistema para dar acesso à aplicação ao seu espaço de trabalho de aprendizagem automática Azure.

1. Navegue para a visão do Designer de Aplicações Lógicas e selecione o modelo de aplicação da lógica em branco. 
    > [!div class="mx-imgBorder"]
    > ![Modelo em branco](media/how-to-trigger-published-pipeline/blank-template.png)

1. No Designer, procure **blob.** Selecione o gatilho **Quando uma bolha é adicionada ou modificada (apenas propriedades)** e adicione este gatilho à sua Aplicação Lógica.
    > [!div class="mx-imgBorder"]
    > ![Adicionar acionador](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Preencha as informações de ligação para a conta de armazenamento Blob que pretende monitorizar para adições ou modificações blob. Selecione o recipiente para monitorizar. 
 
    Escolha o **Intervalo** e **A Frequência** para fazer sondagens para atualizações que funcionem para si.  

    > [!NOTE]
    > Este gatilho monitorizará o recipiente selecionado, mas não monitorizará as subpastas.

1. Adicione uma ação HTTP que será executada quando for detetada uma bolha nova ou modificada. Selecione **+ New Step,** em seguida, procure e selecione a ação HTTP.

  > [!div class="mx-imgBorder"]
  > ![Pesquisa de ação HTTP](media/how-to-trigger-published-pipeline/search-http.png)

  Utilize as seguintes definições para configurar a sua ação:

  | Definição | Valor | 
  |---|---|
  | Ação HTTP | POST |
  | URI |o ponto final para o oleoduto publicado que encontrou como [pré-requisito](#prerequisites) |
  | Modo de autenticação | Identidade Gerida |

1. Configurar a sua programação para definir o valor de quaisquer Parâmetros de [PipelineParação DataPath](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) que possa ter:

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

    Use `DataStoreName` o que adicionou ao seu espaço de trabalho como [pré-requisito](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > ![Definições http](media/how-to-trigger-published-pipeline/http-settings.png)

1. Selecione **Guardar** e o seu horário está agora pronto.
