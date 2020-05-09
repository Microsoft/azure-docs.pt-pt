---
title: Atualizar modelos de aprendizagem automática usando azure data factory
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
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927416"
---
# <a name="update-ml-studio-classicv-models-by-using-update-resource-activity"></a>Atualizar ml studio (modelos clássicos)v utilizando a atividade de Recurso update

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo complementa o principal artigo de integração da Azure Data Factory - ML Studio (clássico):Criar [oleodutos preditivos utilizando o Azure Machine Learning e a Azure Data Factory.](transform-data-using-machine-learning.md) Se ainda não o fez, reveja o artigo principal antes de ler este artigo.

## <a name="overview"></a>Descrição geral
Como parte do processo de operacionalização dos modelos ML Studio (clássicos), o seu modelo é treinado e salvo. Em seguida, usa-o para criar um serviço Web preditivo. O serviço Web pode então ser consumido em web sites, dashboards e aplicações móveis.

Os modelos que cria sem machine learning não são tipicamente estáticos. À medida que novos dados ficam disponíveis ou quando o consumidor da API tiver os seus próprios dados, o modelo precisa de ser retreinado. 

A reconversão pode ocorrer frequentemente. Com a atividade de execução de lote e a atividade de atualização de recursos, pode operacionalizar o modelo de Aprendizagem automática Azure e atualizar o Serviço Web preditivo utilizando a Data Factory.

A imagem que se segue retrata a relação entre o treino e os serviços web preditivos.

![Serviços web](./media/update-machine-learning-models/web-services.png)

## <a name="ml-studio-classic-update-resource-activity"></a>Ml Studio (clássico) atualizar atividade de recursos

O seguinte snippet JSON define uma atividade de execução de lote de estúdio ML (clássico).

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
| nome                          | Nome da atividade no oleoduto     | Sim      |
| descrição                   | Texto descrevendo o que a atividade faz.  | Não       |
| tipo                          | Para a atividade de atualização de aprendizagem automática Azure, o tipo de atividade é **AzureMLUpdateResource**. | Sim      |
| linkedServiceName             | Serviço ligado à Aprendizagem automática Azure que contém a propriedade de updateResourceEndpoint. | Sim      |
| nome de modelo treinado              | Nome do módulo Modelo Treinado na experiência do Serviço Web a ser atualizado | Sim      |
| treinadoModelLinkedServiceName | Nome do serviço ligado ao Armazenamento Azur, que detém o ficheiro iasso que é carregado pela operação de atualização | Sim      |
| treinadoModelFilePath          | A trajetória relativa de ficheiros no ModelLinkedService treinado para representar o ficheiro iasso que é carregado pela operação de atualização | Sim      |

## <a name="end-to-end-workflow"></a>Fluxo de trabalho ponto a ponto

Todo o processo de operacionalização da reconversão de um modelo e atualização dos Serviços Web preditivos envolve os seguintes passos:

- Invoque o **Serviço Web** de treino utilizando a atividade de **execução**do lote . Invocar um Serviço Web de formação é o mesmo que invocar um Serviço Web preditivo descrito em [Criar oleodutos preditivos utilizando](transform-data-using-machine-learning.md)a atividade de execução de lotes de machine learning e fábrica de dados Azure. A saída do Serviço Web de treino é um ficheiro iLearner que pode utilizar para atualizar o Serviço Web preditivo.
- Invoque o ponto final do recurso de **atualização** do **Serviço Web preditivo** utilizando a atividade de **Recurso atualizado** para atualizar o Serviço Web com o modelo recém-treinado.

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado à Aprendizagem automática Azure

Para que o fluxo de trabalho extremo-a-extremo acima mencionado funcione, é necessário criar dois serviços ligados à Aprendizagem automática Azure:

