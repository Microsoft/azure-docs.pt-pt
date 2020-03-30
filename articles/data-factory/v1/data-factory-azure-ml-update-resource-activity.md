---
title: Atualizar modelos de aprendizagem automática usando a Fábrica de Dados Azure
description: Descreve como criar oleodutos preditivos usando a Azure Data Factory e o Azure Machine Learning
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
ms.openlocfilehash: afc79badd19fa180e631f1f8fa9735567a0b1e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978718"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Atualizar os modelos de Aprendizagem automática Azure utilizando a Atividade de Recursos Atualizados

> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md) 
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [MapReduce Atividade](data-factory-map-reduce.md)
> * [Atividade de streaming de hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte modelos de [aprendizagem automática atualizados na Data Factory](../update-machine-learning-models.md).

Este artigo complementa o principal artigo de integração da Azure Data Factory - Azure Machine Learning: [Create predive pipelines utilizando a Azure Machine Learning e a Azure Data Factory.](data-factory-azure-ml-batch-execution-activity.md) Se ainda não o fez, reveja o artigo principal antes de ler este artigo. 

## <a name="overview"></a>Descrição geral
Com o tempo, os modelos preditivos nas experiências de pontuação do Azure ML precisam de ser retreinados utilizando novos conjuntos de dados de entrada. Depois de terminar a reconversão, pretende atualizar o serviço web de pontuação com o modelo ML retreinado. Os passos típicos para permitir a reconversão e atualização dos modelos Azure ML através de serviços web são:

1. Crie uma experiência no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net).
2. Quando estiver satisfeito com o modelo, use o Azure Machine Learning Studio (clássico) para publicar serviços web tanto para a experiência de **treino** como para a experiência de pontuação/**preditiva**.

A tabela seguinte descreve os serviços web utilizados neste exemplo.  Consulte os [modelos Retrain Machine Learning programáticamente](../../machine-learning/machine-learning-retrain-models-programmatically.md) para obter mais detalhes.

- Serviço web de **formação** - Recebe dados de formação e produz modelos treinados. A saída da reconversão é um ficheiro .ilearner num armazenamento Azure Blob. O **ponto final padrão** é automaticamente criado para si quando publica a experiência de treino como um serviço web. Pode criar mais pontos finais, mas o exemplo utiliza apenas o ponto final predefinido.
- **Pontuação do serviço web** - Recebe exemplos de dados não rotulados e faz previsões. A saída de previsão pode ter vários formulários, tais como um ficheiro .csv ou linhas numa base de dados Azure SQL, dependendo da configuração da experiência. O ponto final padrão é automaticamente criado para si quando publica a experiência preditiva como um serviço web. 

A imagem que se segue retrata a relação entre o treino e a pontuação final no Azure ML.

![Serviços web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Pode invocar o **serviço web** de treino utilizando a Atividade de Execução de **Lotes Azure ML.** Invocar um serviço web de formação é o mesmo que invocar um serviço web Azure ML (pontuação do serviço web) para a pontuação de dados. As secções anteriores cobrem como invocar um serviço web Azure ML a partir de um oleoduto Azure Data Factory em detalhe. 

Pode invocar o serviço web de **pontuação** utilizando a Atividade de Recursos de **Atualização Do Azure ML** para atualizar o serviço web com o modelo recém-treinado. Os seguintes exemplos fornecem definições de serviço sem ligação: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Pontuar o serviço web é um serviço web clássico
Se o serviço web de pontuação for um **serviço web clássico,** crie o segundo **ponto final não predefinido e atualizável** utilizando o portal Azure. Consulte o artigo Create Endpoints para [passos.](../../machine-learning/machine-learning-create-endpoint.md) Depois de criar o ponto final não indevido, faça os seguintes passos:

* Clique em **EXECUÇÃO DE LOTE** para obter o valor URI para a propriedade **mlEndpoint** JSON.
* Clique na ligação **DE RECURSOS DE ATUALIZAÇÃO** para obter o valor URI para a propriedade JSON de **atualizaçãoResourceEndpoint.** A tecla API está na própria página do ponto final (no canto inferior direito).

![ponto final updatable](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

O exemplo seguinte fornece uma definição JSON de amostra para o serviço ligado ao AzureML. O serviço ligado utiliza o apiKey para autenticação.  

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
Se o serviço web for o novo tipo de serviço web que expõe um ponto final do Gestor de Recursos Azure, não precisa de adicionar o segundo ponto final **não predefinido.** A **actualizaçãoResourceEndpoint** no serviço ligado é do formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Pode obter valores para os suportes de lugares no URL ao consultar o serviço web no Portal de Serviços Web de [Aprendizagem automática Azure](https://services.azureml.net/). O novo tipo de ponto final de recurso de atualização requer um símbolo aAD (Azure Ative Directory). Especifique o **serviçoPrincipalId** e **o serviçoPrincipalKey** no serviço ligado à Aprendizagem automática Azure. Veja [como criar o diretor de serviço e atribuir permissões para gerir o recurso Azure](../../active-directory/develop/howto-create-service-principal-portal.md). Aqui está uma amostra de definição de serviço ligada azureML: 

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

O seguinte cenário fornece mais detalhes. Tem um exemplo para retreinar e atualizar os modelos Azure ML a partir de um oleoduto Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Cenário: reconversão e atualização de um modelo Azure ML
Esta secção fornece um gasoduto de amostra que utiliza a atividade de execução de **lote De Lote Azure ML** para retreinar um modelo. O pipeline também utiliza a atividade de Recursos de **Atualização Azure ML** para atualizar o modelo no serviço web de pontuação. A secção também fornece snippets JSON para todos os serviços, conjuntos de dados e pipeline ligados no exemplo.

Aqui está a visão do diagrama do gasoduto de amostra. Como pode ver, a Atividade de Execução de Lotes Azure ML recebe a entrada de formação e produz uma saída de formação (ficheiro iLearner). A Atividade de Recursos de Atualização Azure ML leva esta saída de formação e atualiza o modelo no ponto final do serviço web de pontuação. A Atividade de Recursos atualizados não produz qualquer saída. O placeholderBlob é apenas um conjunto de dados de saída de bonecos que é exigido pelo serviço Azure Data Factory para executar o pipeline.

![diagrama de gasoduto](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Serviço ligado ao armazenamento Azure Blob:
O Armazenamento Azure detém os seguintes dados:

* dados de formação. Os dados de entrada para o serviço web de formação Azure ML.  
* ficheiro iLearner. A saída do serviço web de treino Azure ML. Este ficheiro é também a entrada na atividade do Recurso de Atualização.  

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

### <a name="training-input-dataset"></a>Conjunto de dados de entrada de formação:
O conjunto de dados que se segue representa os dados de formação de entrada para o serviço web de formação de Machine Learning Azure. A atividade de execução de lotes de aprendizagem automática Azure toma este conjunto de dados como uma entrada.

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
O conjunto de dados seguinte representa o ficheiro iLearner de saída do serviço web de treino Azure ML. A Atividade de Execução de Lotes Azure ML produz este conjunto de dados. Este conjunto de dados é também a entrada na atividade de Recursos de Atualização Do Azure ML.

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

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Serviço ligado para o endpoint de formação de machine learning Azure
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

No **Azure Machine Learning Studio (clássico)** faça o seguinte para obter valores para **mlEndpoint** e **apiKey:**

1. Clique em **WEB SERVICES** no menu esquerdo.
2. Clique no serviço web de **formação** na lista de serviços web.
3. Clique na cópia ao lado da caixa de texto **chave API.** Colhe a chave na pasta no editor jSON da Fábrica de Dados.
4. No Estúdio de **Aprendizagem automática Azure (clássico)** clique no link **BATCH EXECUTION.**
5. Copie o **Pedido URI** da secção **Solicitar** e cole-o no editor da Fábrica de Dados JSON.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Serviço ligado para ponto final de pontuação updatable Azure ML:
O seguinte snippet JSON define um serviço ligado à Aprendizagem automática Azure que aponta para o ponto final não padrão do serviço web de pontuação.  

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

### <a name="placeholder-output-dataset"></a>Conjunto de dados de saída de espaço reservado:
A atividade de atualização azure ML não gera qualquer saída. No entanto, a Azure Data Factory requer um conjunto de dados de saída para impulsionar o calendário de um oleoduto. Portanto, usamos um conjunto de dados de manequim/espaço reservado neste exemplo.  

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
O gasoduto tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. A atividade de execução de lote de MiO Azure toma os dados de formação como entrada e produz um ficheiro iLearner como uma saída. A atividade invoca o serviço web de formação (experiência de formação exposta como um serviço web) com os dados de formação de entrada e recebe o ficheiro ilearner do serviço web. O placeholderBlob é apenas um conjunto de dados de saída de bonecos que é exigido pelo serviço Azure Data Factory para executar o pipeline.

![diagrama de gasoduto](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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
