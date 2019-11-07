---
title: Criar pipelines de dados de previsão usando Azure Data Factory
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
ms.openlocfilehash: eba5df587d6bd6dda6083314cfb94836c6669393
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683147"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Criar pipelines preditivas usando Azure Machine Learning e Azure Data Factory

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

## <a name="introduction"></a>Introdução
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [transformar dados usando o Machine Learning no data Factory](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite criar, testar e implantar soluções de análise preditiva. De um ponto de vista de alto nível, isso é feito em três etapas:

1. **Crie um teste de treinamento**. Você faz essa etapa usando o Azure Machine Learning Studio. O Azure Machine Learning Studio é um ambiente de desenvolvimento Visual colaborativo que você usa para treinar e testar um modelo de análise preditiva usando dados de treinamento.
2. **Converta-o em um experimento de previsão**. Depois que o modelo tiver sido treinado com os dados existentes e você estiver pronto para usá-lo para pontuar novos dados, prepare e simplifique seu experimento para pontuação.
3. **Implante-o como um serviço Web**. Você pode publicar seu experimento de Pontuação como um serviço Web do Azure. Você pode enviar dados para seu modelo por meio deste ponto de extremidade de serviço Web e receber previsões de resultado partir o modelo.

### <a name="azure-data-factory"></a>Azure Data Factory
O Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o **movimento** e a **transformação** de dados. Você pode criar soluções de integração de dados usando Azure Data Factory que podem ingerir dados de vários armazenamentos de dados, transformar/processar os dados e publicar os dados de resultado nos armazenamentos de dados.

O serviço Data Factory permite-lhe criar pipelines de dados que movimentam e transformam dados e, depois, executar esses pipelines numa agenda especificada (hora a hora, diariamente, semanalmente, etc.). Também proporciona visualizações ricas para apresentar a linhagem e as dependências entre os pipelines de dados e monitorizá-los a partir de uma vista unificada única, para identificar facilmente problemas e configurar alertas de monitorização.

Consulte [introdução ao Azure data Factory](data-factory-introduction.md) e [crie seus primeiros](data-factory-build-your-first-pipeline.md) artigos de pipeline para começar rapidamente a usar o serviço de Azure data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory e Machine Learning em conjunto
Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web publicado [Azure Machine Learning][azure-machine-learning] para análise preditiva. Usando a **atividade de execução em lote** em um pipeline Azure data Factory, você pode invocar um serviço web do Azure Machine Learning Studio para fazer previsões sobre os dados no lote. Consulte invocando um serviço Web Azure Machine Learning Studio usando a seção atividade de execução em lote para obter detalhes.

Ao longo do tempo, os modelos de previsão no Azure Machine Learning Studio pontuando experimentos precisam ser retreinados usando novos conjuntos de dados de entrada. Você pode treinar novamente um modelo do Azure Machine Learning Studio de um pipeline de Data Factory executando as seguintes etapas:

1. Publique o teste de treinamento (sem experimentos de previsão) como um serviço Web. Você faz essa etapa no Azure Machine Learning Studio como fez para expor o experimento de previsão como um serviço Web no cenário anterior.
2. Use a atividade de execução em lote do Azure Machine Learning Studio para invocar o serviço Web para o teste de treinamento. Basicamente, você pode usar a atividade de execução em lote do Azure Machine Learning Studio para invocar o serviço Web de treinamento e o serviço Web de pontuação.

Depois de concluir o treinamento, atualize o serviço Web de Pontuação (teste de previsão exposto como um serviço Web) com o modelo treinado recentemente usando a atividade de **recurso de atualização do Azure Machine Learning Studio**. Veja [atualizando modelos usando o artigo atualizar atividade de recursos](data-factory-azure-ml-update-resource-activity.md) para obter detalhes.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocar um serviço Web usando a atividade de execução em lote
Você usa Azure Data Factory para orquestrar a movimentação e o processamento de dados e, em seguida, executar a execução em lote usando Azure Machine Learning. Aqui estão as etapas de nível superior:

1. Crie um Azure Machine Learning serviço vinculado. Você precisa dos seguintes valores:

   1. **URI de solicitação** para a API de execução em lote. Você pode encontrar o URI de solicitação clicando no link **execução em lote** na página serviços Web.
   2. **Chave de API** para o serviço web do Azure Machine Learning publicado. Você pode encontrar a chave de API clicando no serviço Web que você publicou.
   3. Use a atividade **AzureMLBatchExecution** .

      ![Painel do Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![URI do lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário: experimentos usando entradas/saídas do serviço Web que se referem aos dados no armazenamento de BLOBs do Azure
Nesse cenário, o serviço Web Azure Machine Learning faz previsões usando dados de um arquivo em um armazenamento de BLOBs do Azure e armazena os resultados da previsão no armazenamento de BLOBs. O JSON a seguir define um pipeline de Data Factory com uma atividade AzureMLBatchExecution. A atividade tem o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como a saída. O **DecisionTreeInputBlob** é passado como uma entrada para o serviço Web usando a propriedade JSON **webServiceInput** . O **DecisionTreeResultBlob** é passado como uma saída para o serviço Web usando a propriedade JSON **webServiceOutputs** .

> [!IMPORTANT]
> Se o serviço Web usa várias entradas, use a propriedade **webServiceInputs** em vez de usar **webServiceInput**. Consulte a seção [serviço Web requer várias entradas](#web-service-requires-multiple-inputs) para obter um exemplo de como usar a propriedade webServiceInputs.
>
> Os conjuntos de resultados que são referenciados pelas propriedades **webServiceInput**/**webServiceInputs** e **webServiceOutputs** (em **typeproperties**) também devem ser incluídos nas **entradas** e **saídas**da atividade.
>
> No experimento do Azure Machine Learning Studio, as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("entrada1", "entrada2") que você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webServiceOutputs e globalparameters devem corresponder exatamente aos nomes nos experimentos. Você pode exibir o conteúdo de solicitação de exemplo na página de ajuda de execução do lote para o ponto de extremidade do Azure Machine Learning Studio para verificar o mapeamento esperado.
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
> Somente as entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no trecho JSON acima, DecisionTreeInputBlob é uma entrada para a atividade AzureMLBatchExecution, que é passada como uma entrada para o serviço Web por meio do parâmetro webServiceInput.
>
>

### <a name="example"></a>Exemplo
Este exemplo usa o armazenamento do Azure para manter os dados de entrada e saída.

Recomendamos que você passe pelo tutorial [criar seu primeiro pipeline com data Factory antes de][adf-build-1st-pipeline] passar por este exemplo. Use o editor de Data Factory para criar artefatos Data Factory (serviços vinculados, conjuntos de valores, Pipeline) neste exemplo.

1. Crie um **serviço vinculado** para o **armazenamento do Azure**. Se os arquivos de entrada e saída estiverem em contas de armazenamento diferentes, você precisará de dois serviços vinculados. Aqui está um exemplo de JSON:

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
2. Crie o **conjunto**de dados de Azure data Factory de **entrada** . Ao contrário de alguns outros conjuntos de Data Factory, esses conjuntos de datadevem conter os valores **FolderPath** e **filename** . Você pode usar o particionamento para fazer com que cada execução em lotes (cada fatia de dados) processe ou produza arquivos de entrada e saída exclusivos. Talvez seja necessário incluir algumas atividades upstream para transformar a entrada no formato de arquivo CSV e colocá-la na conta de armazenamento para cada fatia. Nesse caso, você não incluirá as configurações de **externalData** e **externas** mostradas no exemplo a seguir, e seu DecisionTreeInputBlob seria o conjunto de resultados de saída de uma atividade diferente.

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

    O arquivo CSV de entrada deve ter a linha de cabeçalho da coluna. Se você estiver usando a **atividade de cópia** para criar/mover o CSV para o armazenamento de BLOBs, defina a propriedade de coletor **blobWriterAddHeader** como **true**. Por exemplo:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Se o arquivo CSV não tiver a linha de cabeçalho, você poderá ver o seguinte erro: **erro na atividade: erro ao ler a cadeia de caracteres. Token inesperado: StartObject. Caminho ' ', linha 1, posição 1**.
3. Crie o **conjunto**de Azure data Factory de **saída** . Este exemplo usa o particionamento para criar um caminho de saída exclusivo para cada execução de fatia. Sem o particionamento, a atividade substituiria o arquivo.

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
4. Crie um **serviço vinculado** do tipo: **AzureMLLinkedService**, fornecendo a chave de API e a URL de execução de lote do modelo.

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
5. Por fim, crie um pipeline que contenha uma atividade **AzureMLBatchExecution** . Em tempo de execução, o pipeline executa as seguintes etapas:

   1. Obtém o local do arquivo de entrada dos conjuntos de dados de entrada.
   2. Invoca a API de execução de Azure Machine Learning lote
   3. Copia a saída de execução em lotes para o blob fornecido em seu conjunto de dados de saída.

      > [!NOTE]
      > A atividade AzureMLBatchExecution pode ter zero ou mais entradas e uma ou mais saídas.
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

      Os DateTimes de **início** e **término** devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **término** é opcional. Se você não especificar o valor para a propriedade **end** , ele será calculado como "**Start + 48 hours".** Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**. Veja [Referência de Processamento de Scripts JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes sobre as propriedades de JSON.

      > [!NOTE]
      > A especificação da entrada para a atividade AzureMLBatchExecution é opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário: experimentos usando módulos de leitor/gravador para se referir a dados em vários armazenamentos
Outro cenário comum na criação de experimentos Azure Machine Learning Studio é usar módulos de leitor e gravador. O módulo leitor é usado para carregar dados em um experimento e o módulo gravador é para salvar dados de seus experimentos. Para obter detalhes sobre os módulos de leitor e gravador, consulte os tópicos de [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [gravador](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN.

Ao usar os módulos leitor e gravador, é uma boa prática usar um parâmetro de serviço Web para cada propriedade desses módulos de leitor/gravador. Esses parâmetros da Web permitem que você configure os valores durante o tempo de execução. Por exemplo, você pode criar um experimento com um módulo leitor que usa um banco de dados SQL do Azure: XXX.database.windows.net. Depois que o serviço Web for implantado, você deseja habilitar os consumidores do serviço Web para especificar outro SQL Server do Azure chamado YYY.database.windows.net. Você pode usar um parâmetro de serviço Web para permitir que esse valor seja configurado.

> [!NOTE]
> A entrada e saída do serviço Web são diferentes dos parâmetros do serviço Web. No primeiro cenário, você viu como uma entrada e saída podem ser especificadas para um serviço Web Azure Machine Learning Studio. Nesse cenário, você passa parâmetros para um serviço Web que corresponde às propriedades de módulos de leitor/gravador.
>
>

Vejamos um cenário para usar parâmetros de serviço Web. Você tem um serviço Web Azure Machine Learning implantado que usa um módulo leitor para ler dados de uma das fontes de dados com suporte do Azure Machine Learning (por exemplo: banco de dado SQL do Azure). Depois que a execução do lote é executada, os resultados são gravados usando um módulo gravador (banco de dados SQL do Azure).  Nenhuma entrada e saída de serviço Web é definida nos experimentos. Nesse caso, recomendamos que você configure os parâmetros de serviço Web relevantes para os módulos leitor e gravador. Essa configuração permite que os módulos de leitor/gravador sejam configurados ao usar a atividade AzureMLBatchExecution. Você especifica parâmetros de serviço Web na seção **globalparameters** na atividade JSON da seguinte maneira.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Você também pode usar [Data Factory funções](data-factory-functions-variables.md) na passagem de valores para os parâmetros do serviço Web, conforme mostrado no exemplo a seguir:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, certifique-se de que os nomes que você especificar na atividade JSON correspondam àqueles expostos pelo serviço Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Usando um módulo leitor para ler dados de vários arquivos no blob do Azure
Pipelines de Big data com atividades como Pig e Hive podem produzir um ou mais arquivos de saída sem extensões. Por exemplo, quando você especifica uma tabela externa do hive, os dados da tabela externa do hive podem ser armazenados no armazenamento de BLOBs do Azure com o seguinte nome 000000_0. Você pode usar o módulo leitor em um experimento para ler vários arquivos e usá-los para previsões.

Ao usar o módulo leitor em um experimento Azure Machine Learning, você pode especificar o blob do Azure como uma entrada. Os arquivos no armazenamento de BLOBs do Azure podem ser os arquivos de saída (exemplo: 000000_0) que são produzidos por um script Pig e Hive em execução no HDInsight. O módulo leitor permite que você leia arquivos (sem extensões) configurando o **caminho para o contêiner, o diretório/blob**. O **caminho para o contêiner** aponta para o contêiner e o **diretório/blob** aponta para a pasta que contém os arquivos, conforme mostrado na imagem a seguir. O asterisco, \*) **especifica que todos os arquivos no contêiner/pasta (ou seja, data/aggregateddata/ano = 2014/month-6/\*)** são lidos como parte do experimento.

![Propriedades de blob do Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline com atividade AzureMLBatchExecution com parâmetros de serviço Web

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

No exemplo de JSON acima:

* O serviço Web Azure Machine Learning implantado usa um módulo leitor e gravador para ler/gravar dados de/para um banco de dado SQL do Azure. Esse serviço Web expõe os quatro parâmetros a seguir: nome do servidor de banco de dados, nome do banco de dados, nome da conta de usuário do servidor e senha da conta de usuário do servidor.
* Os DateTimes de **início** e **término** devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **término** é opcional. Se você não especificar o valor para a propriedade **end** , ele será calculado como "**Start + 48 hours".** Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**. Veja [Referência de Processamento de Scripts JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes sobre as propriedades de JSON.

### <a name="other-scenarios"></a>Outros cenários
#### <a name="web-service-requires-multiple-inputs"></a>O serviço Web requer várias entradas
Se o serviço Web usa várias entradas, use a propriedade **webServiceInputs** em vez de usar **webServiceInput**. Os conjuntos de valores que são referenciados pelo **webServiceInputs** também devem ser incluídos nas **entradas**da atividade.

No experimento do Azure Machine Learning Studio, as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("entrada1", "entrada2") que você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webServiceOutputs e globalparameters devem corresponder exatamente aos nomes nos experimentos. Você pode exibir o conteúdo de solicitação de exemplo na página de ajuda de execução do lote para o ponto de extremidade do Azure Machine Learning Studio para verificar o mapeamento esperado.

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

#### <a name="web-service-does-not-require-an-input"></a>O serviço Web não requer uma entrada
Os serviços Web de execução em lote do Azure Machine Learning Studio podem ser usados para executar qualquer fluxo de trabalho, por exemplo, scripts R ou Python, que podem não exigir entradas. Ou, o experimento pode ser configurado com um módulo leitor que não expõe Globalparameters. Nesse caso, a atividade AzureMLBatchExecution seria configurada da seguinte maneira:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>O serviço Web não requer uma entrada/saída
O serviço Web de execução em lote do Azure Machine Learning Studio pode não ter nenhuma saída de serviço Web configurada. Neste exemplo, não há nenhuma entrada ou saída de serviço Web, nem os Globalparameters configurados. Ainda há uma saída configurada na própria atividade, mas ela não é fornecida como um webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>O serviço Web usa leitores e gravadores e a atividade é executada somente quando outras atividades tiverem êxito
Os módulos leitor e gravador do serviço Web Azure Machine Learning Studio podem ser configurados para serem executados com ou sem Globalparameters. No entanto, talvez você queira inserir chamadas de serviço em um pipeline que usa dependências de conjunto de serviços para invocar o serviço somente quando algum processamento upstream tiver sido concluído. Você também pode disparar alguma outra ação depois que a execução do lote for concluída usando essa abordagem. Nesse caso, você pode expressar as dependências usando entradas e saídas de atividade, sem nomeá-las como entradas ou saídas do serviço Web.

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

Os **argumentos** são:

* Se o seu ponto de extremidade de experimento usa um webServiceInput: ele é representado por um conjunto de informações de BLOB e é incluído nas entradas de atividade e na propriedade webServiceInput. Caso contrário, a propriedade webServiceInput será omitida.
* Se seu ponto de extremidade de experimento usa webServiceOutput (s): eles são representados por conjuntos de BLOBs e são incluídos nas saídas de atividade e na propriedade webServiceOutputs. As saídas de atividade e webServiceOutputs são mapeadas pelo nome de cada saída no experimento. Caso contrário, a propriedade webServiceOutputs será omitida.
* Se o ponto de extremidade do experimento expõe globalParameter (s), eles são fornecidos na propriedade globalparameters da atividade como pares chave, valor. Caso contrário, a propriedade globalparameters será omitida. As chaves diferenciam maiúsculas de minúsculas. [Azure data Factory funções](data-factory-functions-variables.md) podem ser usadas nos valores.
* Os conjuntos de resultados adicionais podem ser incluídos nas propriedades de entradas e saídas da atividade, sem ser referenciado no tipo de Atividadeproperties. Esses conjuntos de valores controlam a execução usando dependências de fatias, mas são ignorados pela atividade AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Atualizando modelos usando a atividade atualizar recurso
Depois de concluir o treinamento, atualize o serviço Web de Pontuação (teste de previsão exposto como um serviço Web) com o modelo treinado recentemente usando a atividade de **recurso de atualização do Azure Machine Learning Studio**. Veja [atualizando modelos usando o artigo atualizar atividade de recursos](data-factory-azure-ml-update-resource-activity.md) para obter detalhes.

### <a name="reader-and-writer-modules"></a>Módulos de leitor e gravador
Um cenário comum para usar parâmetros de serviço Web é o uso de leitores e gravadores SQL do Azure. O módulo leitor é usado para carregar dados em um experimento de serviços de gerenciamento de dados fora Azure Machine Learning Studio. O módulo gravador é para salvar dados de suas experiências em serviços de gerenciamento de dados fora Azure Machine Learning Studio.

Para obter detalhes sobre o Azure Blob/leitor/gravador SQL do Azure, consulte os tópicos de [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [gravador](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN. O exemplo na seção anterior usou o leitor de blob do Azure e o gravador de blob do Azure. Esta seção aborda o uso do leitor do SQL Azure e do gravador do SQL do Azure.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**P:** Tenho vários arquivos que são gerados por meus pipelines de Big Data. Posso usar a atividade AzureMLBatchExecution para trabalhar em todos os arquivos?

**R:** Sim. Consulte a seção **usando um módulo leitor para ler dados de vários arquivos no blob do Azure** para obter detalhes.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Atividade de Pontuação de lote do Azure Machine Learning Studio
Se você estiver usando a atividade **AzureMLBatchScoring** para integrar com Azure Machine Learning, recomendamos que você use a atividade de **AzureMLBatchExecution** mais recente.

A atividade AzureMLBatchExecution é introduzida na versão de agosto de 2015 do SDK do Azure e Azure PowerShell.

Se você quiser continuar usando a atividade AzureMLBatchScoring, continue lendo esta seção.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Atividade de Pontuação de lote do Azure Machine Learning Studio usando o armazenamento do Azure para entrada/saída

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

### <a name="web-service-parameters"></a>Parâmetros de serviço Web
Para especificar valores para parâmetros de serviço Web, adicione uma seção **typeproperties** à seção **AZUREMLBATCHSCORINGACTIVITY** no pipeline JSON, conforme mostrado no exemplo a seguir:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Você também pode usar [Data Factory funções](data-factory-functions-variables.md) na passagem de valores para os parâmetros do serviço Web, conforme mostrado no exemplo a seguir:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, certifique-se de que os nomes que você especificar na atividade JSON correspondam àqueles expostos pelo serviço Web.
>
>

## <a name="see-also"></a>Veja também
* [Postagem do blog do Azure: introdução ao Azure Data Factory e Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
