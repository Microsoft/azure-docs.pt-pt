---
title: Atualizar Modelos de Machine Learning com o Azure Data Factory | Documentos da Microsoft
description: Descreve como criar crie pipelines previsíveis utilizando o Azure Data Factory e o Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0c0e0e3983344bba76f5f305ecaf73f91110f3bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567342"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>A atualizar os modelos do Azure Machine Learning com a atividade de recursos de atualização

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade do Hive](data-factory-hive-activity.md) 
> * [Atividade PIg](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [atualizar modelos de machine learning no Data Factory](../update-machine-learning-models.md).

Este artigo complementa o principal do Azure Data Factory - artigo de integração do Azure Machine Learning: [Crie pipelines previsíveis utilizando o Azure Machine Learning e Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Se ainda não o fez, consulte o artigo principal antes de ler este artigo. 

## <a name="overview"></a>Descrição geral
Ao longo do tempo, os modelos preditivos no Azure ML experimentações de classificação necessário reestruturar utilizando conjuntos de dados de entrada novo. Depois de terminar com reparametrização, pretende atualizar o serviço web de pontuação com o modelo de ML retrained. As etapas típicas para ativar a reparametrização e com atualização modelos do Azure ML por meio de serviços da web são:

1. Criar uma experimentação no [do Azure ML Studio](https://studio.azureml.net).
2. Quando estiver satisfeito com o modelo, utilizar o Azure ML Studio para publicar serviços web para ambos os **experimentação de preparação** e classificação /**experimentação preditiva**.

A tabela seguinte descreve os serviços web utilizados neste exemplo.  Ver [Retrain Machine Learning dos modelos](../../machine-learning/machine-learning-retrain-models-programmatically.md) para obter detalhes.

- **Serviço web de treinamento** - recebe dados de treinamento e produza modelos de formação. A saída da reparametrização do é um arquivo de .ilearner num armazenamento de Blobs do Azure. O **predefinido o ponto final** é criado automaticamente para quando publica o treinamento de experimentação como um serviço web. Pode criar mais pontos finais, mas o exemplo usa apenas o ponto final predefinido.
- **Serviço web de classificação** - recebe exemplos de dados sem etiqueta e faz predições. A saída de predição pode ter várias formas, como um ficheiro. csv ou linhas numa base de dados SQL do Azure, dependendo da configuração da experimentação. O ponto final predefinido é criado automaticamente para quando publicar a experimentação preditiva como um serviço web. 

A imagem seguinte ilustra a relação entre a preparação e classificação de pontos finais no Azure ML.

![Serviços web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

É possível invocar o **treinamento do serviço web** utilizando o **atividade de execução de lote do Azure ML**. Invocar um serviço da web de treinamento é a mesma que invocar um serviço da web do Azure ML (serviço web de classificação) para dados de classificação. As secções anteriores abrangem como invocar um serviço web do Azure ML a partir de um pipeline do Azure Data Factory em detalhes. 

É possível invocar o **serviço web de classificação** utilizando o **da atividade de recursos de atualização do Azure ML** para atualizar o serviço web com o modelo treinado recentemente. Os exemplos seguintes fornecem definições de serviço ligado: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Serviço web de classificação é um serviço web clássico
Se o serviço web de pontuação é um **serviço web clássico**, criar o segundo **ponto final não predefinidas e atualizável** com o portal do Azure. Ver [criar pontos finais](../../machine-learning/machine-learning-create-endpoint.md) para obter os passos. Depois de criar o ponto de extremidade atualizável não predefinido, siga os passos abaixo:

* Clique em **execução de lotes** para obter o valor URI para o **mlEndpoint** propriedade JSON.
* Clique em **recursos de ATUALIZAÇÃO** ligação para obter o valor URI para o **updateResourceEndpoint** propriedade JSON. A chave de API é a página de ponto final (no canto inferior direito).

![ponto de extremidade atualizável](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

O exemplo seguinte fornece uma definição de JSON de exemplo para o serviço AzureML ligado. O serviço ligado utiliza o apiKey para autenticação.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Serviço web de classificação é o serviço web do Azure Resource Manager 
Se o serviço web é o novo tipo de serviço web que expõe um ponto de final do Azure Resource Manager, não é necessário adicionar o segundo **não predefinidas** ponto final. O **updateResourceEndpoint** no serviço ligado tem o formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Pode obter os valores para espaços reservados no URL ao consultar o serviço web no [Portal do Azure Machine Learning Web Services](https://services.azureml.net/). O novo tipo de ponto final de recurso de atualização exige um token do AAD (Azure Active Directory). Especifique **servicePrincipalId** e **servicePrincipalKey**no AzureML serviço ligado. Ver [como criar principal de serviço e atribuir permissões para gerir recursos do Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Eis um exemplo de definição de serviço AzureML ligado: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

O cenário seguinte fornece mais detalhes. Ele tem um exemplo de reparametrização e atualizando modelos do Azure ML a partir de um pipeline do Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Cenário: reparametrização e atualizar um modelo do Azure ML
Esta secção fornece um exemplo de pipeline que utiliza a **atividade de execução de lote do Azure ML** para voltar a preparar um modelo. O pipeline também utiliza a **atividade de recursos de atualização do Azure ML** para atualizar o modelo de serviço de web de pontuação. A secção também fornece trechos de código JSON para todos os serviços ligados, conjuntos de dados e pipeline no exemplo.

Segue-se a vista de diagrama de exemplo de pipeline. Como pode ver, a atividade de execução de lote do Azure ML assume a entrada de treinamento e produz uma saída de treinamento (ficheiro iLearner). A atividade de recursos de atualização do Azure ML usa esta saída de treinamento e atualiza o modelo no ponto de extremidade de serviço web classificação. A atividade de recursos de atualização não produz quaisquer dados. O placeholderBlob é apenas um dataset de saída fictício que é necessário pelo serviço do Azure Data Factory para executar o pipeline.

![Diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Serviço ligado do armazenamento de Blobs do Azure:
O armazenamento do Azure mantém os dados seguintes:

* dados de treinamento. Os dados de entrada para o serviço de web de formação do Azure ML.  
* ficheiro iLearner. A saída do serviço de web de formação do Azure ML. Este ficheiro também é a entrada para a atividade de recursos de atualização.  

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

### <a name="training-input-dataset"></a>Conjunto de dados entrado de treinamento:
O seguinte conjunto de dados representa os dados de treinamento de entrada para o serviço de web de formação do Azure ML. A atividade de execução de lote do Azure ML obtém este conjunto de dados como entrada.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Conjunto de dados de saída treinamento:
O conjunto de dados seguinte representa o ficheiro iLearner a saída do serviço de web de formação do Azure ML. A atividade de execução de lote do Azure ML produz este conjunto de dados. Este conjunto de dados também é a entrada para a atividade de recursos de atualização do Azure ML.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Serviço ligado para o ponto final de formação do Azure ML
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

Na **do Azure ML Studio**, faça o seguinte para obter valores para **mlEndpoint** e **apiKey**:

1. Clique em **serviços da WEB** no menu da esquerda.
2. Clique nas **serviço web de treinamento** na lista de serviços da web.
3. Clique em Copiar junto a **chave de API** caixa de texto. Cole a chave na área de transferência no editor de JSON do Data Factory.
4. Na **do Azure ML studio**, clique em **execução de lotes** ligação.
5. Cópia a **Request URI** partir o **pedir** secção e cole-o num editor de JSON do Data Factory.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Serviço ligado para o ponto final de classificação atualizável Azure ML:
O seguinte fragmento JSON define um serviço ligado do Azure Machine Learning que aponta para o ponto de extremidade atualizável do serviço web de pontuação não predefinido.  

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

### <a name="placeholder-output-dataset"></a>Conjunto de dados de saída marcador de posição:
A atividade de recursos de atualização do Azure ML não gera quaisquer dados. No entanto, o Azure Data Factory requer um conjunto de dados de saída para orientar o agendamento de um pipeline. Portanto, usamos um conjunto de dados do marcador de posição/dummy neste exemplo.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução de lote do Azure ML usa os dados de treinamento como entrada e produz um ficheiro iLearner como saída. A atividade invoca o serviço web de treinamento (experimentação de preparação exposto como um serviço web) com os dados de entrada de treinamento e recebe o ficheiro ilearner a partir do serviço Web. O placeholderBlob é apenas um dataset de saída fictício que é necessário pelo serviço do Azure Data Factory para executar o pipeline.

![Diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
