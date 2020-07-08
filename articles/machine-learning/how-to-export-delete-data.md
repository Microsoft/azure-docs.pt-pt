---
title: Exportar ou apagar dados do espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como exportar ou apagar o seu espaço de trabalho com o estúdio Azure Machine Learning, CLI, SDK e APIs de REPOUSO autenticado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.custom: seodec18
ms.openlocfilehash: 78e8d02ecaaf57c457a0eaeed86d392cd6372fa9
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963704"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou eliminar os dados do seu espaço de trabalho do serviço de Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

No Azure Machine Learning, pode exportar ou eliminar os seus dados do espaço de trabalho com a API DE REPOUSO autenticada. Este artigo diz-lhe como.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controle os seus dados do espaço de trabalho

Os dados do produto armazenados pela Azure Machine Learning estão disponíveis para exportação e eliminação através do estúdio Azure Machine Learning, CLI, SDK e REA DE REPOUSO autenticado. Os dados de telemetria podem ser acedidos através do portal Azure Privacy. 

No Azure Machine Learning, os dados pessoais consistem em informações do utilizador em documentos de histórico executados e registos de telemetria de algumas interações do utilizador com o serviço.

## <a name="delete-workspace-data-with-the-rest-api"></a>Eliminar dados do espaço de trabalho com a API REST

Para eliminar dados, as seguintes chamadas API podem ser feitas com o verbo HTTP DELETE. Estes são autorizados por ter um `Authorization: Bearer <arm-token>` cabeçalho no pedido, onde está o sinal de `<arm-token>` acesso AAD para o `https://management.core.windows.net/` ponto final.  

Para aprender a obter este token e ligue para os pontos finais do Azure, consulte [Use REST para gerir os recursos ML](how-to-manage-rest.md) e [documentação API AZURE REST](https://docs.microsoft.com/rest/api/azure/).  

Nos exemplos seguintes, substitua o texto {} pelos nomes de instância que determinam o recurso associado.

### <a name="delete-an-entire-workspace"></a>Apagar um espaço de trabalho inteiro

Utilize esta chamada para apagar todo um espaço de trabalho.  
> [!WARNING]
> Toda a informação será eliminada e o espaço de trabalho deixará de ser utilizável.

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="delete-models"></a>Eliminar modelos

Utilize esta chamada para obter uma lista de modelos e seus IDs:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

Os modelos individuais podem ser eliminados com:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="delete-assets"></a>Eliminar ativos

Use esta chamada para obter uma lista de ativos e suas iDs:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

Os ativos individuais podem ser eliminados com:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="delete-images"></a>Eliminar imagens

Use esta chamada para obter uma lista de imagens e seus IDs:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

As imagens individuais podem ser eliminadas com:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="delete-services"></a>Apagar serviços

Utilize esta chamada para obter uma lista de serviços e seus IDs:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

Os serviços individuais podem ser eliminados com:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

## <a name="export-service-data-with-the-rest-api"></a>Dados do serviço de exportação com a API REST

Para exportar dados, as seguintes chamadas API podem ser feitas com o verbo HTTP GET. Estes são autorizados por ter um `Authorization: Bearer <arm-token>` cabeçalho no pedido, onde `<arm-token>` está o token de acesso da AAD para o ponto final`https://management.core.windows.net/`  

Para aprender a obter este token e ligue para os pontos finais do Azure, consulte [Use REST para gerir os recursos ML](how-to-manage-rest.md) e [documentação AZure REST API](https://docs.microsoft.com/rest/api/azure/)..   

Nos exemplos seguintes, substitua o texto {} pelos nomes de instância que determinam o recurso associado.

### <a name="export-workspace-information"></a>Informação sobre o espaço de trabalho de exportação

Use esta chamada para obter uma lista de todos os espaços de trabalho:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01`

Informações sobre um espaço de trabalho individual podem ser obtidas por:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01`

### <a name="export-compute-information"></a>Informação sobre o Cálculo de Exportação

Todos os alvos de cálculo ligados a um espaço de trabalho podem ser obtidos por:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01`

As informações sobre um único alvo de cálculo podem ser obtidas:

`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01`

### <a name="export-run-history-data"></a>Dados do histórico da exportação

Use esta chamada para obter uma lista de todas as experiências e suas informações:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments`

Todas as corridas para uma experiência específica podem ser obtidas por:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs`

Os itens de história de execução podem ser obtidos por:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId}`

Todas as métricas de execução para uma experiência podem ser obtidas por:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics`

Uma única métrica de corrida pode ser obtida por:

`https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}`

### <a name="export-artifacts"></a>Artefactos de exportação

Use esta chamada para obter uma lista de artefactos e seus caminhos:

`https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}`

### <a name="export-notifications"></a>Notificações de exportação

Utilize esta chamada para obter uma lista de tarefas armazenadas:

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks`

As notificações para uma única tarefa podem ser obtidas:

`https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}`

### <a name="export-data-stores"></a>Lojas de dados de exportação

Utilize esta chamada para obter uma lista de lojas de dados:

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores`

As lojas de dados individuais podem ser obtidas por:

`https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}`

### <a name="export-models"></a>Exportar modelos

Utilize esta chamada para obter uma lista de modelos e seus IDs:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01`

Os modelos individuais podem ser obtidos por:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01`

### <a name="export-assets"></a>Ativos de exportação

Use esta chamada para obter uma lista de ativos e suas iDs:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01`

Os ativos individuais podem ser obtidos por:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01`

### <a name="export-images"></a>Imagens de exportação

Use esta chamada para obter uma lista de imagens e seus IDs:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01`

As imagens individuais podem ser obtidas por:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01`

### <a name="export-services"></a>Serviços de exportação

Utilize esta chamada para obter uma lista de serviços e seus IDs:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01`

Os serviços individuais podem ser obtidos por:

`https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01`

### <a name="export-pipeline-experiments"></a>Experiências de Gasoduto de Exportação

As experiências individuais podem ser obtidas por:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}`

### <a name="export-pipeline-graphs"></a>Gráficos de pipeline de exportação

Os gráficos individuais podem ser obtidos por:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}`

### <a name="export-pipeline-modules"></a>Módulos de Gasoduto de Exportação

Os módulos podem ser obtidos por:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}`

### <a name="export-pipeline-templates"></a>Modelos de gasoduto de exportação

Os modelos podem ser obtidos por:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}`

### <a name="export-pipeline-data-sources"></a>Fontes de dados do gasoduto de exportação

As fontes de dados podem ser obtidas por:

`https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}`

## <a name="delete-assets-in-the-designer"></a>Eliminar ativos no designer

No designer onde criou a sua experiência, elimine os ativos individuais:

1. Ir para designer

    ![Eliminar ativos](./media/how-to-export-delete-data/delete-experiment.png)

1. Na lista, selecione o rascunho do pipeline individual para eliminar.

1. Selecione **Eliminar**.

### <a name="delete-datasets-in-the-designer"></a>Eliminar conjuntos de dados no designer

Para eliminar conjuntos de dados no designer, utilize o portal Azure ou o Storage Explorer para navegar para contas de armazenamento conectadas e eliminar conjuntos de dados lá. Não registar conjuntos de dados no designer apenas remove o ponto de referência no armazenamento.

