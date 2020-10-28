---
title: Criar oleodutos de dados preditivos utilizando a Azure Data Factory
description: Descreve como criar oleodutos preditivos usando a Azure Data Factory e o Azure Machine Learning Studio (clássico)
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
ms.openlocfilehash: ce3175a015b7a5813f62c639fdadbeea367bbc22
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631772"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Crie oleodutos preditivos utilizando o Azure Machine Learning Studio (clássico) e a Azure Data Factory

> [!div class="op_single_selector" title1="Atividades de Transformação"]
> * [Atividade da Colmeia](data-factory-hive-activity.md)
> * [Atividade do Porco](data-factory-pig-activity.md)
> * [Atividade mapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade de Faísca](data-factory-spark.md)
> * [Atividade de Execução em Lotes do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [.NET Atividade Personalizada](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introdução
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte os dados de [transformação utilizando machine learning na Data Factory.](../transform-data-using-machine-learning.md)


### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (clássico)
[O Azure Machine Learning Studio (clássico)](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe construir, testar e implementar soluções de análise preditiva. De um ponto de vista de alto nível, é feito em três etapas:

1. **Crie uma experiência de treino.** Faça este passo usando o Azure Machine Learning Studio (clássico). Studio (clássico) é um ambiente de desenvolvimento visual colaborativo que você usa para treinar e testar um modelo de análise preditiva usando dados de treino.
2. **Converta-o numa experiência preditiva.** Uma vez que o seu modelo tenha sido treinado com dados existentes e esteja pronto a usá-lo para obter novos dados, prepara e agiliza a sua experiência para a pontuação.
3. **Implemente-o como um serviço web.** Pode publicar a sua experiência de pontuação como um serviço web Azure. Pode enviar dados para o seu modelo através deste ponto final do serviço web e receber previsões de resultados no modelo.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o **movimento** e **transformação** de dados. Pode criar soluções de integração de dados utilizando a Azure Data Factory que possa ingerir dados de várias lojas de dados, transformar/processar os dados e publicar os dados dos resultados nas lojas de dados.

O serviço Data Factory permite-lhe criar pipelines de dados que movimentam e transformam dados e, depois, executar esses pipelines numa agenda especificada (hora a hora, diariamente, semanalmente, etc.). Também proporciona visualizações ricas para apresentar a linhagem e as dependências entre os pipelines de dados e monitorizá-los a partir de uma vista unificada única, para identificar facilmente problemas e configurar alertas de monitorização.

Consulte a Introdução à Fábrica de [Dados Azure](data-factory-introduction.md) e [construa os seus primeiros](data-factory-build-your-first-pipeline.md) artigos de pipeline para começar rapidamente com o serviço Azure Data Factory.

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory e Machine Learning Studio (clássico) juntos
A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web publicado [do Azure Machine Learning Studio (clássico)][azure-machine-learning] para análise preditiva. Utilizando a **Atividade de Execução** de Lotes num oleoduto Azure Data Factory, pode invocar um serviço web Studio (clássico) para fazer previsões sobre os dados em lote. Consulte invocando um serviço web Azure Machine Learning Studio (clássico) utilizando a secção de Atividade de Execução de Lote para mais detalhes.

Com o tempo, os modelos preditivos no Estúdio (clássico) experiências de pontuação precisam de ser retreinados usando novos conjuntos de dados de entrada. Pode treinar um modelo Studio (clássico) a partir de um oleoduto data factory fazendo os seguintes passos:

1. Publique a experiência de formação (não experiência preditiva) como um serviço web. Faz este passo no Studio (clássico) como fez para expor a experiência preditiva como um serviço web no cenário anterior.
2. Utilize a Atividade de Execução de Lote (clássica) para invocar o serviço web para a experiência de treino. Basicamente, você pode usar a atividade de execução de lote (clássico) para invocar tanto o serviço web de treino como o serviço web de pontuação.

Depois de terminar a reconversão, atualize o serviço web de pontuação (experiência preditiva exposta como um serviço web) com o modelo recém-treinado utilizando o **Azure Machine Learning Studio (clássico) Update Resource Activity** . Consulte [os modelos de atualização utilizando](data-factory-azure-ml-update-resource-activity.md) o artigo de Atualização da Atividade de Recursos para obter mais detalhes.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocando um serviço web usando a atividade de execução de lotes
Utiliza a Azure Data Factory para orquestrar o movimento e o processamento de dados e, em seguida, executa a execução do lote utilizando o Studio (clássico). Aqui estão os passos de alto nível:

1. Crie um serviço ligado ao Azure Machine Learning Studio (clássico). Precisa dos seguintes valores:

   1. **Solicite uri** para a API de Execução de Lote. Pode encontrar o URI pedido clicando no link **DE EXECUÇÃO DE LOTE** na página de serviços web.
   2. **Chave API** para o serviço web do Studio (clássico) publicado. Pode encontrar a chave API clicando no serviço web que publicou.
   3. Utilize a atividade **AzureMLBatchExecution.**

      ![Estúdio de Aprendizagem Automática (clássico) Dashboard](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Lote URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário: Experiências utilizando entradas/saídas de serviço web que se referem a dados no Azure Blob Storage
Neste cenário, o serviço Web Studio (clássico) faz previsões usando dados de um ficheiro num armazenamento de bolhas Azure e armazena os resultados da previsão no armazenamento de bolhas. O seguinte JSON define um oleoduto de Fábrica de Dados com uma atividade AzureMLBatchExecution. A atividade tem o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como a saída. O **DecisionTreeInputBlob** é passado como uma entrada para o serviço web utilizando a propriedade **webServiceInput** JSON. A **decisãoTreeResultBlob** é transmitida como uma saída para o serviço Web utilizando a propriedade **webServiceOutputs** JSON.

> [!IMPORTANT]
> Se o serviço web tiver múltiplas entradas, utilize a propriedade **webServiceInputs** em vez de utilizar **o webServiceInput** . Consulte o [serviço Web requer várias entradas](#web-service-requires-multiple-inputs) para um exemplo de utilização da propriedade webServiceInputs.
>
> Os conjuntos de dados **webServiceInput** que são referenciados pelas propriedades / **webServiceInputs** e **webServiceOutputs** (em **typeProperties)** também devem ser **incluídos** nas entradas e **saídas** de Atividade .
>
> Na sua experiência studio (clássica), as portas de entrada e saída de serviço web e os parâmetros globais têm nomes padrão ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e configurações globais de Parameters devem corresponder exatamente aos nomes das experiências. Pode ver a carga útil do pedido de amostra na página de Ajuda à Execução do Lote para o seu ponto final do Estúdio (clássico) para verificar o mapeamento esperado.
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
> Apenas as entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no snippet JSON acima, DecisionTreeInputBlob é uma entrada para a atividade AzureMLBatchExecution, que é passada como uma entrada para o serviço Web através do parâmetro webServiceInput.
>
>

### <a name="example"></a>Exemplo
Este exemplo utiliza o Azure Storage para conter os dados de entrada e saída.

Recomendamos que passe pelo [seu primeiro oleoduto com][adf-build-1st-pipeline] o tutorial da Data Factory antes de passar por este exemplo. Utilize o Editor de Fábrica de Dados para criar artefactos da Data Factory (serviços ligados, conjuntos de dados, pipeline) neste exemplo.

1. Crie um **serviço ligado** para o seu **Azure Storage** . Se os ficheiros de entrada e saída estiverem em diferentes contas de armazenamento, precisa de dois serviços ligados. Aqui está um exemplo JSON:

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
2. Criar o conjunto de **dados** da **Azure** Data Factory . Ao contrário de outros conjuntos de dados da Data Factory, estes conjuntos de dados devem conter tanto **valores de pastaPa** e **nome de ficheiro.** Pode utilizar a partição para fazer com que cada execução do lote (cada fatia de dados) processe ou produza ficheiros de entrada e saída únicos. Pode ser necessário incluir alguma atividade a montante para transformar a entrada no formato de ficheiro CSV e colocá-la na conta de armazenamento de cada fatia. Nesse caso, não incluiria as definições **deData externo** e **externo** mostradas no exemplo seguinte, e o seu DecisionTreeInputBlob seria o conjunto de dados de saída de uma Atividade diferente.

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

    O seu ficheiro csv de entrada deve ter a linha do cabeçalho da coluna. Se estiver a utilizar a **Atividade de Cópia** para criar/mover o csv para o armazenamento do blob, deve definir a propriedade da pia **blobWriterAddHeader** para **ser verdadeira** . Por exemplo:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Se o ficheiro csv não tiver a linha do cabeçalho, poderá ver o seguinte erro: **Erro na Atividade: Cadeia de leitura de erro. Token inesperado: StartObject. Caminho '', linha 1, posição 1** .
3. Criar o **conjunto de** **dados** Azure Data Factory de saída . Este exemplo usa a partição para criar um caminho de saída único para cada execução de fatias. Sem a divisão, a atividade substituiria o ficheiro.

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
4. Criar um serviço de tipo **ligado:** **AzureMLLinkedService,** fornecendo a chave API e url de execução de lote de modelo.

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
5. Finalmente, autore um oleoduto contendo uma **Atividade AzureMLBatchExecution.** No tempo de funcionação, o gasoduto executa os seguintes passos:

   1. Obtém a localização do ficheiro de entrada a partir dos seus conjuntos de dados de entrada.
   2. Invoca o Estúdio (clássico) execução de lote API
   3. Copia a saída de execução do lote para a bolha dada no conjunto de dados de saída.

      > [!NOTE]
      > A atividade AzureMLBatchExecution pode ter entradas zero ou mais e uma ou mais saídas.
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

      As datas **de início** e **de fim** devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **fim do** tempo é opcional. Se não especificar valor para a propriedade **final,** é calculado como " **início + 48 horas ".** Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end** . Veja [Referência de Processamento de Scripts JSON](/previous-versions/azure/dn835050(v=azure.100)) para obter mais detalhes sobre as propriedades de JSON.

      > [!NOTE]
      > Especificar a entrada para a atividade AzureMLBatchExecution é opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário: Experiências que utilizam módulos de leitor/escritor para se referir a dados em vários armazenamentos
Outro cenário comum ao criar experiências de Estúdio (clássico) é usar módulos Reader e Writer. O módulo de leitor é usado para carregar dados numa experiência e o módulo de escritor é para guardar dados das suas experiências. Para mais detalhes sobre os módulos de leitor e escritor, consulte tópicos [de Leitor](/azure/machine-learning/studio-module-reference/import-data) e [Escritor](/azure/machine-learning/studio-module-reference/export-data) na Biblioteca MSDN.

Ao utilizar os módulos de leitor e escritor, é uma boa prática utilizar um parâmetro de serviço Web para cada propriedade destes módulos de leitor/escritor. Estes parâmetros web permitem configurar os valores durante o tempo de funcionamento. Por exemplo, pode criar uma experiência com um módulo de leitor que utiliza uma Base de Dados Azure SQL: XXX.database.windows.net. Depois de o serviço web ter sido implementado, pretende permitir que os consumidores do serviço web especifiquem outro servidor lógico do SQL chamado YYY.database.windows.net. Pode utilizar um parâmetro de serviço Web para permitir que este valor seja configurado.

> [!NOTE]
> A entrada e saída do serviço web são diferentes dos parâmetros do serviço Web. No primeiro cenário, viu como uma entrada e saída podem ser especificadas para um serviço Web Studio (clássico). Neste cenário, você aprova parâmetros para um serviço Web que corresponde às propriedades dos módulos de leitor/escritor.
>
>

Vamos olhar para um cenário para usar parâmetros de serviço Web. Tem um serviço web de Estúdio (clássico) implantado que utiliza um módulo de leitor para ler dados de uma das fontes de dados suportadas pelo Studio (clássico) (por exemplo: Azure SQL Database). Após a execução do lote, os resultados são escritos utilizando um módulo Writer (Base de Dados Azure SQL).  Não são definidas entradas e saídas de serviço web nas experiências. Neste caso, recomendamos que configuure os parâmetros de serviço web relevantes para os módulos de leitor e escritor. Esta configuração permite configurar os módulos leitor/escritor ao utilizar a atividade AzureMLBatchExecution. Especifica os parâmetros do serviço Web na secção **global de Parâmetros** na atividade JSON da seguinte forma.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Também pode utilizar [funções de fábrica de dados](data-factory-functions-variables.md) em valores de passagem para os parâmetros do serviço Web, como mostrado no exemplo seguinte:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros do serviço Web são sensíveis a casos, por isso certifique-se de que os nomes especificados na atividade JSON correspondem aos expostos pelo serviço Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Utilizar um módulo Reader para ler dados de vários ficheiros em Azure Blob
Grandes oleodutos de dados com atividades como Pig e Hive podem produzir um ou mais ficheiros de saída sem extensões. Por exemplo, quando especificar uma tabela de Colmeia externa, os dados para a tabela de Colmeia externa podem ser armazenados no armazenamento de bolhas Azure com o seguinte nome 000000_0. Pode utilizar o módulo de leitor numa experiência para ler vários ficheiros e usá-los para previsões.

Ao utilizar o módulo de leitor numa experiência studio (clássica), pode especificar Azure Blob como uma entrada. Os ficheiros no armazenamento de blob Azure podem ser os ficheiros de saída (Exemplo: 000000_0) que são produzidos por um script pig and hive em execução em HDInsight. O módulo do leitor permite-lhe ler ficheiros (sem extensões) configurando o **Caminho para o contentor, diretório/bolha** . O **Caminho para** o contentor aponta para o contentor e o **diretório/bolha** aponta para a pasta que contém os ficheiros como mostrado na imagem seguinte. O asterisco que é, \* ) especifica que todos os **ficheiros do recipiente/pasta (isto é, dados/dados agregados/ano=2014/mês-6/ \* )** são lidos como parte da experiência.

![Propriedades de Azure Blob](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline com atividade de AzureMLBatchExecution com Parâmetros de Serviço Web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
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

No exemplo JSON acima:

* O serviço Web do Studio (clássico) implantado utiliza um leitor e um módulo de escritor para ler/escrever dados de/para uma Base de Dados Azure SQL. Este serviço Web expõe os seguintes quatro parâmetros: nome do servidor de base de dados, nome da base de dados, nome da conta do utilizador do servidor e senha de conta de utilizador do Servidor.
* As datas **de início** e **de fim** devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **fim do** tempo é opcional. Se não especificar valor para a propriedade **final,** é calculado como " **início + 48 horas ".** Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end** . Veja [Referência de Processamento de Scripts JSON](/previous-versions/azure/dn835050(v=azure.100)) para obter mais detalhes sobre as propriedades de JSON.

### <a name="other-scenarios"></a>Outros cenários
#### <a name="web-service-requires-multiple-inputs"></a>O serviço web requer múltiplas entradas
Se o serviço web tiver múltiplas entradas, utilize a propriedade **webServiceInputs** em vez de utilizar **o webServiceInput** . Os conjuntos de dados que são referenciados pelos **webServiceInputs** também devem ser **incluídos** nas entradas De Atividade .

Na sua experiência Azure Machine Learning Studio (clássica), as portas de entrada e saída de serviço web e os parâmetros globais têm nomes padrão ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e configurações globais de Parameters devem corresponder exatamente aos nomes das experiências. Pode ver a carga útil do pedido de amostra na página de Ajuda à Execução do Lote para o seu ponto final do Estúdio (clássico) para verificar o mapeamento esperado.

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

#### <a name="web-service-does-not-require-an-input"></a>O Serviço Web não requer uma entrada
Os serviços web de execução de lotes Azure Machine Learning Studio (clássicos) podem ser utilizados para executar quaisquer fluxos de trabalho, por exemplo, scripts R ou Python, que podem não exigir nenhuma entrada. Ou, a experiência pode ser configurada com um módulo Reader que não expõe nenhum GlobalParameters. Nesse caso, a Atividade deExecução AzureMLBatch seria configurada da seguinte forma:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>O Serviço Web não requer uma entrada/saída
O serviço web de execução de lote Azure Machine Learning Studio (clássico) pode não ter nenhuma saída de Serviço Web configurada. Neste exemplo, não existe entrada ou saída do Serviço Web, nem existem configurados Os GlobalParameters. Existe ainda uma saída configurada na própria atividade, mas não é dada como um webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>O Serviço Web usa leitores e escritores, e a atividade só funciona quando outras atividades têm sucesso
O Azure Machine Learning Studio (clássico) leitor de serviços web e módulos de escritor podem ser configurados para funcionar com ou sem qualquer GlobalParameters. No entanto, é melhor incorporar chamadas de serviço num pipeline que utilize dependências de conjuntos de dados para invocar o serviço apenas quando algum processamento a montante tiver concluído. Também pode desencadear outra ação depois de concluída a execução do lote utilizando esta abordagem. Nesse caso, pode expressar as dependências usando entradas e saídas de atividade, sem nomear nenhuma delas como entradas ou saídas do Serviço Web.

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

Os **takeaways** são:

* Se o seu ponto final de experiência utilizar um webServiceInput: é representado por um conjunto de dados blob e está incluído nas entradas de atividade e na propriedade webServiceInput. Caso contrário, a propriedade webServiceInput é omitida.
* Se o seu ponto final de experiência utilizar webServiceOutput(s): eles são representados por conjuntos de dados blob e estão incluídos nas saídas de atividade e na propriedade webServiceOutputs. As saídas de atividade e webServiceOuts são mapeadas pelo nome de cada saída na experiência. Caso contrário, a propriedade webServiceOutputs é omitida.
* Se o seu ponto final de experiência expõe os globalParameter(s), eles são dados na atividade globalParameters propriedade como chave, pares de valor. Caso contrário, a propriedade globalParameters é omitida. As chaves são sensíveis a casos. [As funções Azure Data Factory](data-factory-functions-variables.md) podem ser utilizadas nos valores.
* Conjuntos de dados adicionais podem ser incluídos nas propriedades de entradas e saídas de atividade, sem serem referenciados nas propriedades do tipo De atividade. Estes conjuntos de dados regem a execução usando dependências de fatias, mas são ignorados pela Atividade AzureMLBatchExecution.


## <a name="updating-models-using-update-resource-activity"></a>Atualizar modelos usando a atividade de recursos de atualização
Depois de terminar a reconversão, atualize o serviço web de pontuação (experiência preditiva exposta como um serviço web) com o modelo recém-treinado utilizando o **Azure Machine Learning Studio (clássico) Update Resource Activity** . Consulte [os modelos de atualização utilizando](data-factory-azure-ml-update-resource-activity.md) o artigo de Atualização da Atividade de Recursos para obter mais detalhes.

### <a name="reader-and-writer-modules"></a>Módulos de Leitor e Escritor
Um cenário comum para a utilização de parâmetros de serviço Web é o uso de Leitores e Escritores Azure SQL. O módulo de leitor é usado para carregar dados numa experiência a partir de serviços de gestão de dados fora do Studio (clássico). O módulo de escritor é guardar dados das suas experiências em serviços de gestão de dados fora do Studio (clássico).

Para mais detalhes sobre o leitor/escritor Azure Blob/Azure SQL, consulte tópicos [de Reader](/azure/machine-learning/studio-module-reference/import-data) e [Writer](/azure/machine-learning/studio-module-reference/export-data) na MsDN Library. O exemplo na secção anterior usou o leitor Azure Blob e o escritor Azure Blob. Esta secção discute a utilização do leitor Azure SQL e do escritor Azure SQL.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Q:** Tenho vários ficheiros que são gerados pelos meus grandes oleodutos de dados. Posso utilizar a Atividade AzureMLBatchExecution para trabalhar em todos os ficheiros?

**R:** Sim. Consulte o **módulo 'Utilizar um leitor' para ler dados de vários ficheiros na secção Azure Blob** para obter mais detalhes.

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Azure Machine Learning Studio (clássico) Atividade de pontuação de lote
Se estiver a utilizar a atividade **AzureMLBatchScoring** para integrar-se com o Azure Machine Learning Studio (clássico), recomendamos que utilize a mais recente atividade **de AzureMLBatchExecution.**

A atividade AzureMLBatchExecution é introduzida no lançamento de agosto de 2015 da Azure SDK e da Azure PowerShell.

Se pretender continuar a utilizar a atividade AzureMLBatchScoring, continue a ler através desta secção.

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning Studio (clássico) Atividade de pontuação de lote usando Azure Storage para entrada/saída

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

### <a name="web-service-parameters"></a>Parâmetros de serviço web
Para especificar os valores para os parâmetros de serviço Web, adicione uma secção **de tipoProperties** à secção **AzureMLBatchScoringActivity** no pipeline JSON, como mostra o seguinte exemplo:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Também pode utilizar [funções de fábrica de dados](data-factory-functions-variables.md) em valores de passagem para os parâmetros do serviço Web, como mostrado no exemplo seguinte:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros do serviço Web são sensíveis a casos, por isso certifique-se de que os nomes especificados na atividade JSON correspondem aos expostos pelo serviço Web.
>
>

## <a name="see-also"></a>Consulte também
* [Post de blog da Azure: Começar com a Azure Data Factory e Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/