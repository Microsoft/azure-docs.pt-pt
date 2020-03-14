---
title: Exportar ou eliminar dados de área de trabalho
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218279"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou eliminar os dados de área de trabalho do serviço de Machine Learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

No Azure Machine Learning, pode exportar ou eliminar os dados de área de trabalho com a API REST autenticados. Este artigo mostra-lhe como.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controle os seus dados de área de trabalho

Os dados in-product armazenados pela Azure Machine Learning estão disponíveis para exportação e eliminação através do estúdio Azure Machine Learning, CLI, SDK e APIs REST autenticados. Dados de telemetria podem ser acedidos através do portal de privacidade do Azure. 

No Azure Machine Learning, os dados pessoais consistem em informações do utilizador em documentos de histórico de execução e registos de telemetria de algumas interações do utilizador com o serviço.

## <a name="delete-workspace-data-with-the-rest-api"></a>Eliminar dados de área de trabalho com a API REST

Para eliminar dados, as seguintes chamadas à API podem ser feitas com o verbo HTTP DELETE. Estes são autorizados por ter um cabeçalho `Authorization: Bearer <arm-token>` no pedido, onde `<arm-token>` é o sinal de acesso AAD para o `https://management.core.windows.net/` ponto final.  

Para aprender a obter este símbolo e ligar para os pontos finais do Azure, consulte [Use REST para gerir os recursos ml](how-to-manage-rest.md) e [documentação Azure REST API](https://docs.microsoft.com/rest/api/azure/).  

Nos exemplos seguintes, substitua o texto em {} pelos nomes de exemplo que determinam o recurso associado.

### <a name="delete-an-entire-workspace"></a>Eliminar uma área de trabalho inteira

Utilize esta chamada para eliminar uma área de trabalho inteira.  
> [!WARNING]
> Todas as informações serão eliminadas e a área de trabalho já não poderá ser utilizada.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Eliminar modelos

Utilize esta chamada para obter uma lista de modelos e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Modelos individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Eliminar recursos

Utilize esta chamada para obter uma lista de recursos e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Recursos individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Eliminar imagens

Utilize esta chamada para obter uma lista de imagens e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Imagens individuais podem ser eliminadas com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Eliminar serviços

Utilize esta chamada para obter uma lista de serviços e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Serviços individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Exportar dados de serviço com a API REST

Para exportar dados, as seguintes chamadas à API podem ser feitas com o verbo HTTP GET. Estes são autorizados por ter um cabeçalho `Authorization: Bearer <arm-token>` no pedido, onde `<arm-token>` é o sinal de acesso a AAD para o ponto final `https://management.core.windows.net/`  

Para aprender a obter este símbolo e ligar para os pontos finais do Azure, consulte [use o REST para gerir os recursos ml](how-to-manage-rest.md) e [documentação Azure REST API](https://docs.microsoft.com/rest/api/azure/)..   

Nos exemplos seguintes, substitua o texto em {} pelos nomes de exemplo que determinam o recurso associado.

### <a name="export-workspace-information"></a>Informações da área de trabalho de exportação

Utilize esta chamada para obter uma lista de todas as áreas de trabalho:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Informações sobre uma área de trabalho individual podem ser obtidas por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Exportar as informações de computação

Todos os computação destinos anexados a uma área de trabalho podem ser obtidos através de:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

É possível obter informações sobre um destino de computação único por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Dados do histórico de execuções de exportação

Utilize esta chamada para obter uma lista de todas as experimentações e suas informações:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Todas as execuções para uma experimentação específica podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Itens podem ser obtidos através do histórico de execução:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Todas as métricas de execução para uma experimentação podem ser obtidas por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Uma única métrica de execução pode ser obtida por:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Artefactos de exportação

Utilize esta chamada para obter uma lista de artefactos e seus caminhos:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Notificações de exportação

Utilize esta chamada para obter uma lista de tarefas armazenadas:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Notificações para uma única tarefa podem ser obtidas por:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Arquivos de dados de exportação

Utilize esta chamada para obter uma lista dos arquivos de dados:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Arquivos de dados individuais podem ser obtidos por:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportar modelos

Utilize esta chamada para obter uma lista de modelos e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Modelos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Exportar recursos

Utilize esta chamada para obter uma lista de recursos e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Recursos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Imagens de exportação

Utilize esta chamada para obter uma lista de imagens e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Imagens individuais podem ser obtidas por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Serviços de exportação

Utilize esta chamada para obter uma lista de serviços e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Serviços individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Experimentações de Pipeline de exportação

Experimentações individuais podem ser obtidas por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportar gráficos de Pipeline

Gráficos individuais podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Módulos de Pipeline de exportação

Módulos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportar modelos de Pipeline

Modelos podem ser obtidos por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportar as origens de dados de Pipeline

Origens de dados podem ser obtidas por:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Eliminar ativos no designer

No designer onde criou a sua experiência, elimine os ativos individuais:

1. Ir ao designer

    ![Eliminar recursos](./media/how-to-export-delete-data/delete-experiment.png)

1. Na lista, selecione o projeto de gasoduto individual para eliminar.

1. Selecione **Eliminar**.

### <a name="delete-datasets-in-the-designer"></a>Eliminar conjuntos de dados no designer

Para eliminar conjuntos de dados no designer, utilize o portal Azure ou o Storage Explorer para navegar para contas de armazenamento conectadas e eliminar conjuntos de dados lá. O desregistar conjuntos de dados no designer apenas remove o ponto de referência no armazenamento.

## <a name="export-data-in-the-designer"></a>Dados de exportação no designer

No designer onde criou a sua experiência, os dados de exportação acrescentou:

1. À esquerda, selecione **Datasets**.

1. Na lista, selecione o conjunto de dados para exportar

    ![Descarregue dados](./media/how-to-export-delete-data/unregister-dataset.png)
