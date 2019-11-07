---
title: Atualizar modelos de Machine Learning usando o Azure Data Factory
description: Descreve como criar pipelines de previsão usando o Azure Data Factory e o Machine Learning
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
ms.openlocfilehash: 3313c9c362a9b82cf7ed8db63479aaa5cf0c777e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683238"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Atualizar modelos de Azure Machine Learning usando atualizar atividade de recurso
Este artigo complementa o principal artigo de integração de Azure Data Factory Azure Machine Learning: [criar pipelines preditivas usando Azure Machine Learning e Azure data Factory](transform-data-using-machine-learning.md). Se você ainda não tiver feito isso, examine o artigo principal antes de ler este artigo.

## <a name="overview"></a>Descrição geral
Como parte do processo de operacionalização de modelos de Azure Machine Learning, seu modelo é treinado e salvo. Em seguida, você o usa para criar um serviço Web de previsão. O serviço Web pode ser consumido em sites, painéis e aplicativos móveis.

Modelos criados usando Machine Learning normalmente não são estáticos. À medida que novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados, o modelo precisa ser retreinado. Consulte [readaptação de um modelo de Machine Learning](../machine-learning/machine-learning-retrain-machine-learning-model.md) para obter detalhes sobre como você pode treinar novamente um modelo no Azure Machine Learning.

O novo treinamento pode ocorrer com frequência. Com a atividade de execução em lote e a atividade atualizar recurso, você pode colocar em operação o modelo de Azure Machine Learning retreinando e atualizando o serviço Web de previsão usando Data Factory.

A imagem a seguir descreve a relação entre o treinamento e os serviços Web de previsão.

