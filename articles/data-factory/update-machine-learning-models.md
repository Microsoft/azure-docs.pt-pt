---
title: Atualizar modelos de aprendizagem automática usando Azure Data Factory
description: Descreve como criar oleodutos preditivos usando a Azure Data Factory e machine learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: e8fb39e8762d31f00029a0eeea33f1e630fb15a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82927416"
---
# <a name="update-ml-studio-classicv-models-by-using-update-resource-activity"></a>Atualizar modelos ML Studio (clássicos)v utilizando a atividade de Update Resource

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo complementa o principal artigo de integração do Azure Data Factory - ML Studio (clássico): [Criar oleodutos preditivos utilizando a Azure Machine Learning e a Azure Data Factory](transform-data-using-machine-learning.md). Se ainda não o fez, reveja o artigo principal antes de ler este artigo.

## <a name="overview"></a>Descrição geral
Como parte do processo de operacionalização dos modelos ML Studio (clássicos), o seu modelo é treinado e guardado. Em seguida, usa-o para criar um serviço Web preditivo. O serviço Web pode então ser consumido em sites, dashboards e aplicações móveis.

Os modelos que cria usando machine learning normalmente não são estáticos. À medida que novos dados se tornam disponíveis ou quando o consumidor da API tem os seus próprios dados, o modelo precisa de ser retreinado. 

A reconversão pode ocorrer com frequência. Com a atividade de execução de lotes e a atividade de Recurso de atualização, pode operacionalizar o modelo de aprendizagem automática Azure e atualizar o serviço web preditivo utilizando a Data Factory.

A imagem que se segue retrata a relação entre o treino e os serviços web preditivos.

![Serviços web](./media/update-machine-learning-models/web-services.png)

## <a name="ml-studio-classic-update-resource-activity"></a>Atividade de recurso de atualização ml Studio (clássico)

O seguinte snippet JSON define uma atividade de execução de lote ML Studio (clássico).

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Propriedade                      | Descrição                              | Necessário |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Nome da atividade no oleoduto     | Sim      |
| descrição                   | Texto descrevendo o que a atividade faz.  | Não       |
| tipo                          | Para a atividade de Azure Machine Learning Update Resource, o tipo de atividade é **AzureMLUpdateResource**. | Sim      |
| linkedServiceName             | Serviço ligado Azure Machine Learning que contém atualização Propriedade ResourceEndpoint. | Sim      |
| me de Modelo treinado              | Nome do módulo Modelo Treinado na experiência do Serviço Web a ser atualizado | Sim      |
| treinadoModelLinkedServiceName | Nome do serviço ligado ao Azure Storage que mantém o ficheiro ilearner que é carregado pela operação de atualização | Sim      |
| treinadoModelFilePath          | O caminho relativo do ficheiro no TreinadoModelLinkedService para representar o ficheiro ilearner que é carregado pela operação de atualização | Sim      |

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto

Todo o processo de operacionalização de um modelo e de atualização dos serviços Web preditivos envolve os seguintes passos:

- Invoque o **Serviço Web de formação** utilizando a **atividade de execução do lote.** Invocar um Serviço Web de formação é o mesmo que invocar um serviço web preditivo descrito na [Criação de oleodutos preditivos utilizando a atividade de aprendizagem automática de Azure machine learning e de execução de lotes de fábrica de dados.](transform-data-using-machine-learning.md) A saída do Serviço Web de formação é um ficheiro iLearner que pode utilizar para atualizar o Serviço Web preditivo.
- Invoque o ponto final de recurso de **atualização** do **Serviço Web preditivo** utilizando a **atividade de Update Resource** para atualizar o Serviço Web com o modelo recém-treinado.

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado a Azure Machine Learning

Para que o fluxo de trabalho acima mencionado funcione, é necessário criar dois serviços ligados à Aprendizagem automática Azure:

