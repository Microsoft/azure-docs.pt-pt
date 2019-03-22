---
title: Atualizar Modelos de aprendizagem automática com o Azure Data Factory | Documentos da Microsoft
description: Descreve como criar crie pipelines previsíveis utilizando o Azure Data Factory e machine learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 8f1320db0af85f6c83a9daf8e17a691336c9b251
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58164464"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Atualizar os modelos do Azure Machine Learning, utilizando a atividade de recursos de atualização
Este artigo complementa o principal do Azure Data Factory - artigo de integração do Azure Machine Learning: [Crie pipelines previsíveis utilizando o Azure Machine Learning e Azure Data Factory](transform-data-using-machine-learning.md). Se ainda não o fez, consulte o artigo principal antes de ler este artigo.

## <a name="overview"></a>Descrição geral
Como parte do processo de operacionalização de modelos do Azure Machine Learning, o seu modelo é preparado e guardado. , Em seguida, utilizá-lo para criar um serviço Web preditivo. O serviço Web, em seguida, pode ser consumido em web sites, dashboards e aplicações móveis.

Modelos de que criar com o Machine Learning, normalmente, não são estáticos. À medida que novos dados ficam disponíveis ou quando o consumidor da API tem seus próprios dados tem de reestruturar o modelo. Consulte a [voltar a preparar um modelo de aprendizagem automática](../machine-learning/machine-learning-retrain-machine-learning-model.md) para obter detalhes sobre como pode voltar a preparar um modelo no Azure Machine Learning.

Reparametrização pode ocorrer com frequência. Com a atividade de execução de lotes e atividade de recursos de atualização, pode operacionalizar o modelo do Azure Machine Learning reparametrização e atualizar o serviço Web preditivo com o Data Factory.

A imagem seguinte ilustra a relação entre os serviços de Web de treinamento e preditiva.

