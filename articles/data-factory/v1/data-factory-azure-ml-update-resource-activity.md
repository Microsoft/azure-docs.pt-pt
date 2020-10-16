---
title: Atualizar modelos de machine learning usando Azure Data Factory
description: Descreve como criar oleodutos preditivos usando Azure Data Factory v1 e Azure Machine Learning Studio (clássico)
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
ms.openlocfilehash: bed66ab8f3dc3db47b94070cbbeb64fb91163f8c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014465"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>Atualizar modelos do Azure Machine Learning Studio (clássico) usando a atividade de recurso de atualização

> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md) 
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [Atividade mapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Azure Machine Learning Studio (clássico) Atividade de execução de lote](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning Studio (clássico) Atualização De Recursos Atividade](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [os modelos de aprendizagem automática de atualização na Data Factory.](../update-machine-learning-models.md)

Este artigo complementa o principal artigo de integração do Azure Machine Learning Factory - Azure Machine Learning Studio (clássico): [Criar oleodutos preditivos utilizando o Azure Machine Learning Studio (clássico) e a Azure Data Factory.](data-factory-azure-ml-batch-execution-activity.md) Se ainda não o fez, reveja o artigo principal antes de ler este artigo. 

## <a name="overview"></a>Descrição geral
Com o tempo, os modelos preditivos no Azure Machine Learning Studio (clássico) experiências de pontuação precisam de ser retreinados usando novos conjuntos de dados de entrada. Depois de terminar a reconversão, pretende atualizar o serviço web de pontuação com o modelo ML retreinado. Os passos típicos para permitir a reconversão e atualização dos modelos studio (clássico) através de serviços web são:

1. Crie uma experiência no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net).
2. Quando estiver satisfeito com o modelo, use o Azure Machine Learning Studio (clássico) para publicar serviços web tanto para a **experiência de treino** como para a experiência de pontuação/preditiva .**predictive experiment**

A tabela seguinte descreve os serviços web utilizados neste exemplo.  Consulte [os modelos retrain Machine Learning Studio (clássico) programáticamente](../../machine-learning/studio/retrain-machine-learning-model.md) para obter detalhes.

- **Serviço web de formação** - Recebe dados de formação e produz modelos treinados. A saída da reconversão é um ficheiro .ilearner num armazém da Azure Blob. O **ponto final predefinido** é criado automaticamente para si quando publica a experiência de treino como um serviço web. Pode criar mais pontos finais, mas o exemplo utiliza apenas o ponto final predefinido.
- **Serviço web de pontuação** - Recebe exemplos de dados não rotulados e faz previsões. A saída da previsão pode ter várias formas, como um ficheiro .csv ou linhas na Base de Dados Azure SQL, dependendo da configuração da experiência. O ponto final predefinido é automaticamente criado para si quando publica a experiência preditiva como um serviço web. 

A imagem que se segue retrata a relação entre o treino e os pontos finais no Azure Machine Learning Studio (clássico).

