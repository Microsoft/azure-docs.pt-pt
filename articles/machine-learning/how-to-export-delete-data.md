---
title: Exportar ou eliminar dados do espaço de trabalho
titleSuffix: Azure Machine Learning
description: Aprenda a exportar ou a eliminar o seu espaço de trabalho com o estúdio Azure Machine Learning, CLI, SDK e APIs REST autenticados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 4abef0146b4bf0cfaa254d196b0ca68f0d8ac883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218279"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou eliminar os seus dados do espaço de trabalho do serviço de aprendizagem automática

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

No Azure Machine Learning, pode exportar ou eliminar os seus dados do espaço de trabalho com a API REST autenticada. Este artigo diz-lhe como.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controle os seus dados do espaço de trabalho

Os dados in-product armazenados pela Azure Machine Learning estão disponíveis para exportação e eliminação através do estúdio Azure Machine Learning, CLI, SDK e APIs REST autenticados. Os dados de telemetria podem ser acedidos através do portal de privacidade Azure. 

No Azure Machine Learning, os dados pessoais consistem em informações do utilizador em documentos de histórico de execução e registos de telemetria de algumas interações do utilizador com o serviço.

## <a name="delete-workspace-data-with-the-rest-api"></a>Eliminar dados do espaço de trabalho com a API REST

Para eliminar os dados, as seguintes chamadas API podem ser efetuadas com o verbo HTTP DELETE. Estes são autorizados `Authorization: Bearer <arm-token>` por ter um `<arm-token>` cabeçalho no pedido, onde `https://management.core.windows.net/` está o sinal de acesso AAD para o ponto final.  

Para aprender a obter este símbolo e ligar para os pontos finais do Azure, consulte [Use REST para gerir os recursos ml](how-to-manage-rest.md) e [documentação Azure REST API](https://docs.microsoft.com/rest/api/azure/).  

Nos exemplos seguintes, substitua o texto {} pelos nomes da instância que determinam o recurso associado.

### <a name="delete-an-entire-workspace"></a>Eliminar todo um espaço de trabalho

Utilize esta chamada para eliminar todo um espaço de trabalho.  
> [!WARNING]
> Toda a informação será eliminada e o espaço de trabalho deixará de ser utilizável.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Eliminar modelos

Use esta chamada para obter uma lista de modelos e suas iDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Os modelos individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Eliminar ativos

Use esta chamada para obter uma lista de bens e suas iDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Os ativos individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Eliminar imagens

Use esta chamada para obter uma lista de imagens e suas iDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

As imagens individuais podem ser eliminadas com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Eliminar serviços

Use esta chamada para obter uma lista de serviços e suas iDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Os serviços individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Dados do serviço de exportação com a API REST

Para exportar dados, as seguintes chamadas API podem ser feitas com o verbo HTTP GET. Estes são autorizados `Authorization: Bearer <arm-token>` por ter um `<arm-token>` cabeçalho no pedido, onde está o sinal de acesso AAD para o ponto final`https://management.core.windows.net/`  

Para aprender a obter este símbolo e ligar para os pontos finais do Azure, consulte [use o REST para gerir os recursos ml](how-to-manage-rest.md) e [documentação Azure REST API](https://docs.microsoft.com/rest/api/azure/)..   

Nos exemplos seguintes, substitua o texto {} pelos nomes da instância que determinam o recurso associado.

### <a name="export-workspace-information"></a>Informação do Espaço de Trabalho de Exportação

Use esta chamada para obter uma lista de todos os espaços de trabalho:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

A informação sobre um espaço de trabalho individual pode ser obtida por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Informação computacional de exportação

Todos os alvos de computação ligados a um espaço de trabalho podem ser obtidos por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

A informação sobre um único objetivo de cálculo pode ser obtida por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Dados do histórico de exportação

Use esta chamada para obter uma lista de todas as experiências e suas informações:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Todas as corridas para uma determinada experiência podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Os itens de histórico de execução podem ser obtidos por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Todas as métricas de execução para uma experiência podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Uma única métrica de execução pode ser obtida por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Artefactos de exportação

Use esta chamada para obter uma lista de artefactos e seus caminhos:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Notificações de exportação

Utilize esta chamada para obter uma lista de tarefas armazenadas:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

As notificações para uma única tarefa podem ser obtidas por:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Lojas de dados de exportação

Utilize esta chamada para obter uma lista de lojas de dados:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

As lojas de dados individuais podem ser obtidas por:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportar modelos

Use esta chamada para obter uma lista de modelos e suas iDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Os modelos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Ativos de exportação

Use esta chamada para obter uma lista de bens e suas iDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Os ativos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Imagens de exportação

Use esta chamada para obter uma lista de imagens e suas iDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

As imagens individuais podem ser obtidas por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Serviços de exportação

Use esta chamada para obter uma lista de serviços e suas iDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Os serviços individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Experiências de gasoduto de exportação

As experiências individuais podem ser obtidas por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Gráficos de gasoduto de exportação

Os gráficos individuais podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Módulos de gasoduto de exportação

Os módulos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Modelos de gasoduto de exportação

Os modelos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Fontes de dados do gasoduto de exportação

As Fontes de Dados podem ser obtidas por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Eliminar ativos no designer

No designer onde criou a sua experiência, elimine os ativos individuais:

1. Ir ao designer

    ![Eliminar ativos](./media/how-to-export-delete-data/delete-experiment.png)

1. Na lista, selecione o projeto de gasoduto individual para eliminar.

1. Selecione **Eliminar**.

### <a name="delete-datasets-in-the-designer"></a>Eliminar conjuntos de dados no designer

Para eliminar conjuntos de dados no designer, utilize o portal Azure ou o Storage Explorer para navegar para contas de armazenamento conectadas e eliminar conjuntos de dados lá. O desregistar conjuntos de dados no designer apenas remove o ponto de referência no armazenamento.

## <a name="export-data-in-the-designer"></a>Dados de exportação no designer

No designer onde criou a sua experiência, os dados de exportação acrescentou:

1. À esquerda, selecione **Datasets**.

1. Na lista, selecione o conjunto de dados para exportar

    ![Transferir dados](./media/how-to-export-delete-data/unregister-dataset.png)