1. Um serviço de aprendizagem automática Azure ligado ao serviço web de formação, este serviço ligado é utilizado pela atividade de execução de lotes da mesma forma que o mencionado em [Criar oleodutos preditivos usando a atividade de aprendizagem automática de Azure machine learning e data factory batch execution.](transform-data-using-machine-learning.md) A diferença é que a saída do serviço web de formação é um ficheiro iLearner que é então utilizado pela atividade de Update Resource para atualizar o serviço web preditivo.
2. Um serviço de Aprendizagem automática Azure ligado ao ponto final do recurso de atualização do serviço web preditivo. Este serviço ligado é utilizado pela atividade de Update Resource para atualizar o serviço web preditivo utilizando o ficheiro iLearner devolvido de cima do passo.

Para o segundo serviço ligado a Azure Machine Learning, a configuração é diferente quando o seu Azure Machine Learning Web Service é um Serviço Web clássico ou um novo Serviço Web. As diferenças são discutidas separadamente nas seguintes secções.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>O serviço web é o novo serviço web Azure Resource Manager

Se o serviço web for o novo tipo de serviço web que expõe um ponto final do Azure Resource Manager, não precisa de adicionar o segundo ponto final **não padrão.** A **actualizaçãoResourceEndpoint** no serviço ligado é do formato:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Pode obter valores para os detentores de lugares no URL ao consultar o serviço web no Portal de [Serviços Web de Aprendizagem de Máquinas Azure](https://services.azureml.net/).

O novo tipo de ponto final de recurso de atualização requer a autenticação principal do serviço. Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe a função **de Contribuinte** ou **Proprietário** da subscrição ou do grupo de recursos a que pertence o serviço web. A Ver [como criar o principal de serviço e atribuir permissões para gerir o recurso Azure.](../active-directory/develop/howto-create-service-principal-portal.md) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:

- ID da Aplicação
- Chave de aplicação
- ID do inquilino

Aqui está uma definição de serviço ligada à amostra:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

O cenário seguinte fornece mais detalhes. Tem um exemplo para requalificar e atualizar os modelos de estúdio Azure Machine Learning a partir de um oleoduto Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Amostra: Requalificação e atualização de um modelo de aprendizagem automática Azure

Esta secção fornece um pipeline de amostra que utiliza a **atividade de execução do estúdio Azure Machine Learning** para retreinar um modelo. O pipeline também utiliza a atividade de **Azure Machine Learning studio Update Resource** para atualizar o modelo no serviço web de pontuação. A secção também fornece snippets JSON para todos os serviços ligados, conjuntos de dados e pipeline no exemplo.

### <a name="azure-blob-storage-linked-service"></a>Serviço ligado ao armazenamento Azure Blob:
O Azure Storage contém os seguintes dados:

* dados de formação. Os dados de entrada para o serviço web de formação de estúdio Azure Machine Learning.
* ficheiro iLearner. A saída do serviço web de formação de estúdios Azure Machine Learning. Este ficheiro é também a entrada para a atividade de Recurso de atualização.

Aqui está a definição JSON da amostra do serviço ligado:

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Serviço ligado para a azure machine learning estúdio de formação ponto final
O seguinte snippet JSON define um serviço ligado a Azure Machine Learning que aponta para o ponto final padrão do serviço web de formação.

```JSON
{
    "name": "trainingEndpoint",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
            "apiKey": "myKey"
        }
    }
}
```

No **estúdio Azure Machine Learning,** faça o seguinte para obter valores para **mlEndpoint** e **apiKey**:

1. Clique nos **SERVIÇOS WEB** no menu esquerdo.
2. Clique no **serviço web de formação** na lista de serviços web.
3. Clique na cópia ao lado da caixa de texto **chave API.** Cole a chave na pasta para o editor JSON da Data Factory.
4. No **estúdio Azure Machine Learning,** clique no link **DE EXECUÇÃO DO LOTE.**
5. Copie o **URI pedido** da secção **Request** e cole-o no editor JSON da Data Factory.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Serviço ligado para Azure Machine Learning estúdio updatable ponto final de pontuação:
O seguinte snippet JSON define um serviço ligado a Azure Machine Learning que aponta para o ponto final até ao topo do serviço web de pontuação.

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O gasoduto tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução do lote toma os dados de formação como entrada e produz um ficheiro iLearner como uma saída. A atividade de Update Resource pega então neste ficheiro iLearner e utiliza-o para atualizar o serviço web preditivo.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Próximos passos
Veja os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do porco](transform-data-using-hadoop-pig.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