![Serviços web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Pode invocar o **serviço web de formação** utilizando o **Azure Machine Learning Studio (clássico) Atividade de Execução de Lotes**. Invocar um serviço web de formação é o mesmo que invocar um serviço web Azure Machine Learning Studio (clássico) (serviço web de pontuação) para a pontuação de dados. As secções anteriores cobrem como invocar um serviço web Azure Machine Learning Studio (clássico) a partir de um oleoduto Azure Data Factory em detalhe. 

Pode invocar o **serviço web de pontuação** utilizando o **Azure Machine Learning Studio (clássico) Update Resource Activity** para atualizar o serviço web com o modelo recém-treinado. Os seguintes exemplos fornecem definições de serviço ligadas: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>O serviço web de pontuação é um serviço web clássico
Se o serviço web de pontuação for um **serviço web clássico,** crie o segundo **ponto final não padrão e updatable** utilizando o portal Azure. Consulte o artigo ['Criar pontos finais'.](../../machine-learning/studio/create-endpoint.md) Depois de criar o ponto final não padrão, faça os seguintes passos:

* Clique **em EXECUÇÃO DE LOTE** para obter o valor URI para a propriedade **mlEndpoint** JSON.
* Clique no link **RECURSO UPDATE** para obter o valor URI para a propriedade JSON de **atualização.** A tecla API está na própria página do ponto final (no canto inferior direito).

![ponto final updatable](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

O exemplo a seguir fornece uma definição JSON de amostra para o serviço ligado AzureML. O serviço ligado utiliza o apiKey para autenticação.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>O serviço web de pontuação é o serviço web Azure Resource Manager 
Se o serviço web for o novo tipo de serviço web que expõe um ponto final do Azure Resource Manager, não precisa de adicionar o segundo ponto final **não padrão.** A **actualizaçãoResourceEndpoint** no serviço ligado é do formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Pode obter valores para os detentores de lugares no URL ao consultar o serviço web no Portal de [Serviços Web Azure Machine Learning Studio (clássico).](https://services.azureml.net/) O novo tipo de ponto final de recurso de atualização requer um token AAD (Azure Ative Directory). Especifique **o serviçoPrincipalId** e **o serviçoPrincipalKey** no serviço ligado ao Estúdio (clássico). Veja [como criar o principal de serviço e atribuir permissões para gerir o recurso Azure.](../../active-directory/develop/howto-create-service-principal-portal.md) Aqui está uma definição de serviço ligada AzureML: 

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

O cenário seguinte fornece mais detalhes. Tem um exemplo para requalificar e atualizar modelos Studio (clássicos) de um oleoduto Azure Data Factory.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Cenário: requalificação e atualização de um modelo studio (clássico)
Esta secção fornece um pipeline de amostra que utiliza a atividade de execução de **lote Azure Machine Learning Studio (clássico)** para retreinar um modelo. O pipeline também utiliza a atividade do **Azure Machine Learning Studio (clássico) Update Resource** para atualizar o modelo no serviço web de pontuação. A secção também fornece snippets JSON para todos os serviços ligados, conjuntos de dados e pipeline no exemplo.

Aqui está a vista do diagrama do gasoduto de amostra. Como pode ver, a Atividade de Execução de Lotes studio (clássica) leva a entrada de treino e produz uma saída de formação (ficheiro iLearner). O Studio (clássico) Update Resource Activity toma esta saída de formação e atualiza o modelo no ponto final do serviço web de pontuação. A Atualização da Atividade de Recursos não produz qualquer saída. O espaço-reservadoBlob é apenas um conjunto de dados de saída falso que é exigido pelo serviço Azure Data Factory para executar o pipeline.

![diagrama de pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Serviço ligado ao armazenamento Azure Blob:
O Azure Storage contém os seguintes dados:

* dados de formação. Os dados de entrada para o serviço web de formação studio (clássico).  
* ficheiro iLearner. A saída do serviço web de formação Studio (clássico). Este ficheiro é também a entrada para a atividade de Recurso de atualização.  

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

### <a name="training-input-dataset"></a>Conjunto de dados de entrada de formação:
O conjunto de dados que se segue representa os dados de formação de entrada para o serviço web de formação studio (clássico). A atividade de execução de lote (clássica) toma este conjunto de dados como uma entrada.

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

### <a name="training-output-dataset"></a>Conjunto de dados de saída de formação:
O conjunto de dados que se segue representa o ficheiro iLearner de saída do serviço web de formação Azure Machine Learning Studio (clássico). O Azure Machine Learning Studio (clássico) Batch Execution Activity produz este conjunto de dados. Este conjunto de dados é também a entrada para a atividade do Azure Machine Learning Studio (clássico) Update Resource.

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

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Serviço ligado para estúdio (clássico) ponto final de treino
O seguinte snippet JSON define um serviço ligado studio (clássico) que aponta para o ponto final padrão do serviço web de formação.

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

No **Azure Machine Learning Studio (clássico)**, faça o seguinte para obter valores para **mlEndpoint** e **apiKey**:

1. Clique nos **SERVIÇOS WEB** no menu esquerdo.
2. Clique no **serviço web de formação** na lista de serviços web.
3. Clique na cópia ao lado da caixa de texto **chave API.** Cole a chave na pasta para o editor JSON da Data Factory.
4. No **Azure Machine Learning Studio (clássico)**, clique no link **DE EXECUÇÃO BATCH.**
5. Copie o **URI pedido** da secção **Request** e cole-o no editor JSON da Data Factory.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Linked Service for Studio (classic) updatable scoreing endpoint:
O seguinte snippet JSON define um serviço ligado studio (clássico) que aponta para o ponto final não padrão updatable do serviço web pontuação.  

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

### <a name="placeholder-output-dataset"></a>Conjunto de dados de saída do espaço reservado:
A atividade de Recurso de atualização do Estúdio (clássico) não gera qualquer saída. No entanto, a Azure Data Factory requer um conjunto de dados de saída para impulsionar o calendário de um oleoduto. Portanto, usamos um conjunto de dados falso/espaço reservado neste exemplo.  

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
O gasoduto tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução de lote Azure Machine Learning Studio (clássico) toma os dados de formação como entrada e produz um ficheiro iLearner como uma saída. A atividade invoca o serviço web de formação (experiência de formação exposta como um serviço web) com os dados de formação de entrada e recebe o ficheiro ilearner do serviço web. O espaço-reservadoBlob é apenas um conjunto de dados de saída falso que é exigido pelo serviço Azure Data Factory para executar o pipeline.

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
