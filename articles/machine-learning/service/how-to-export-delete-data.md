---
title: Exportar ou excluir dados do espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como exportar ou excluir seu espaço de trabalho com o Azure Machine Learning Studio, CLI, SDK e APIs REST autenticadas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: d653d1cd719ac59bf6c98bee588c41858052bc61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489837"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou excluir seus dados do espaço de trabalho do serviço Machine Learning 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

No Azure Machine Learning, você pode exportar ou excluir os dados do espaço de trabalho com a API REST autenticada. Este artigo explica como.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controlar os dados do espaço de trabalho
Os dados no produto armazenados por Azure Machine Learning estão disponíveis para exportação e exclusão por meio do Azure Machine Learning Studio, CLI, SDK e APIs REST autenticadas. Os dados de telemetria podem ser acessados por meio do portal de privacidade do Azure. 

Em Azure Machine Learning, os dados pessoais consistem em informações do usuário em documentos de histórico de execução e registros de telemetria de algumas interações de usuário com o serviço.

## <a name="delete-workspace-data-with-the-rest-api"></a>Excluir dados do espaço de trabalho com a API REST 

Para excluir dados, as seguintes chamadas à API podem ser feitas com o verbo HTTP DELETE. Eles são autorizados por ter um cabeçalho `Authorization: Bearer <arm-token>` na solicitação, em que `<arm-token>` é o token de acesso do AAD para o ponto de extremidade `https://management.core.windows.net/`.  

Para saber como obter esse token e chamar os pontos de extremidade do Azure, consulte a [documentação da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).  

Nos exemplos a seguir, substitua o texto em {} pelos nomes de instância que determinam o recurso associado.

### <a name="delete-an-entire-workspace"></a>Excluir um espaço de trabalho inteiro

Use essa chamada para excluir um espaço de trabalho inteiro.  
> [!WARNING]
> Todas as informações serão excluídas e o espaço de trabalho não será mais utilizável.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Excluir modelos

Use esta chamada para obter uma lista de modelos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Modelos individuais podem ser excluídos com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Excluir ativos

Use esta chamada para obter uma lista de ativos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Ativos individuais podem ser excluídos com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Excluir imagens

Use esta chamada para obter uma lista de imagens e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Imagens individuais podem ser excluídas com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Excluir serviços

Use esta chamada para obter uma lista de serviços e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Serviços individuais podem ser excluídos com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exportar dados do serviço com a API REST

Para exportar dados, as seguintes chamadas à API podem ser feitas com o verbo HTTP GET. Eles são autorizados por ter um cabeçalho `Authorization: Bearer <arm-token>` na solicitação, em que `<arm-token>` é o token de acesso do AAD para o ponto de extremidade `https://management.core.windows.net/`  

Para saber como obter esse token e chamar os pontos de extremidade do Azure, consulte a [documentação da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).   

Nos exemplos a seguir, substitua o texto em {} pelos nomes de instância que determinam o recurso associado.

### <a name="export-workspace-information"></a>Exportar informações do espaço de trabalho

Use esta chamada para obter uma lista de todos os espaços de trabalho:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

As informações sobre um espaço de trabalho individual podem ser obtidas por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Exportar informações de computação

Todos os destinos de computação anexados a um espaço de trabalho podem ser obtidos por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

As informações sobre um único destino de computação podem ser obtidas por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Exportar dados do histórico de execução
Use esta chamada para obter uma lista de todos os experimentos e suas informações:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Todas as execuções para um experimento específico podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Itens de histórico de execução podem ser obtidos por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Todas as métricas de execução para um experimento podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Uma única métrica de execução pode ser obtida por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Exportar artefatos

Use esta chamada para obter uma lista de artefatos e seus caminhos:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Exportar notificações

Use esta chamada para obter uma lista de tarefas armazenadas:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

As notificações para uma única tarefa podem ser obtidas por:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exportar armazenamentos de dados

Use esta chamada para obter uma lista de armazenamentos de dados:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Repositórios de dados individuais podem ser obtidos por:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportar modelos

Use esta chamada para obter uma lista de modelos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Modelos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exportar ativos

Use esta chamada para obter uma lista de ativos e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Ativos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Exportar imagens

Use esta chamada para obter uma lista de imagens e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Imagens individuais podem ser obtidas por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Serviços de exportação

Use esta chamada para obter uma lista de serviços e suas IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Serviços individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Exportar experimentos de pipeline

Experimentos individuais podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportar grafos de pipeline

Os grafos individuais podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportar módulos de pipeline

Os módulos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportar modelos de pipeline

Os modelos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportar fontes de dados de pipeline

As fontes de dados podem ser obtidas por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Excluir ativos no designer

No designer em que você criou seu experimento, exclua ativos individuais:

1. Ir para o designer

    ![Excluir ativos](media/how-to-export-delete-data.md/delete-experiment.png)

1. Na lista, selecione o rascunho de pipeline individual a ser excluído.

1. Na parte inferior, selecione **excluir**.

## <a name="unregister-data-in-the-designer"></a>Cancelar o registro de dados no designer

No designer em que você criou seu experimento, exporte os dados que adicionou:

1. À esquerda, selecione **conjuntos de valores**.



    ![Baixar dados](media/how-to-export-delete-data.md/unregister-dataset.png)

1. Na lista, selecione o conjunto de registros individual e clique em cancelar registro