![Serviços web](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Atividade de recursos de atualização do Azure Machine Learning

O fragmento JSON seguinte define uma atividade de execução de lote do Azure Machine Learning.

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
| descrição                   | Texto que descreve o que faz a atividade.  | Não       |
| tipo                          | Para a atividade do recurso de atualização do Azure Machine Learning, é o tipo de atividade **AzureMLUpdateResource**. | Sim      |
| linkedServiceName             | Serviço ligado do Machine Learning do Azure que contém a propriedade updateResourceEndpoint. | Sim      |
| trainedModelName              | Nome do módulo modelo preparado na experimentação do serviço Web para ser atualizado | Sim      |
| trainedModelLinkedServiceName | Nome do serviço ligado do armazenamento do Azure que contém o ficheiro ilearner que é carregado pela operação de atualização | Sim      |
| trainedModelFilePath          | O caminho de ficheiro relativo no trainedModelLinkedService para representar o ficheiro ilearner a que é carregado pela operação de atualização | Sim      |

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto

Todo o processo de operacionalização de reparametrização um modelo e atualizar os serviços Web preditivos envolve os seguintes passos:

- Invocar o **serviço Web de treinamento** utilizando o **atividade de execução de lotes**. Invocar um serviço Web de treinamento é o mesmo que invocar um serviço Web preditivo descrito em [crie pipelines previsíveis utilizando o Azure Machine Learning e execução de lotes de fábrica de dados de atividade](transform-data-using-machine-learning.md). A saída do serviço Web de treinamento é um ficheiro iLearner que pode utilizar para atualizar o serviço Web preditivo.
- Invocar o **atualizar o ponto final de recurso** da **preditiva Web Service** utilizando o **atividade de recursos de atualização** para atualizar o serviço Web com o modelo treinado recentemente.

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado do Machine Learning do Azure

Para que ele acima mencionados ponto-a-ponto funcionar, terá de criar dois serviços ligados do Azure Machine Learning:

1. Um Azure Machine Learning serviço ligado ao serviço da web de treinamento, este serviço ligado é utilizado pela atividade de execução de lote da mesma forma como o que é mencionado na [Criar pipelines previsíveis utilizando o Azure Machine Learning e de lote de fábrica de dados Atividade de execução](transform-data-using-machine-learning.md). Diferença é que a saída do serviço web do treinamento é um ficheiro iLearner a que, em seguida, é utilizado pela atividade de recursos de atualização para atualizar o serviço web preditivo.
2. Um serviço ligado do Azure Machine Learning para o ponto de final de recursos de atualização do serviço web preditivo. Este serviço ligado é utilizado pela atividade de recursos de atualização para atualizar o serviço web de previsão a utilizar o ficheiro iLearner devolvido from above passo.

Para o serviço ligado do Azure Machine Learning segundo, a configuração é diferente quando o serviço Web do Azure Machine Learning é um serviço Web clássico ou um novo serviço Web. As diferenças são discutidas separadamente nas seções a seguir.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Serviço Web é o novo serviço web do Azure Resource Manager

Se o serviço web é o novo tipo de serviço web que expõe um ponto de final do Azure Resource Manager, não é necessário adicionar o segundo **não predefinidas** ponto final. O **updateResourceEndpoint** no serviço ligado tem o formato:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Pode obter os valores para espaços reservados no URL ao consultar o serviço web no [Portal do Azure Machine Learning Web Services](https://services.azureml.net/).

O novo tipo de ponto final de recurso de atualização requer autenticação do principal de serviço. Para utilizar a autenticação do principal de serviço, registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) e conceda-o **contribuinte** ou **proprietário** onde de grupo de função da subscrição ou o recurso o serviço web pertence. O, veja [como criar principal de serviço e atribuir permissões para gerir recursos do Azure](../active-directory/develop/howto-create-service-principal-portal.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:

- ID da aplicação
- Chave da aplicação
- ID do inquilino

Esta é uma definição de serviço ligado de exemplo:

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

O cenário seguinte fornece mais detalhes. Ele tem um exemplo de reparametrização e atualizando modelos do Azure Machine Learning studio a partir de um pipeline do Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Exemplo: Reparametrização e atualizar um modelo do Azure Machine Learning

Esta secção fornece um exemplo de pipeline que utiliza a **atividade de execução de lote do Azure Machine Learning studio** para voltar a preparar um modelo. O pipeline também utiliza a **atividade de recursos de atualização do Azure Machine Learning studio** para atualizar o modelo de serviço de web de pontuação. A secção também fornece trechos de código JSON para todos os serviços ligados, conjuntos de dados e pipeline no exemplo.

### <a name="azure-blob-storage-linked-service"></a>Serviço ligado do armazenamento de Blobs do Azure:
O armazenamento do Azure mantém os dados seguintes:

* dados de treinamento. Os dados de entrada para o serviço web de formação do Azure Machine Learning studio.
* ficheiro iLearner. A saída do serviço web de treinamento o Azure Machine Learning studio. Este ficheiro também é a entrada para a atividade de recursos de atualização.

Esta é a definição de JSON de exemplo do serviço ligado:

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Serviço ligado para o ponto final de formação do Azure Machine Learning studio
O seguinte fragmento JSON define um serviço ligado do Azure Machine Learning que aponta para o ponto final predefinido do serviço web do treinamento.

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

Na **Azure Machine Learning studio**, faça o seguinte para obter valores para **mlEndpoint** e **apiKey**:

1. Clique em **serviços da WEB** no menu da esquerda.
2. Clique nas **serviço web de treinamento** na lista de serviços da web.
3. Clique em Copiar junto a **chave de API** caixa de texto. Cole a chave na área de transferência no editor de JSON do Data Factory.
4. Na **Azure Machine Learning studio**, clique em **execução de lotes** ligação.
5. Cópia a **Request URI** partir o **pedir** secção e cole-o num editor de JSON do Data Factory.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Serviço ligado para o Azure Machine Learning studio atualizável classificação ponto final:
O seguinte fragmento JSON define um serviço ligado do Azure Machine Learning, que aponta para o ponto final atualizável do serviço web de pontuação.

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
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução de lotes usa os dados de treinamento como entrada e produz um ficheiro iLearner como saída. A atividade de recursos de atualização, em seguida, utiliza este ficheiro iLearner e utilizá-lo para atualizar o serviço web preditivo.

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
## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do Hive](transform-data-using-hadoop-hive.md)
* [Atividade PIg](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