1. Um serviço azure machine learning ligado ao serviço web de formação, este serviço ligado é usado pela atividade de Execução de Lote da mesma forma que o mencionado em [Pipelines preditivos Create usando](transform-data-using-machine-learning.md)a atividade de execução de lotes de machine learning e fábrica de dados Azure. Diferença é que a saída do serviço web de treino é um ficheiro iLearner que é então utilizado pela atividade do Update Resource para atualizar o serviço web preditivo.
2. Um serviço azure machine learning ligado ao ponto final do recurso de atualização do serviço web preditivo. Este serviço ligado é utilizado pela atividade do Update Resource para atualizar o serviço web preditivo utilizando o ficheiro iLearner devolvido de cima.

Para o segundo serviço ligado ao Azure Machine Learning, a configuração é diferente quando o seu Serviço Web de Aprendizagem automática Azure é um Serviço Web clássico ou um novo Serviço Web. As diferenças são discutidas separadamente nas seguintes secções.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Serviço web é novo serviço web Do Gestor de Recursos Azure

Se o serviço web for o novo tipo de serviço web que expõe um ponto final do Gestor de Recursos Azure, não precisa de adicionar o segundo ponto final **não predefinido.** A **actualizaçãoResourceEndpoint** no serviço ligado é do formato:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Pode obter valores para os suportes de lugares no URL ao consultar o serviço web no Portal de Serviços Web de [Aprendizagem automática Azure](https://services.azureml.net/).

O novo tipo de ponto final do recurso de atualização requer a autenticação principal do serviço. Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe o papel de **Contribuinte** ou **Proprietário** da subscrição ou do grupo de recursos onde o serviço web pertence. Veja [como criar o principal de serviço e atribuir permissões para gerir o recurso Azure](../active-directory/develop/howto-create-service-principal-portal.md). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:

- ID da aplicação
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

O seguinte cenário fornece mais detalhes. Tem um exemplo para retreinar e atualizar os modelos de estúdio Azure Machine Learning a partir de um pipeline Azure Data Factory.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Amostra: Reconversão e atualização de um modelo de Aprendizagem automática Azure

Esta secção fornece um pipeline de amostra que utiliza a atividade de execução de **lotexecução do estúdio de aprendizagem automática Azure** para retreinar um modelo. O pipeline também utiliza a atividade de Update Resource do **estúdio Azure Machine Learning** para atualizar o modelo no serviço web de pontuação. A secção também fornece snippets JSON para todos os serviços, conjuntos de dados e pipeline ligados no exemplo.

### <a name="azure-blob-storage-linked-service"></a>Serviço ligado ao armazenamento Azure Blob:
O Armazenamento Azure detém os seguintes dados:

* dados de formação. Os dados de entrada para o serviço web de formação de estúdio Azure Machine Learning.
* ficheiro iLearner. A saída do serviço web de formação de estúdio Azure Machine Learning. Este ficheiro é também a entrada na atividade do Recurso de Atualização.

Aqui está a definição jSON da amostra do serviço ligado:

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Serviço ligado para estúdio de aprendizagem automática Azure
O seguinte snippet JSON define um serviço ligado à Aprendizagem automática Azure que aponta para o ponto final padrão do serviço web de treino.

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

No **estúdio Azure Machine Learning,** faça o seguinte para obter valores para **mlEndpoint** e **apiKey:**

1. Clique em **WEB SERVICES** no menu esquerdo.
2. Clique no serviço web de **formação** na lista de serviços web.
3. Clique na cópia ao lado da caixa de texto **chave API.** Colhe a chave na pasta no editor jSON da Fábrica de Dados.
4. No **estúdio Azure Machine Learning,** clique no link **BATCH EXECUTION.**
5. Copie o **Pedido URI** da secção **Solicitar** e cole-o no editor da Fábrica de Dados JSON.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Serviço ligado para estúdio de Machine Learning Azure ponto final de pontuação:
O seguinte snippet JSON define um serviço ligado à Aprendizagem automática Azure que aponta para um ponto final updatable do serviço web de pontuação.

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
O gasoduto tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de Execução de Lote toma os dados de formação como entrada e produz um ficheiro iLearner como uma saída. A atividade do Recurso update pega então neste ficheiro iLearner e usa-o para atualizar o serviço web preditivo.

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
Consulte os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade de porco](transform-data-using-hadoop-pig.md)
* [MapReduzir a atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
