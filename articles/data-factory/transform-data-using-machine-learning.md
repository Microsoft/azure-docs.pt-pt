---
title: Criar pipelines de dados Preditiva com o Azure Data Factory | Documentos da Microsoft
description: Saiba como criar um pipeline preditivo com o Azure Machine Learning - atividade de execução do Batch no Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: fa156beba6d5eb1f7e10529d7f1287ab9a148024
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576570"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Crie pipelines previsíveis utilizando o Azure Machine Learning e Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versão atual](transform-data-using-machine-learning.md)

[O Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe criar, testar e implementar soluções de Análise Preditiva. Do ponto de vista alto nível, é feita em três passos:

1. **Criar uma experimentação de preparação**. Efetue este passo com o Azure Machine Learning studio. O Azure Machine Learning studio é um ambiente de colaboração de desenvolvimento visual que utilizar para dar formação e testar um modelo de Análise Preditiva com dados de treinamento.
2. **Convertê-lo para uma experimentação preditiva**. Assim que o seu modelo foi treinado com os dados existentes e está pronto para usá-lo para pontuar novos dados, preparar e simplifique a sua experimentação para a classificação.
3. **Implementá-lo como um serviço web**. Pode publicar a sua experiência de classificação como um serviço web do Azure. Pode enviar dados para o seu modelo através deste ponto de final de serviço web e receber previsões de resultado do modelo.

### <a name="data-factory-and-machine-learning-together"></a>Fábrica de dados e Machine Learning em conjunto
O Azure Data Factory permite-lhe facilmente criar pipelines que utilizem um publicados [Azure Machine Learning] [azure--] serviço web machine learning para Análise Preditiva. Utilizar o **atividade de execução de Batch** num pipeline do Azure Data Factory, pode invocar um serviço web do Azure Machine Learning studio para fazer previsões sobre os dados no batch.

Ao longo do tempo, os modelos preditivos no experiências de classificação do Azure Machine Learning studio necessário reestruturar utilizando conjuntos de dados de entrada novo. Pode voltar a preparar um modelo a partir de um pipeline de fábrica de dados, efetuando os seguintes passos:

1. Publica a experimentação de preparação (experimentação preditiva não) como um serviço web. Efetue este passo no studio do Azure Machine Learning como fez para expor experimentação preditiva como um serviço web no cenário anterior.
2. Utilize o atividade de execução de lote do Azure Machine Learning studio para invocar o serviço web para a experimentação de preparação. Basicamente, pode utilizar o Azure Machine Learning studio atividade de execução de lotes para invocar serviço web de formação e serviço web de pontuação.

Depois de terminar com reparametrização, atualizar o serviço web de pontuação (experimentação preditiva exposto como um serviço web) com o modelo treinado recentemente e com o **atualizar atividade de recursos do Azure Machine Learning studio**. Ver [atualizar modelos com a atividade de recursos de atualização](update-machine-learning-models.md) artigo para obter detalhes.

## <a name="azure-machine-learning-linked-service"></a>Serviço ligado do Machine Learning do Azure

Criar uma **do Azure Machine Learning** serviço ligado para ligar um serviço Web do Azure Machine Learning para uma fábrica de dados do Azure. O serviço ligado é utilizado pela atividade de execução de lote do Azure Machine Learning e [atividade de recursos de atualização](update-machine-learning-models.md).

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

Ver [serviços ligados de computação](compute-linked-services.md) artigo para obter descrições sobre as propriedades na definição de JSON.

O Azure Machine Learning suporta os serviços Web clássicos e novos serviços Web para a experimentação preditiva. Pode escolher a ferramenta correta para utilizar a partir do Data Factory. Para obter as informações necessárias para criar o serviço ligado do Azure Machine Learning, aceda a https://services.azureml.net, onde estão apresentados todos os seus serviços da Web (novo) e serviços Web clássicos. Clique no serviço da Web que pretende aceder e clique em **Consume** página. Cópia **chave primária** para **apiKey** propriedade, e **pedidos de Batch** para **mlEndpoint** propriedade.

![Serviços Web do Azure Machine Learning](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Atividade de execução de lote do Machine Learning do Azure

O fragmento JSON seguinte define uma atividade de execução de lote do Azure Machine Learning. Definição da atividade possui uma referência ao serviço ligado do Azure Machine Learning que criou anteriormente.

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
| nome              | Nome da atividade no pipeline     | Sim      |
| descrição       | Texto que descreve o que faz a atividade.  | Não       |
| tipo              | Para a atividade de U-SQL do Data Lake Analytics, é o tipo de atividade **AzureMLBatchExecution**. | Sim      |
| linkedServiceName | Serviço ligado do serviços ligados para o Azure Machine Learning. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| webServiceInputs  | Chaves, pares de valor, mapear os nomes das entradas de serviço Web do Azure Machine Learning. Tem de corresponder os parâmetros de entrada definidos no publicados do Azure Machine Learning Web Service. O valor é um par de propriedades de serviços ligados do armazenamento do Azure e o caminho do ficheiro, especificar as localizações de Blob de entrada. | Não       |
| webServiceOutputs | Chaves, pares de valor, mapear os nomes das saídas de serviço Web do Azure Machine Learning. Tem de corresponder os parâmetros de saída definidos no publicados do Azure Machine Learning Web Service. O valor é um serviços ligados do armazenamento do Azure e o caminho do ficheiro localizações de Blobs do par de propriedades, especificando a saída. | Não       |
| globalParameters  | Pares de valores chaves, a serem passados para o ponto de extremidade de serviço de execução de lote do Azure Machine Learning studio. Chaves têm de corresponder os nomes dos parâmetros do serviço web definidos no serviço de web do Azure Machine Learning studio publicado. Valores são transmitidos na propriedade GlobalParameters da solicitação de execução de lote do Azure Machine Learning studio | Não       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário 1: Experimentações com o Web service entradas/saídas que se referem aos dados no armazenamento de Blobs do Azure

Neste cenário, o serviço Web do Azure Machine Learning faz predições com dados a partir de um ficheiro num armazenamento de Blobs do Azure e armazena os resultados da predição no armazenamento de Blobs. O seguinte JSON define um pipeline do Data Factory com uma atividade de AzureMLBatchExecution. Os dados de entrada e saída no armazenamento do blogue do Azure são referenciados com um par de LinkedName e caminho do ficheiro. No exemplo de serviço ligado de entradas e saídas são diferentes, pode utilizar diferentes serviços ligados para cada uma das suas entradas/saídas da fábrica de dados para ser capaz de aprender os arquivos certos e enviar para o serviço Web do Azure Machine Learning studio.

> [!IMPORTANT]
> Na sua experimentação do Azure Machine Learning studio, a entrada do serviço web e a saída portas e parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes que utiliza para definições de globalParameters, webServiceOutputs e webServiceInputs devem corresponder exatamente os nomes nas experimentações. Pode ver o payload de pedido de exemplo na página de ajuda de Execusão de Batch para o ponto final do Azure Machine Learning studio verificar o mapeamento esperado.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário 2: Utilização de módulos de Leitor/gravador para fazer referência aos dados em vários armazenamentos de experimentações
Outro cenário comum ao criar experimentações do Azure Machine Learning studio é usar módulos importar dados e dados de saída. O módulo de importação de dados é utilizado para carregar dados para uma experimentação e o módulo de dados de saída é salvar dados a partir das suas experimentações. Para obter detalhes sobre os módulos importar dados e dados de saída, consulte [importar dados](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [dados de saída](https://msdn.microsoft.com/library/azure/dn905984.aspx) tópicos na biblioteca MSDN.

Ao utilizar os módulos importar dados e dados de saída, é recomendável usar um parâmetro de serviço da Web para cada propriedade de um desses módulos. Estes parâmetros da web permitem-lhe configurar os valores durante o tempo de execução. Por exemplo, pode criar uma experimentação com um módulo de importação de dados que utiliza uma base de dados do SQL do Azure: XXX.database.windows.net. Depois de implementar o serviço web, o que pretende permitir aos consumidores do serviço web especificar outro Azure SQL Server chamado `YYY.database.windows.net`. Pode utilizar um parâmetro de serviço da Web para permitir que este valor a ser configurado.

> [!NOTE]
> Entrada do serviço Web e de saída são diferentes dos parâmetros do serviço Web. O primeiro cenário, viu como uma entrada e saída podem ser especificados para um serviço Web do Azure Machine Learning studio. Neste cenário, transmita os parâmetros para um serviço Web que correspondem às propriedades dos módulos de importar dados / saída de dados.
>
>

Vamos examinar um cenário de utilização de parâmetros do serviço Web. Tem um serviço de web do Azure Machine Learning implementado que utiliza um módulo de leitor para ler dados a partir de uma das origens de dados suportadas pelo Azure Machine Learning (por exemplo: Base de dados SQL do Azure). Após ter sido efetuada a execução de lote, os resultados são escritos usando um módulo de escritor (base de dados do Azure SQL).  Não existem entradas de serviço da web e as saídas são definidas nas experimentações. Neste caso, recomendamos que configure parâmetros de serviço da web relevantes para os módulos do leitor e gravador. Esta configuração permite que o Leitor/gravador módulos de ser configurada ao utilizar a atividade de AzureMLBatchExecution. Especificar parâmetros do serviço Web na **globalParameters** secção no JSON de atividade da seguinte forma.

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
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, por isso, certifique-se de que os nomes que especificar na atividade JSON corresponder aos perfis expostos pelo serviço Web.
>

Depois de terminar com reparametrização, atualizar o serviço web de pontuação (experimentação preditiva exposto como um serviço web) com o modelo treinado recentemente e com o **atualizar atividade de recursos do Azure Machine Learning studio**. Ver [atualizar modelos com a atividade de recursos de atualização](update-machine-learning-models.md) artigo para obter detalhes.

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