![Serviços web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning atualizar atividade de recurso

O trecho JSON a seguir define uma atividade de execução de Azure Machine Learning lote.

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
| nome                          | Nome da atividade no pipeline     | Sim      |
| descrição                   | Texto que descreve o que a atividade faz.  | Não       |
| tipo                          | Para Azure Machine Learning atividade atualizar recurso, o tipo de atividade é **AzureMLUpdateResource**. | Sim      |
| linkedServiceName             | Azure Machine Learning serviço vinculado que contém a propriedade updateResourceEndpoint. | Sim      |
| trainedModelName              | Nome do módulo modelo treinado no experimento do serviço Web a ser atualizado | Sim      |
| trainedModelLinkedServiceName | Nome do serviço vinculado do armazenamento do Azure que contém o arquivo ilearner que é carregado pela operação de atualização | Sim      |
| trainedModelFilePath          | O caminho relativo do arquivo em trainedModelLinkedService para representar o arquivo ilearner que é carregado pela operação de atualização | Sim      |

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto

Todo o processo de operacionalização de novo treinamento de um modelo e de atualização dos serviços Web de previsão envolve as seguintes etapas:

- Invocar o **serviço Web de treinamento** usando a **atividade de execução em lote**. Invocar um serviço Web de treinamento é o mesmo que invocar um serviço Web de previsão descrito em [criar pipelines preditivas usando Azure Machine Learning e data Factory atividade de execução em lote](transform-data-using-machine-learning.md). A saída do serviço Web de treinamento é um arquivo iLearner que você pode usar para atualizar o serviço Web de previsão.
- Invoque o **ponto de extremidade de recurso de atualização** do **serviço Web de previsão** usando a **atividade atualizar recurso** para atualizar o serviço Web com o modelo treinado recentemente.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning serviço vinculado

Para que o fluxo de trabalho de ponta a ponta mencionado acima funcione, você precisa criar dois Azure Machine Learning serviços vinculados:

1. Um Azure Machine Learning serviço vinculado ao serviço Web de treinamento, esse serviço vinculado é usado pela atividade de execução em lote da mesma maneira que o que é mencionado em [criar pipelines preditivas usando Azure Machine Learning e data Factory execução em lote atividade](transform-data-using-machine-learning.md). Diferença é a saída do serviço Web de treinamento é um arquivo iLearner que é usado pela atividade atualizar recurso para atualizar o serviço Web de previsão.
2. Um Azure Machine Learning serviço vinculado ao ponto de extremidade de recurso de atualização do serviço Web de previsão. Esse serviço vinculado é usado pela atividade atualizar recurso para atualizar o serviço Web de previsão usando o arquivo iLearner retornado da etapa acima.

Para o segundo Azure Machine Learning serviço vinculado, a configuração é diferente quando o serviço Web do Azure Machine Learning é um serviço Web clássico ou um novo serviço Web. As diferenças são discutidas separadamente nas seções a seguir.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>O serviço Web é novo Azure Resource Manager serviço Web

Se o serviço Web for o novo tipo de serviço Web que expõe um ponto de extremidade Azure Resource Manager, você não precisará adicionar o segundo ponto de extremidade **não padrão** . O **updateResourceEndpoint** no serviço vinculado está no formato:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Você pode obter valores para colocar os contentores na URL ao consultar o serviço Web no [portal de serviços web Azure Machine Learning](https://services.azureml.net/).

O novo tipo de ponto de extremidade de recurso de atualização requer autenticação de entidade de serviço. Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (AD do Azure) e conceda a ela a função de **colaborador** ou **proprietário** da assinatura ou do grupo de recursos ao qual o serviço Web pertence. Veja [como criar uma entidade de serviço e atribuir permissões para gerenciar o recurso do Azure](../active-directory/develop/howto-create-service-principal-portal.md). Anote os seguintes valores, que você usa para definir o serviço vinculado:

- ID da aplicação
- Chave do aplicativo
- ID do inquilino

Aqui está um exemplo de definição de serviço vinculado:

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

O cenário a seguir fornece mais detalhes. Ele tem um exemplo para retreinar e atualizar modelos do Azure Machine Learning Studio de um pipeline de Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Exemplo: retreinando e atualizando um modelo de Azure Machine Learning

Esta seção fornece um pipeline de exemplo que usa a **atividade de execução em lote do Azure Machine Learning Studio** para treinar novamente um modelo. O pipeline também usa a **atividade de recurso de atualização do Azure Machine Learning Studio** para atualizar o modelo no serviço Web de pontuação. A seção também fornece trechos de código JSON para todos os serviços vinculados, conjuntos de itens e pipeline no exemplo.

### <a name="azure-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de BLOBs do Azure:
O armazenamento do Azure contém os seguintes dados:

* dados de treinamento. Os dados de entrada para o serviço Web de treinamento do Azure Machine Learning Studio.
* arquivo iLearner. A saída do serviço Web de treinamento do Azure Machine Learning Studio. Esse arquivo também é a entrada para a atividade atualizar recurso.

Aqui está a definição de JSON de exemplo do serviço vinculado:

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Serviço vinculado para o ponto de extremidade de treinamento do Azure Machine Learning Studio
O trecho JSON a seguir define um Azure Machine Learning serviço vinculado que aponta para o ponto de extremidade padrão do serviço Web de treinamento.

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

No **Azure Machine Learning Studio**, faça o seguinte para obter valores para **mlEndpoint** e **apiKey**:

1. Clique em **Serviços Web** no menu à esquerda.
2. Clique no **serviço Web de treinamento** na lista de serviços Web.
3. Clique em copiar ao lado de caixa de texto **chave de API** . Cole a chave na área de transferência no Data Factory editor de JSON.
4. No **Azure Machine Learning Studio**, clique no link **execução em lote** .
5. Copie o **URI de solicitação** da seção **solicitação** e cole-o no editor data Factory JSON.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Serviço vinculado para o ponto de extremidade de Pontuação atualizável do Azure Machine Learning Studio:
O trecho JSON a seguir define um Azure Machine Learning serviço vinculado que aponta para o ponto de extremidade atualizável do serviço Web de pontuação.

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
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução em lote usa os dados de treinamento como entrada e produz um arquivo iLearner como uma saída. Em seguida, a atividade atualizar recurso usa esse arquivo iLearner e o utiliza para atualizar o serviço Web de previsão.

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
## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
