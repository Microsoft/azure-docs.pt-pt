---
title: Criar gasodutos de dados preditivos
description: Aprenda a criar um pipeline preditivo utilizando a Azure Machine Learning - Atividade de Execução de Lotes na Fábrica de Dados Azure.
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/20/2019
ms.openlocfilehash: 44371c78a4d02588eef21aae5a4bba3d033763d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76029985"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Crie pipelines preditivos utilizando o Azure Machine Learning e o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versão atual](transform-data-using-machine-learning.md)

[O Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe construir, testar e implementar soluções de análise preditiva. Do ponto de vista de alto nível, é feito em três etapas:

1. **Criar uma experiência de treino.** Faça este passo utilizando o Azure Machine Learning Studio (clássico). O Azure Machine Learning Studio (clássico) é um ambiente de desenvolvimento visual colaborativo que utiliza para treinar e testar um modelo de análise preditiva utilizando dados de formação.
2. **Converta-o numa experiência preditiva.** Uma vez que o seu modelo tenha sido treinado com os dados existentes e esteja pronto para usá-lo para obter novos dados, prepare e agilize a sua experiência para pontuar.
3. **Implementá-lo como um serviço web.** Pode publicar a sua experiência de pontuação como um serviço web Azure. Pode enviar dados para o seu modelo através deste ponto final do serviço web e receber previsões de resultados do modelo.

### <a name="data-factory-and-machine-learning-together"></a>Fábrica de Dados e Machine Learning juntos
A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web [azure machine learning](https://azure.microsoft.com/documentation/services/machine-learning) publicado para análise preditiva. Utilizando a Atividade de Execução de **Lotes** num oleoduto Azure Data Factory, pode invocar um serviço web Azure Machine Learning Studio (clássico) para fazer previsões sobre os dados em lote.

Com o tempo, os modelos preditivos do Azure Machine Learning Studio (clássico) experiências de pontuação precisam de ser retreinados usando novos conjuntos de dados de entrada. Pode retreinar um modelo a partir de um gasoduto Data Factory fazendo os seguintes passos:

1. Publique a experiência de formação (não experiência preditiva) como um serviço web. Você faz este passo no Azure Machine Learning Studio (clássico) como fez para expor a experiência preditiva como um serviço web no cenário anterior.
2. Utilize o Azure Machine Learning Studio (clássico) Atividade de Execução de Lotes para invocar o serviço web para a experiência de treino. Basicamente, você pode usar a atividade de Execução de Lot (clássico) do Estúdio de Aprendizagem automática azure para invocar tanto o serviço web de treino como o serviço web de pontuação.

Depois de terminar a reconversão, atualize o serviço web de pontuação (experiência preditiva exposta como um serviço web) com o modelo recém-treinado utilizando o **Azure Machine Learning Studio (clássico) Update Resource Activity**. Consulte a atualização de modelos utilizando o artigo [de Atividade de Recursos Atualizados](update-machine-learning-models.md) para mais detalhes.

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado à Aprendizagem automática Azure

Cria um serviço ligado à **Aprendizagem automática Azure** para ligar um Serviço Web de Aprendizagem automática Azure a uma fábrica de dados Azure. O Serviço Ligado é utilizado pela Atividade de Execução de Lotes de Aprendizagem automática Azure e pela [Atividade de Recursos atualizados.](update-machine-learning-models.md)

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Consulte o artigo de [serviços ligados](compute-linked-services.md) à Compute para descrições sobre propriedades na definição JSON.

O Azure Machine Learning suporta tanto os Serviços Web Clássicos como os Novos Serviços Web para a sua experiência preditiva. Pode escolher o certo para usar na Data Factory. Para obter as informações necessárias para criar o https://services.azureml.netServiço Ligado à Aprendizagem automática Azure, vá até onde todos os seus (novos) Serviços Web e Serviços Web Clássicos estão listados. Clique no Serviço Web a que gostaria de aceder e clique na página **Consumir.** Copiar **Chave Primária** para propriedade **apiKey,** e Pedidos de **Lote** para propriedade **mlEndpoint.**

![Serviços Web de aprendizagem automática Azure](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Atividade de execução de lote de aprendizagem automática azure

O seguinte snippet JSON define uma atividade de execução de lote de aprendizagem automática Azure. A definição de atividade tem uma referência ao serviço ligado à Aprendizagem automática Azure que criou anteriormente.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Propriedade          | Descrição                              | Necessário |
| :---------------- | :--------------------------------------- | :------- |
| nome              | Nome da atividade no oleoduto     | Sim      |
| descrição       | Texto descrevendo o que a atividade faz.  | Não       |
| tipo              | Para a atividade U-SQL do Data Lake Analytics, o tipo de atividade é **AzureMLBatchExecution**. | Sim      |
| linkedServiceName | Serviços Ligados ao Serviço Ligado à Aprendizagem automática Azure. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md) | Sim      |
| webServiceInputs  | Teclado, pares de valor, mapeando os nomes de Inputs de Serviço Web de Aprendizagem automática Azure. A chave deve corresponder aos parâmetros de entrada definidos no serviço web de aprendizagem automática azure publicado. Valor é um par de propriedades Azure Storage Linked Services e FilePath especificando as localizações blob de entrada. | Não       |
| webServiceOutputs | Chave, pares de valor, mapeando os nomes de Saídas do Serviço Web de Aprendizagem automática Azure. A chave deve corresponder aos parâmetros de saída definidos no serviço web de aprendizagem automática azure publicado. Valor é um par de propriedades Azure Storage Linked Services e FilePath especificando as localizações blob de saída. | Não       |
| parâmetros globais  | Teclado, pares de valor a serem passados para o Azure Machine Learning Studio (clássico) Batch Execution Service endpoint. As teclas devem coincidir com os nomes dos parâmetros do serviço web de serviço definidos no serviço web Azure Machine Learning Studio (clássico). Os valores são passados na propriedade GlobalParameters do Azure Machine Learning Studio (clássico) pedido de execução de lotes | Não       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário 1: Experiências utilizando entradas/saídas do serviço Web que se referem a dados no Armazenamento de Blob Azure

Neste cenário, o serviço Web Azure Machine Learning faz previsões utilizando dados de um ficheiro num armazenamento de blob Azure e armazena a previsão resulta no armazenamento de blob. O seguinte JSON define um gasoduto data factory com uma atividade azureMLBatchExecution. Os dados de entrada e saída no Azure Blog Storage são referenciados utilizando um par LinkedName e FilePath. Na amostra O Serviço Linked de entradas e saídas é diferente, pode utilizar diferentes Serviços Linked para cada uma das suas entradas/saídas para data Factory para poder pegar nos ficheiros certos e enviar para o Azure Machine Learning Studio (clássico) Web Service.

> [!IMPORTANT]
> Na sua experiência Azure Machine Learning Studio (clássica), as portas de entrada e saída do serviço web, e os parâmetros globais têm nomes padrão ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e definições globalParameters devem corresponder exatamente aos nomes das experiências. Pode ver a carga útil do pedido de amostra na página de Ajuda de Execução de Lote para o seu Estúdio de Aprendizagem automática (clássico) para verificar o mapeamento esperado.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário 2: Experiências utilizando Módulos leitor/escritor para se referir a dados em vários armazenamentos
Outro cenário comum ao criar experiências do Azure Machine Learning Studio (clássico) é usar módulos de Dados de Importação e Dados de Saída. O módulo de dados de importação é usado para carregar dados numa experiência e o módulo De dados de saída é para guardar dados das suas experiências. Para mais detalhes sobre os dados de importação e módulos de dados de saída, consulte os tópicos de Dados de [Importação](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Dados de Saída](https://msdn.microsoft.com/library/azure/dn905984.aspx) na Biblioteca MSDN.

Ao utilizar os módulos de Dados de Importação e Dados de Saída, é uma boa prática utilizar um parâmetro de serviço Web para cada propriedade destes módulos. Estes parâmetros web permitem configurar os valores durante o tempo de execução. Por exemplo, pode criar uma experiência com um módulo de dados de importação que utiliza uma Base de Dados SQL Azure: XXX.database.windows.net. Depois de implementado o serviço web, pretende permitir aos consumidores do serviço web especificar `YYY.database.windows.net`outro Servidor Azure SQL chamado . Pode utilizar um parâmetro de serviço Web para permitir configurar este valor.

> [!NOTE]
> A entrada e saída do serviço web são diferentes dos parâmetros do serviço Web. No primeiro cenário, você viu como uma entrada e saída podem ser especificadas para um serviço Web Azure Machine Learning Studio (clássico). Neste cenário, passa por parâmetros para um serviço Web que corresponda a propriedades dos módulos de dados de importação/dados de saída.
>
>

Vamos olhar para um cenário para usar parâmetros de serviço Web. Tem um serviço web Azure Machine Learning implantado que utiliza um módulo de leitor para ler dados de uma das fontes de dados suportadas pela Azure Machine Learning (por exemplo: Base de Dados Azure SQL). Após a execução do lote, os resultados são escritos utilizando um módulo Writer (Base de Dados Azure SQL).  Nenhuma entrada e saídas do serviço web são definidas nas experiências. Neste caso, recomendamos que configure parâmetros de serviço web relevantes para os módulos de leitor e escritor. Esta configuração permite configurar os módulos leitor/escritor ao utilizar a atividade AzureMLBatchExecution. Especifica os parâmetros do serviço Web na secção **GlobalParâmetros** na atividade JSON da seguinte forma.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Os parâmetros do serviço Web são sensíveis a casos, por isso certifique-se de que os nomes que especifica na atividade JSON correspondem aos expostos pelo serviço Web.
>

Depois de terminar a reconversão, atualize o serviço web de pontuação (experiência preditiva exposta como um serviço web) com o modelo recém-treinado utilizando o **Azure Machine Learning Studio (clássico) Update Resource Activity**. Consulte a atualização de modelos utilizando o artigo [de Atividade de Recursos Atualizados](update-machine-learning-models.md) para mais detalhes.

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
