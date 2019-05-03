---
title: Exportar ou eliminar dados de área de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como exportar ou eliminar a área de trabalho com o portal do Azure, CLI, SDK e as APIs de REST autenticados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 3b55282adf56bedb0724eca578b8eaea00c26074
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023981"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportar ou eliminar os dados de área de trabalho do serviço de Machine Learning 

No Azure Machine Learning, pode exportar ou eliminar os dados de área de trabalho com a API REST autenticados. Este artigo mostra-lhe como.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Controle os seus dados de área de trabalho
Dados armazenados por serviços do Azure Machine Learning do produto estão disponíveis para a exportação e eliminação através do portal do Azure, CLI, SDK e autenticado REST APIs. Dados de telemetria podem ser acedidos através do portal de privacidade do Azure. 

Em serviços do Azure Machine Learning, os dados pessoais consiste em informações de utilizador em documentos de histórico de execuções e registos de telemetria de algumas interações de utilizador com o serviço.

## <a name="delete-workspace-data-with-the-rest-api"></a>Eliminar dados de área de trabalho com a API REST 

Para eliminar dados, as seguintes chamadas à API podem ser feitas com o verbo HTTP DELETE. Estes são autorizados por ter uma `Authorization: Bearer <arm-token>` cabeçalho na solicitação, onde `<arm-token>` é o token de acesso do AAD para o `https://management.core.windows.net/` ponto final.  

Para saber como obter esse token e chamar pontos finais do Azure, veja [documentação da API de REST do Azure](https://docs.microsoft.com/rest/api/azure/).  

Nos exemplos a seguir, substitua o texto {} com os nomes de instância que determinam o recurso associado.

### <a name="delete-an-entire-workspace"></a>Eliminar uma área de trabalho inteira

Utilize esta chamada para eliminar uma área de trabalho inteira.  
> [!WARNING]
> Todas as informações serão eliminadas e a área de trabalho já não poderá ser utilizada.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Eliminar modelos

Utilize esta chamada para obter uma lista de modelos e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Modelos individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Eliminar recursos

Utilize esta chamada para obter uma lista de recursos e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Recursos individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Eliminar imagens

Utilize esta chamada para obter uma lista de imagens e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Imagens individuais podem ser eliminadas com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Eliminar serviços

Utilize esta chamada para obter uma lista de serviços e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Serviços individuais podem ser eliminados com:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exportar dados de serviço com a API REST

Para exportar dados, as seguintes chamadas à API podem ser feitas com o verbo HTTP GET. Estes são autorizados por ter uma `Authorization: Bearer <arm-token>` cabeçalho na solicitação, onde `<arm-token>` é o token de acesso do AAD para o ponto final `https://management.core.windows.net/`  

Para saber como obter esse token e chamar pontos finais do Azure, veja [documentação da API de REST do Azure](https://docs.microsoft.com/rest/api/azure/).   

Nos exemplos a seguir, substitua o texto {} com os nomes de instância que determinam o recurso associado.

### <a name="export-workspace-information"></a>Informações da área de trabalho de exportação

Utilize esta chamada para obter uma lista de todas as áreas de trabalho:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Informações sobre uma área de trabalho individual podem ser obtidas por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Exportar as informações de computação

Todos os computação destinos anexados a uma área de trabalho podem ser obtidos através de:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

É possível obter informações sobre um destino de computação único por:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

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

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Modelos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exportar recursos

Utilize esta chamada para obter uma lista de recursos e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Recursos individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Imagens de exportação

Utilize esta chamada para obter uma lista de imagens e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Imagens individuais podem ser obtidas por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Serviços de exportação

Utilize esta chamada para obter uma lista de serviços e os respetivos IDs:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Serviços individuais podem ser obtidos por:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

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

## <a name="delete-visual-interface-assets"></a>Eliminar recursos de visual interface

Na interface do visual onde criou a sua experimentação, elimine recursos individuais:

1. No lado esquerdo, selecione o tipo de recurso que pretende eliminar.

    ![Eliminar recursos](media/how-to-export-delete-data.md/delete-experiment.png)

1. Na lista, selecione os recursos individuais para eliminar.

1. Na parte inferior, selecione **eliminar**.

## <a name="export-visual-interface-data"></a>Exportar dados de visual interface

Na interface do visual onde criou a sua experimentação, exporte dados que foram adicionados:

1. No lado esquerdo, selecione **dados**.

1. Na parte superior, selecione **conjuntos de dados de meu** ou **exemplos** para localizar os dados que pretende exportar.

    ![Transferir dados](media/how-to-export-delete-data.md/download-data.png)

1. Na lista, selecione os conjuntos de dados individuais, para exportar.

1. Na parte inferior, selecione **transferir**.