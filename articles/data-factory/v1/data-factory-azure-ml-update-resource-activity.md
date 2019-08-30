---
title: Atualizar modelos de Machine Learning usando Azure Data Factory | Microsoft Docs
description: Descreve como criar pipelines de previsão usando Azure Data Factory e Azure Machine Learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: a980f269c8b88618ffa3311c05310a88ade379ed
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140460"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Atualizando modelos de Azure Machine Learning usando a atividade atualizar recurso

> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do hive](data-factory-hive-activity.md) 
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [Atualizar modelos de aprendizado de máquina no data Factory](../update-machine-learning-models.md).

Este artigo complementa o principal artigo de integração de Azure Data Factory Azure Machine Learning: [Crie pipelines preditivas usando Azure Machine Learning e Azure data Factory](data-factory-azure-ml-batch-execution-activity.md). Se você ainda não tiver feito isso, examine o artigo principal antes de ler este artigo. 

## <a name="overview"></a>Descrição geral
Ao longo do tempo, os modelos de previsão nos experimentos de Pontuação do Azure ML precisam ser retreinados usando novos conjuntos de dados de entrada. Depois de concluir o retreinamento, você deseja atualizar o serviço Web de pontuação com o modelo ML retreinado. As etapas típicas para habilitar o retreinamento e a atualização de modelos do Azure ML por meio de serviços Web são:

1. Crie um experimento no [Azure ml Studio](https://studio.azureml.net).
2. Quando estiver satisfeito com o modelo, use o ML Studio do Azure para publicar serviços Web para o **experimento de treinamento** e teste de Pontuação/**previsão**.

A tabela a seguir descreve os serviços Web usados neste exemplo.  Consulte [readaptação de modelos de Machine Learning](../../machine-learning/machine-learning-retrain-models-programmatically.md) programaticamente para obter detalhes.

- **Serviço Web de treinamento** – recebe dados de treinamento e produz modelos treinados. A saída do novo treinamento é um arquivo. ilearner em um armazenamento de BLOBs do Azure. O **ponto de extremidade padrão** é criado automaticamente para você quando você publica o teste de treinamento como um serviço Web. Você pode criar mais pontos de extremidade, mas o exemplo usa apenas o ponto de extremidades padrão.
- **Serviço Web de Pontuação** – recebe exemplos de dados sem rótulo e faz previsões. A saída da previsão pode ter várias formas, como um arquivo. csv ou linhas em um banco de dados SQL do Azure, dependendo da configuração do experimento. O ponto de extremidade padrão é criado automaticamente para você quando você publica o teste de previsão como um serviço Web. 

A imagem a seguir descreve a relação entre os pontos de extremidade de treinamento e pontuação no Azure ML.

![Serviços web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Você pode invocar o **serviço Web de treinamento** usando a **atividade de execução em lote do Azure ml**. Invocar um serviço Web de treinamento é o mesmo que invocar um serviço Web do Azure ML (serviço Web de pontuação) para dados de pontuação. As seções anteriores abordam como invocar um serviço Web do Azure ML de um Azure Data Factory pipeline em detalhes. 

Você pode invocar o **serviço Web de Pontuação** usando a **atividade de recurso de atualização do am do Azure** para atualizar o serviço Web com o modelo treinado recentemente. Os exemplos a seguir fornecem definições de serviço vinculado: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>O serviço Web de pontuação é um serviço Web clássico
Se o serviço Web de pontuação for um **serviço Web clássico**, crie o segundo **ponto de extremidade não padrão e atualizável** usando o portal do Azure. Confira o artigo [criar pontos de extremidade](../../machine-learning/machine-learning-create-endpoint.md) para obter as etapas. Depois de criar o ponto de extremidade atualizável não padrão, execute as seguintes etapas:

* Clique em **execução em lote** para obter o valor do URI para a propriedade JSON **mlEndpoint** .
* Clique em atualizar link de **recurso** para obter o valor do URI para a propriedade JSON **updateResourceEndpoint** . A chave de API está na própria página do ponto de extremidade (no canto inferior direito).

![ponto de extremidade atualizável](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

O exemplo a seguir fornece uma definição de JSON de exemplo para o serviço vinculado do AzureML. O serviço vinculado usa o apiKey para autenticação.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>O serviço Web de pontuação é Azure Resource Manager serviço Web 
Se o serviço Web for o novo tipo de serviço Web que expõe um ponto de extremidade Azure Resource Manager, você não precisará adicionar o segundo ponto de extremidade **não padrão** . O **updateResourceEndpoint** no serviço vinculado está no formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Você pode obter valores para colocar os contentores na URL ao consultar o serviço Web no [portal de serviços web Azure Machine Learning](https://services.azureml.net/). O novo tipo de ponto de extremidade de recurso de atualização requer um token AAD (Azure Active Directory). Especifique **servicePrincipalName** e **servicePrincipalKey**no serviço vinculado do AzureML. Consulte [como criar uma entidade de serviço e atribuir permissões para gerenciar o recurso do Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Aqui está um exemplo de definição de serviço vinculado do AzureML: 

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

O cenário a seguir fornece mais detalhes. Ele tem um exemplo para retreinar e atualizar modelos de ML do Azure de um pipeline Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Cenário: retreinando e atualizando um modelo do Azure ML
Esta seção fornece um pipeline de exemplo que usa a **atividade de execução em lote do am do Azure** para treinar novamente um modelo. O pipeline também usa a **atividade de recurso de atualização do am do Azure** para atualizar o modelo no serviço Web de pontuação. A seção também fornece trechos de código JSON para todos os serviços vinculados, conjuntos de itens e pipeline no exemplo.

Aqui está a exibição de diagrama do pipeline de exemplo. Como você pode ver, a atividade de execução em lote do am do Azure usa a entrada de treinamento e produz uma saída de treinamento (arquivo iLearner). A atividade de recurso de atualização do am do Azure usa essa saída de treinamento e atualiza o modelo no ponto de extremidade do serviço Web de pontuação. A atividade atualizar recurso não produz nenhuma saída. O placeholderBlob é apenas um conjunto de resultados de saída fictício exigido pelo serviço de Azure Data Factory para executar o pipeline.

![diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Serviço vinculado do armazenamento de BLOBs do Azure:
O armazenamento do Azure contém os seguintes dados:

* dados de treinamento. Os dados de entrada para o serviço Web de treinamento do Azure ML.  
* arquivo iLearner. A saída do serviço Web de treinamento do Azure ML. Esse arquivo também é a entrada para a atividade atualizar recurso.  

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

### <a name="training-input-dataset"></a>Conjunto de dados de entrada de treinamento:
O DataSet a seguir representa os dados de treinamento de entrada para o serviço Web de treinamento do Azure ML. A atividade de execução em lote do am do Azure usa esse conjunto de dados como uma entrada.

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

### <a name="training-output-dataset"></a>Conjunto de resultados de saída de treinamento:
O conjunto de resultados a seguir representa o arquivo de saída iLearner do serviço Web de treinamento do Azure ML. A atividade de execução em lote do am do Azure produz esse conjunto de DataSet. Esse conjunto de dados também é a entrada para a atividade de recurso de atualização do Azure ML.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Serviço vinculado para o ponto de extremidade de treinamento do Azure ML
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

No **Azure ml Studio**, faça o seguinte para obter valores para **mlEndpoint** e **apiKey**:

1. Clique em **Serviços Web** no menu à esquerda.
2. Clique no **serviço Web de treinamento** na lista de serviços Web.
3. Clique em copiar ao lado de caixa de texto **chave de API** . Cole a chave na área de transferência no Data Factory editor de JSON.
4. No **Azure ml Studio**, clique no link **execução em lote** .
5. Copie o **URI de solicitação** da seção **solicitação** e cole-o no editor data Factory JSON.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Serviço vinculado para o ponto de extremidade de Pontuação atualizável do Azure ML:
O trecho JSON a seguir define um Azure Machine Learning serviço vinculado que aponta para o ponto de extremidade atualizável não padrão do serviço Web de pontuação.  

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

### <a name="placeholder-output-dataset"></a>Conjunto de espaços de saída de espaço reservado:
A atividade de recurso de atualização do am do Azure não gera nenhuma saída. No entanto, Azure Data Factory requer um conjunto de uma saída para direcionar a agenda de um pipeline. Portanto, usamos um conjunto de um DataSet de cópia/espaço reservado neste exemplo.  

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
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução em lote do am do Azure usa os dados de treinamento como entrada e produz um arquivo iLearner como uma saída. A atividade invoca o serviço Web de treinamento (teste de treinamento exposto como um serviço Web) com os dados de treinamento de entrada e recebe o arquivo ilearner do WebService. O placeholderBlob é apenas um conjunto de resultados de saída fictício exigido pelo serviço de Azure Data Factory para executar o pipeline.

![diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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
