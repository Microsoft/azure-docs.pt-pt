---
title: Criar pipelines de dados Preditiva com o Azure Data Factory | Documentos da Microsoft
description: Descreve como criar crie pipelines previsíveis utilizando o Azure Data Factory e o Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6c71956bf43a0147d5abff4b708be93f62fe34fa
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545061"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Crie pipelines previsíveis utilizando o Azure Machine Learning e Azure Data Factory

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

## <a name="introduction"></a>Introdução
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [transformar dados com o machine learning no Data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[O Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe criar, testar e implementar soluções de Análise Preditiva. Do ponto de vista alto nível, é feita em três passos:

1. **Criar uma experimentação de preparação**. Efetue este passo com o Azure Machine Learning studio. O Azure Machine Learning studio é um ambiente de colaboração de desenvolvimento visual que utilizar para dar formação e testar um modelo de Análise Preditiva com dados de treinamento.
2. **Convertê-lo para uma experimentação preditiva**. Assim que o seu modelo foi treinado com os dados existentes e está pronto para usá-lo para pontuar novos dados, preparar e simplifique a sua experimentação para a classificação.
3. **Implementá-lo como um serviço web**. Pode publicar a sua experiência de classificação como um serviço web do Azure. Pode enviar dados para o seu modelo através deste ponto de final de serviço web e a receber de previsões de resultado para o modelo.

### <a name="azure-data-factory"></a>Azure Data Factory
O Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o **movimento** e a **transformação** de dados. Pode criar soluções de integração de dados com o Azure Data Factory que podem ingerir dados de vários arquivos de dados, transformar/processar os dados e publicar os dados resultantes nos arquivos de dados.

O serviço Data Factory permite-lhe criar pipelines de dados que movimentam e transformam dados e, depois, executar esses pipelines numa agenda especificada (hora a hora, diariamente, semanalmente, etc.). Também proporciona visualizações ricas para apresentar a linhagem e as dependências entre os pipelines de dados e monitorizá-los a partir de uma vista unificada única, para identificar facilmente problemas e configurar alertas de monitorização.

Ver [introdução ao Azure Data Factory](data-factory-introduction.md) e [crie seu primeiro pipeline](data-factory-build-your-first-pipeline.md) artigos para começar rapidamente com o serviço do Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Fábrica de dados e Machine Learning em conjunto
O Azure Data Factory permite-lhe facilmente criar pipelines que utilizem um publicados [do Azure Machine Learning] [ azure-machine-learning] web service para Análise Preditiva. Utilizar o **atividade de execução de Batch** num pipeline do Azure Data Factory, pode invocar um serviço web do Azure Machine Learning studio para fazer previsões sobre os dados no batch. Ver invocar um serviço web do Azure Machine Learning studio usando a seção de atividade de execução do Batch para obter detalhes.

Ao longo do tempo, os modelos preditivos no experiências de classificação do Azure Machine Learning studio necessário reestruturar utilizando conjuntos de dados de entrada novo. Pode voltar a preparar um modelo de studio do Azure Machine Learning a partir de um pipeline de fábrica de dados, efetuando os seguintes passos:

1. Publica a experimentação de preparação (experimentação preditiva não) como um serviço web. Efetue este passo no studio do Azure Machine Learning como fez para expor experimentação preditiva como um serviço web no cenário anterior.
2. Utilize o atividade de execução de lote do Azure Machine Learning studio para invocar o serviço web para a experimentação de preparação. Basicamente, pode utilizar o Azure Machine Learning studio atividade de execução de lotes para invocar serviço web de formação e serviço web de pontuação.

Depois de terminar com reparametrização, atualizar o serviço web de pontuação (experimentação preditiva exposto como um serviço web) com o modelo treinado recentemente e com o **atualizar atividade de recursos do Azure Machine Learning studio**. Ver [atualizar modelos com a atividade de recursos de atualização](data-factory-azure-ml-update-resource-activity.md) artigo para obter detalhes.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocar um serviço web com a atividade de execução do Batch
Utilizar o Azure Data Factory para orquestrar o movimento de dados e processamento e, em seguida, executar a execução de lote com o Azure Machine Learning. Eis os passos de nível superior:

1. Crie um serviço ligado do Azure Machine Learning. Terá dos seguintes valores:

   1. **URI do pedido** para a API de execução do Batch. Pode encontrar o URI do pedido ao clicar o **execução de lotes** link na página de serviços da web.
   2. **Chave de API** para a publicação do Azure Machine Learning web service. Pode encontrar a chave de API ao clicar o serviço web que publicou.
   3. Utilize o **AzureMLBatchExecution** atividade.

      ![Dashboard do Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![URI do batch](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário: Experimentações com o Web service entradas/saídas que se referem aos dados no armazenamento de Blobs do Azure
Neste cenário, o serviço Web do Azure Machine Learning faz predições com dados a partir de um ficheiro num armazenamento de Blobs do Azure e armazena os resultados da predição no armazenamento de Blobs. O seguinte JSON define um pipeline do Data Factory com uma atividade de AzureMLBatchExecution. A atividade possui o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como o resultado. O **DecisionTreeInputBlob** é passado como entrada para o serviço web utilizando o **webServiceInput** propriedade JSON. O **DecisionTreeResultBlob** é transmitido como uma saída para o serviço Web utilizando o **webServiceOutputs** propriedade JSON.

> [!IMPORTANT]
> Se o serviço web tem várias entradas, utilize o **webServiceInputs** propriedade em vez de usar **webServiceInput**. Consulte a [serviço Web requer várias entradas](#web-service-requires-multiple-inputs) secção para obter um exemplo de uso da propriedade webServiceInputs.
>
> Conjuntos de dados que são referenciados pela **webServiceInput**/**webServiceInputs** e **webServiceOutputs** propriedades (no  **typeProperties**) também têm de ser incluídos na atividade **entradas** e **produz**.
>
> Na sua experimentação do studio do Azure Machine Learning, entrada do serviço web e de portas de saída e de parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes que utiliza para definições de globalParameters, webServiceOutputs e webServiceInputs devem corresponder exatamente os nomes nas experimentações. Pode ver o payload de pedido de exemplo na página de ajuda de Execusão de Batch para o ponto final do Azure Machine Learning studio verificar o mapeamento esperado.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Apenas entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no fragmento JSON acima, DecisionTreeInputBlob for uma entrada para a atividade de AzureMLBatchExecution, que é passada como entrada para o serviço Web via parâmetro webServiceInput.
>
>

### <a name="example"></a>Exemplo
Este exemplo utiliza o armazenamento do Azure para manter tanto os dados de entrada e saídos.

Recomendamos que passe pela [criar seu primeiro pipeline com o Data Factory] [ adf-build-1st-pipeline] tutorial antes de realizar a este exemplo. Utilize o Editor do Data Factory para criar artefactos do Data Factory (serviços ligados, conjuntos de dados e pipeline) neste exemplo.

1. Criar uma **serviço ligado** para a sua **armazenamento do Azure**. Se os ficheiros de entrada e saídos estão nas contas de armazenamento diferente, terá dois serviços ligados. Eis um exemplo JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Criar a **entrada** do Azure Data Factory **conjunto de dados**. Ao contrário de alguns outros Data Factory conjuntos de dados, estes conjuntos de dados tem de conter ambos **folderPath** e **fileName** valores. Pode usar a criação de partições para fazer com que cada execução de lotes (cada setor de dados) processar ou produzir entrada exclusiva e ficheiros de saída. Se pretender incluir alguma atividade a montante para transformar a entrada em formato de ficheiro CSV e colocá-lo na conta de armazenamento para cada setor. Nesse caso, não incluiria o **externo** e **externalData** definições mostradas no exemplo a seguir e seu DecisionTreeInputBlob seria o conjunto de dados de saída de uma atividade diferente.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
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

    O ficheiro csv de entrada tem de ter a linha de cabeçalho de coluna. Se estiver a utilizar o **atividade de cópia** para criar/mover o csv para o armazenamento de BLOBs, deve definir a propriedade de sink **blobWriterAddHeader** para **verdadeiro**. Por exemplo:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Se o ficheiro csv não tiver a linha de cabeçalho, poderá ver o erro seguinte: **Erro na atividade: Cadeia de leitura de erro. Token inesperado: StartObject. Caminho ', linha 1, posicionar 1**.
3. Criar a **saída** do Azure Data Factory **conjunto de dados**. Este exemplo utiliza a criação de partições para criar um caminho de saída exclusivo para cada execução do setor. Sem a criação de partições, a atividade substituiria o ficheiro.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Criar uma **serviço ligado** do tipo: **AzureMLLinkedService**, fornecendo a chave de API e o URL da execução de batch de modelo.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Por fim, criar um pipeline que contém um **AzureMLBatchExecution** atividade. No tempo de execução, o pipeline executa os seguintes passos:

   1. Obtém a localização do ficheiro de entrada de seus conjuntos de dados de entrada.
   2. Invoca a execução de lote do Azure Machine Learning API
   3. Copia o resultado da execução de lote para o blob fornecido no seu conjunto de dados de saída.

      > [!NOTE]
      > Atividade de AzureMLBatchExecution pode ter zero ou mais entradas e saídas de um ou mais.
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      Ambos **começar** e **final** datetimes tem de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **final** tempo é opcional. Se não especificar valor para o **final** propriedade, esta é calculada como "**início + 48 horas.**" Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**. Veja [Referência de Processamento de Scripts JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes sobre as propriedades de JSON.

      > [!NOTE]
      > Especificação de entrada para o AzureMLBatchExecution atividade é opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário: Utilização de módulos de Leitor/gravador para fazer referência aos dados em vários armazenamentos de experimentações
Outro cenário comum ao criar experimentações do Azure Machine Learning studio consiste em utilizar o leitor e gravador de módulos. O módulo de leitor é utilizado para carregar dados para uma experimentação e o módulo de escritor é salvar dados a partir das suas experimentações. Para obter detalhes sobre o leitor e gravador de módulos, consulte [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) tópicos na biblioteca MSDN.

Ao utilizar os módulos do leitor e gravador, é recomendável usar um parâmetro de serviço da Web para cada propriedade estes módulos de Leitor/gravador. Estes parâmetros da web permitem-lhe configurar os valores durante o tempo de execução. Por exemplo, pode criar uma experimentação com um módulo leitor de que utiliza uma base de dados do SQL do Azure: XXX.database.windows.net. Depois de implementar o serviço web, que pretende permitir aos consumidores do serviço web especificar outro Azure SQL Server chamado YYY.database.windows.net. Pode utilizar um parâmetro de serviço da Web para permitir que este valor a ser configurado.

> [!NOTE]
> Entrada do serviço Web e de saída são diferentes dos parâmetros do serviço Web. O primeiro cenário, viu como uma entrada e saída podem ser especificados para um serviço Web do Azure Machine Learning studio. Neste cenário, transmita os parâmetros para um serviço Web que correspondem às propriedades dos módulos de Leitor/gravador.
>
>

Vamos examinar um cenário de utilização de parâmetros do serviço Web. Tem um serviço de web do Azure Machine Learning implementado que utiliza um módulo de leitor para ler dados a partir de uma das origens de dados suportadas pelo Azure Machine Learning (por exemplo: Base de dados SQL do Azure). Após ter sido efetuada a execução de lote, os resultados são escritos usando um módulo de escritor (base de dados do Azure SQL).  Não existem entradas de serviço da web e as saídas são definidas nas experimentações. Neste caso, recomendamos que configure parâmetros de serviço da web relevantes para os módulos do leitor e gravador. Esta configuração permite que o Leitor/gravador módulos de ser configurada ao utilizar a atividade de AzureMLBatchExecution. Especificar parâmetros do serviço Web na **globalParameters** secção no JSON de atividade da seguinte forma.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Também pode utilizar [funções de fábrica de dados](data-factory-functions-variables.md) em transmitir os valores para a Web service parâmetros, como mostrado no exemplo a seguir:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, por isso, certifique-se de que os nomes que especificar na atividade JSON corresponder aos perfis expostos pelo serviço Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Utilizar um módulo de leitor para ler os dados de vários ficheiros no Blob do Azure
Grandes volumes de dados, pipelines com atividades, tais como o Pig e Hive pode produzir um ou mais ficheiros de saída com nenhuma extensão. Por exemplo, quando especificar uma tabela do Hive externa, os dados de tabela do Hive externo podem ser armazenados no armazenamento de Blobs do Azure com o seguinte 000000_0 de nome. Pode utilizar o módulo de leitor numa experimentação para ler vários ficheiros e utilizá-los para previsões.

Ao utilizar o módulo leitor de uma experimentação do Azure Machine Learning, pode especificar o Blob do Azure como entrada. Os ficheiros no armazenamento de Blobs do Azure podem ser os ficheiros de saída (exemplo: 000000_0) que são produzidos por um script Pig e Hive em execução no HDInsight. O módulo de leitor permite-lhe ler arquivos (com nenhuma extensão) ao configurar o **caminho para o contentor, blob/diretório**. O **caminho para o contentor** aponta para o contentor e **directory/blob** aponta para a pasta que contém os ficheiros, conforme mostrado na imagem seguinte. O asterisco ou seja, \*) **Especifica que todos os ficheiros na pasta/contentor (ou seja, aggregateddata/dados/ano = 2014/mês-6 /\*)** são de leitura como parte da experimentação.

![Propriedades do Blob do Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline com atividade AzureMLBatchExecution com parâmetros do serviço Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning studio model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

No exemplo acima de JSON:

* O serviço Azure Machine Learning Web implementado utiliza um leitor e um módulo de escritor de dados de/para uma base de dados do SQL do Azure de leitura/escrita. Esse Web service expõe os seguintes quatro parâmetros:  Nome do servidor de base de dados, nome de base de dados, nome de conta de utilizador do servidor e palavra-passe de conta de utilizador Server.
* Ambos **começar** e **final** datetimes tem de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **final** tempo é opcional. Se não especificar valor para o **final** propriedade, esta é calculada como "**início + 48 horas.**" Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**. Veja [Referência de Processamento de Scripts JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes sobre as propriedades de JSON.

### <a name="other-scenarios"></a>Outros cenários
#### <a name="web-service-requires-multiple-inputs"></a>Serviço Web requer várias entradas
Se o serviço web tem várias entradas, utilize o **webServiceInputs** propriedade em vez de usar **webServiceInput**. Conjuntos de dados que são referenciados pela **webServiceInputs** também têm de ser incluídos na atividade **entradas**.

Na sua experimentação do studio do Azure Machine Learning, entrada do serviço web e de portas de saída e de parâmetros globais têm nomes predefinidos ("input1", "input2") que pode personalizar. Os nomes que utiliza para definições de globalParameters, webServiceOutputs e webServiceInputs devem corresponder exatamente os nomes nas experimentações. Pode ver o payload de pedido de exemplo na página de ajuda de Execusão de Batch para o ponto final do Azure Machine Learning studio verificar o mapeamento esperado.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Serviço Web não requer uma entrada
Serviços do Azure Machine Learning studio batch execução web podem ser utilizados para executar quaisquer fluxos de trabalho, para R ou Python scripts de exemplo, que não, requerer quaisquer entradas. Em alternativa, a experimentação pode ser configurada com um módulo de leitor não expõe qualquer GlobalParameters. Nesse caso, a atividade de AzureMLBatchExecution seria configurada da seguinte forma:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Serviço Web não requer uma entrada/saída
O serviço web do Azure Machine Learning studio batch execução poderá não ter qualquer saída de serviço da Web configurada. Neste exemplo, não há nenhuma entrada do serviço Web ou de saída, nem qualquer GlobalParameters configurados. Ainda há uma saída configurada na própria atividade, mas não é fornecido como um webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web Service utiliza leitores e gravadores e as execuções de atividade apenas quando outras atividades tiveram êxito
Os Azure Machine Learning web service leitor e gravador de módulos do studio podem ser configurados para ser executado com ou sem qualquer GlobalParameters. No entanto, convém incorporar chamadas de serviço num pipeline que utiliza as dependências do conjunto de dados para invocar o serviço apenas quando algum processamento a montante foi concluída. Também pode acionar qualquer outra ação depois de concluída a execução de lote usando essa abordagem. Nesse caso, pode expressar as dependências com entradas de atividade e saídas, sem nomear qualquer um deles como serviço Web de entrada ou saída.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

O **pedidas** são:

* Se o ponto final de experimentação utiliza um webServiceInput: ele é representado por um conjunto de dados do blob e está incluído nas entradas de atividade e a propriedade webServiceInput. Caso contrário, a propriedade webServiceInput for omitida.
* Se o ponto de final de experimentação utilizar webServiceOutput(s): eles são representados por conjuntos de dados do blob e estão incluídos nas saídas de atividade e na propriedade webServiceOutputs. A atividade produz e webServiceOutputs são mapeados com o nome de cada resultado na experimentação. Caso contrário, a propriedade webServiceOutputs for omitida.
* Se o ponto final de experimentação expõe globalParameter(s), eles são fornecidos na propriedade globalParameters atividade como chave, pares de valor. Caso contrário, a propriedade globalParameters for omitida. As chaves são maiúsculas e minúsculas. [As funções do Azure do Data Factory](data-factory-functions-variables.md) podem ser utilizados os valores.
* Conjuntos de dados adicionais podem ser incluídos nas propriedades de entradas e saídas de atividade, sem a ser referenciado em typeProperties a atividade. Estes conjuntos de dados regulam a utilização das dependências do setor de execução, mas, caso contrário, são ignorados pela atividade AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Atualizar Modelos com a atividade de recursos de atualização
Depois de terminar com reparametrização, atualizar o serviço web de pontuação (experimentação preditiva exposto como um serviço web) com o modelo treinado recentemente e com o **atualizar atividade de recursos do Azure Machine Learning studio**. Ver [atualizar modelos com a atividade de recursos de atualização](data-factory-azure-ml-update-resource-activity.md) artigo para obter detalhes.

### <a name="reader-and-writer-modules"></a>Leitor e gravador de módulos
Um cenário comum para a utilização de parâmetros do serviço Web é a utilização do Azure SQL leitores e gravadores. O módulo de leitor é utilizado para carregar dados para uma experimentação dos serviços de gestão de dados fora do Azure Machine Learning Studio. O módulo de escritor é salvar dados a partir das suas experimentações para os serviços de gestão de dados fora do Azure Machine Learning Studio.

Para obter detalhes sobre SQL do Blob/Azure do Azure. o Leitor/gravador, consulte [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) tópicos na biblioteca MSDN. O exemplo na secção anterior utilizou o leitor de Blobs do Azure e o gravador de Blobs do Azure. Esta secção aborda o uso do leitor de SQL do Azure e gravador de SQL do Azure.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**P:** Posso ter vários arquivos que são gerados pelo meu pipelines de macrodados. Pode utilizar a atividade de AzureMLBatchExecution funcione em todos os ficheiros?

**R:** Sim. Consulte a **utilização de um módulo de leitor para ler os dados de vários ficheiros no Blob do Azure** secção para obter detalhes.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>O Azure Machine Learning studio atividade de classificação de Batch
Se estiver a utilizar o **AzureMLBatchScoring** atividade para integrar com o Azure Machine Learning, recomendamos que utilize a versão mais recente **AzureMLBatchExecution** atividade.

A atividade de AzureMLBatchExecution é introduzida na versão de Agosto de 2015 do SDK do Azure e o Azure PowerShell.

Se pretender continuar a utilizar a atividade de AzureMLBatchScoring, continue lendo esta secção.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>O Azure Machine Learning studio atividade de classificação de lote de mensagens em fila com o armazenamento do Azure para a entrada/saída

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parâmetros do serviço Web
Para especificar valores para parâmetros do serviço Web, adicione uma **typeProperties** secção para o **AzureMLBatchScoringActivity** secção no pipeline JSON, conforme mostrado no exemplo a seguir:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Também pode utilizar [funções de fábrica de dados](data-factory-functions-variables.md) em transmitir os valores para a Web service parâmetros, como mostrado no exemplo a seguir:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, por isso, certifique-se de que os nomes que especificar na atividade JSON corresponder aos perfis expostos pelo serviço Web.
>
>

## <a name="see-also"></a>Consultar Também
* [Mensagem de blogue do Azure: Introdução ao Azure Data Factory e o Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
