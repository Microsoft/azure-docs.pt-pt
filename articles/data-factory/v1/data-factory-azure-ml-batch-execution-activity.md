---
title: Criar gasodutos de dados preditivos utilizando a Fábrica de Dados Azure
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
ms.openlocfilehash: c40b58dfb63ac6bf1b5532eb06bfd2ad0cdccde9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022032"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Crie pipelines preditivos utilizando o Azure Machine Learning e o Azure Data Factory

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

## <a name="introduction"></a>Introdução
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [a transformação](../transform-data-using-machine-learning.md)de dados utilizando machine learning na Data Factory .


### <a name="azure-machine-learning"></a>Azure Machine Learning
[O Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe construir, testar e implementar soluções de análise preditiva. Do ponto de vista de alto nível, é feito em três etapas:

1. **Criar uma experiência de treino.** Faça este passo utilizando o estúdio Azure Machine Learning. O estúdio Azure Machine Learning é um ambiente de desenvolvimento visual colaborativo que utiliza para treinar e testar um modelo de análise preditiva utilizando dados de formação.
2. **Converta-o numa experiência preditiva.** Uma vez que o seu modelo tenha sido treinado com os dados existentes e esteja pronto para usá-lo para obter novos dados, prepare e agilize a sua experiência para pontuar.
3. **Implementá-lo como um serviço web.** Pode publicar a sua experiência de pontuação como um serviço web Azure. Pode enviar dados para o seu modelo através deste ponto final do serviço web e receber previsões de resultados do modelo.

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o **movimento** e **transformação** de dados. Pode criar soluções de integração de dados utilizando a Azure Data Factory que pode ingerir dados de várias lojas de dados, transformar/processar os dados e publicar os dados de resultados nas lojas de dados.

O serviço Data Factory permite-lhe criar pipelines de dados que movimentam e transformam dados e, depois, executar esses pipelines numa agenda especificada (hora a hora, diariamente, semanalmente, etc.). Também proporciona visualizações ricas para apresentar a linhagem e as dependências entre os pipelines de dados e monitorizá-los a partir de uma vista unificada única, para identificar facilmente problemas e configurar alertas de monitorização.

Consulte [a Introdução à Azure Data Factory](data-factory-introduction.md) e [construa os seus primeiros](data-factory-build-your-first-pipeline.md) artigos de pipeline para começar rapidamente com o serviço Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Fábrica de Dados e Machine Learning juntos
A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web [azure machine learning][azure-machine-learning] publicado para análise preditiva. Utilizando a Atividade de Execução de **Lotes** num oleoduto Azure Data Factory, pode invocar um serviço web de estúdio azure machine learning para fazer previsões sobre os dados em lote. Consulte a invocação de um serviço web de estúdio azure machine learning utilizando a secção atividade de execução de lotes para mais detalhes.

Com o tempo, os modelos preditivos do estúdio Azure Machine Learning têm de ser retreinados utilizando novos conjuntos de dados de entrada. Pode retreinar um modelo de estúdio Azure Machine Learning a partir de um pipeline data factory fazendo os seguintes passos:

1. Publique a experiência de formação (não experiência preditiva) como um serviço web. Você faz este passo no estúdio Azure Machine Learning como fez para expor a experiência preditiva como um serviço web no cenário anterior.
2. Utilize o estúdio de aprendizagem automática Azure Batch Execution Activity para invocar o serviço web para a experiência de treino. Basicamente, você pode usar a atividade de execução de lotexecuçã o estúdio de aprendizagem automática Azure para invocar tanto o serviço web de treino como o serviço web de pontuação.

Depois de terminar a reconversão, atualize o serviço web de pontuação (experiência preditiva exposta como um serviço web) com o modelo recém-treinado utilizando o **estúdio de Aprendizagem automática Azure Update Resource Activity**. Consulte a atualização de modelos utilizando o artigo [de Atividade de Recursos Atualizados](data-factory-azure-ml-update-resource-activity.md) para mais detalhes.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocando um serviço web usando a atividade de execução de lote
Usa a Azure Data Factory para orquestrar o movimento e o processamento de dados e, em seguida, executa a execução de lotes utilizando o Azure Machine Learning. Aqui estão os passos de topo:

1. Crie um serviço ligado à Aprendizagem automática Azure. Precisa dos seguintes valores:

   1. **Solicite URI** para a API de Execução de Lote. Pode encontrar o Pedido URI clicando no link **DEEXECUÇÃO** DO LOTE na página de serviços web.
   2. **Chave API** para o serviço web Azure Machine Learning publicado. Pode encontrar a chave API clicando no serviço web que publicou.
   3. Utilize a atividade **AzureMLBatchExecution.**

      ![Painel de aprendizagem automática](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![Lote URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário: Experiências utilizando entradas/saídas do serviço Web que se referem a dados no Armazenamento de Blob Azure
Neste cenário, o serviço Web Azure Machine Learning faz previsões utilizando dados de um ficheiro num armazenamento de blob Azure e armazena a previsão resulta no armazenamento de blob. O seguinte JSON define um gasoduto data factory com uma atividade azureMLBatchExecution. A atividade tem o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como a saída. O **DecisionTreeInputBlob** é passado como uma entrada para o serviço web utilizando a propriedade **WebServiceInput** JSON. O **DecisionTreeResultBlob** é passado como uma saída para o serviço Web utilizando a propriedade **WebServiceOutputs** JSON.

> [!IMPORTANT]
> Se o serviço web tiver várias inputs, utilize a propriedade **webServiceInputs** em vez de utilizar **webServiceInput**. Ver o [serviço Web requer várias inputs](#web-service-requires-multiple-inputs) para um exemplo de utilização da propriedade webServiceInputs.
>
> Os conjuntos de dados referenciados pelas propriedades **webServiceInputs** / **webServiceInputs** e **webServiceOutputs** (em **typeProperties)** também devem ser incluídos nas **inputs** e **saídas**da Atividade.
>
> Na sua experiência de estúdio Azure Machine Learning, as portas de entrada e saída do serviço web e os parâmetros globais têm nomes padrão ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e definições globalParameters devem corresponder exatamente aos nomes das experiências. Pode ver a carga útil do pedido de amostra na página de Ajuda de Execução de Lote para o seu ponto final do estúdio Azure Machine Learning para verificar o mapeamento esperado.
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
> Apenas as inputs e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no corte jSON acima, DecisionTreeInputBlob é uma entrada para a atividade AzureMLBatchExecution, que é passada como uma entrada para o serviço Web através do parâmetro webServiceInput.
>
>

### <a name="example"></a>Exemplo
Este exemplo utiliza o Armazenamento Azure para deter os dados de entrada e de saída.

Recomendamos que passe pelo [seu primeiro pipeline com][adf-build-1st-pipeline] tutorial data factory antes de passar por este exemplo. Utilize o Editor da Fábrica de Dados para criar artefactos da Fábrica de Dados (serviços ligados, conjuntos de dados, pipeline) neste exemplo.

1. Crie um **serviço ligado** para o seu **Armazenamento Azure.** Se os ficheiros de entrada e saída estiverem em diferentes contas de armazenamento, necessita de dois serviços ligados. Aqui está um exemplo da JSON:

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
2. Crie o conjunto de **dados**da **entrada** Azure Data Factory . Ao contrário de outros conjuntos de dados da Data Factory, estes conjuntos de dados devem conter tanto os valores **do folderPath** como do **fileName.** Pode utilizar a partilha para fazer com que cada execução do lote (cada fatia de dados) processe ou produza ficheiros únicos de entrada e saída. Poderá ter de incluir alguma atividade a montante para transformar a entrada no formato de ficheiro CSV e colocá-la na conta de armazenamento de cada fatia. Nesse caso, não incluiria as definições **externas** e **externas** de Dados mostradas no exemplo seguinte, e o seu DecisionTreeInputBlob seria o conjunto de dados de saída de uma Atividade diferente.

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

    O ficheiro csv de entrada deve ter a linha do cabeçalho da coluna. Se estiver a utilizar a **Atividade de Cópia** para criar/mover o csv para o armazenamento **true**de bolhas, deverá configurar a propriedade da pia **blobWriterAddHeader.** Por exemplo:

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    Se o ficheiro csv não tiver a linha do cabeçalho, poderá ver o seguinte erro: **Error in Activity: Error reading string. Ficha inesperada: StartObject. Caminho '', linha 1, posição 1**.
3. Crie o conjunto de dados da **Fábrica** de **Dados**Azure . Este exemplo utiliza a partilha para criar um caminho de saída único para cada execução de fatias. Sem a partilha, a atividade substituiria o ficheiro.

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
4. Crie um serviço de tipo **ligado:** **AzureMLLinkedService,** fornecendo a chave API e url de execução de lote de modelo.

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
5. Por fim, autor de um oleoduto contendo uma Atividade de **Execução de LoteAmento AzureML.** No tempo de funcionação, o gasoduto executa os seguintes passos:

   1. Obtém a localização do ficheiro de entrada a partir dos conjuntos de dados de entrada.
   2. Invoca a API de execução de lote de aprendizagem automática Azure
   3. Copie a saída de execução do lote para a bolha dada no seu conjunto de dados de saída.

      > [!NOTE]
      > A atividade de execução azureMLBatch pode ter zero ou mais inputs e uma ou mais saídas.
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

      As datas de **início** e **de fim** devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **tempo final** é opcional. Se não especificar o valor para a propriedade **final,** é calculado como "**iniciar + 48 horas".** Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**. Veja [Referência de Processamento de Scripts JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes sobre as propriedades de JSON.

      > [!NOTE]
      > Especificar a entrada para a atividade AzureMLBatchExecution é opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário: Experiências utilizando Módulos leitor/escritor para se referir a dados em vários armazenamentos
Outro cenário comum ao criar experiências de estúdio Azure Machine Learning é usar módulos Leitor e Escritor. O módulo de leitor é usado para carregar dados numa experiência e o módulo de escritor é para guardar dados das suas experiências. Para mais detalhes sobre módulos de leitor e escritor, consulte os tópicos [do Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [do Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) na Biblioteca MSDN.

Ao utilizar os módulos de leitor e escritor, é uma boa prática utilizar um parâmetro de serviço Web para cada propriedade destes módulos de leitor/escritor. Estes parâmetros web permitem configurar os valores durante o tempo de execução. Por exemplo, pode criar uma experiência com um módulo de leitor que utiliza uma Base de Dados SQL Azure: XXX.database.windows.net. Depois de implementado o serviço web, pretende permitir aos consumidores do serviço web especificar outro servidor lógico SQL chamado YYY.database.windows.net. Pode utilizar um parâmetro de serviço Web para permitir configurar este valor.

> [!NOTE]
> A entrada e saída do serviço web são diferentes dos parâmetros do serviço Web. No primeiro cenário, viu como uma entrada e saída podem ser especificadas para um serviço Web do estúdio Azure Machine Learning. Neste cenário, passa-se por parâmetros para um serviço Web que corresponda às propriedades dos módulos de leitor/escritor.
>
>

Vamos olhar para um cenário para usar parâmetros de serviço Web. Tem um serviço web Azure Machine Learning implantado que utiliza um módulo de leitor para ler dados de uma das fontes de dados suportadas pela Azure Machine Learning (por exemplo: Base de Dados Azure SQL). Após a execução do lote, os resultados são escritos utilizando um módulo Writer (Base de Dados Azure SQL).  Nenhuma entrada e saídas do serviço web são definidas nas experiências. Neste caso, recomendamos que configure parâmetros de serviço web relevantes para os módulos de leitor e escritor. Esta configuração permite configurar os módulos leitor/escritor ao utilizar a atividade AzureMLBatchExecution. Especifica os parâmetros do serviço Web na secção **GlobalParâmetros** na atividade JSON da seguinte forma.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

Também pode utilizar funções de fábrica de dados em [valores](data-factory-functions-variables.md) de passagem para os parâmetros do serviço Web, como mostra o seguinte exemplo:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros do serviço Web são sensíveis a casos, por isso certifique-se de que os nomes que especifica na atividade JSON correspondem aos expostos pelo serviço Web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Utilização de um módulo Reader para ler dados de vários ficheiros em Azure Blob
Grandes oleodutos com atividades como Pig e Hive podem produzir um ou mais ficheiros de saída sem extensões. Por exemplo, quando especifica uma tabela externa da Colmeia, os dados para a tabela externa da Colmeia podem ser armazenados no armazenamento de blob Azure com o seguinte nome 000000_0. Pode utilizar o módulo do leitor numa experiência para ler vários ficheiros e usá-los para previsões.

Ao utilizar o módulo de leitor numa experiência de Aprendizagem automática Azure, pode especificar o Azure Blob como entrada. Os ficheiros no armazenamento de blob Azure podem ser os ficheiros de saída (Exemplo: 000000_0) que são produzidos por um script pig e hive em execução no HDInsight. O módulo de leitor permite-lhe ler ficheiros (sem extensões) configurando o **Caminho para o contentor, diretório/bolha**. O **Caminho para** o contentor aponta para o recipiente e o **diretório/bolha** aponta para pasta que contém os ficheiros como mostrado na imagem seguinte. O asterisco que é, \* ) especifica que todos os **ficheiros do recipiente/pasta (isto é, dados/dados agregados/ano=2014/mês-6/ \* )** são lidos como parte da experiência.

![Propriedades de Azure Blob](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Exemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Pipeline com atividade de execução AzureMLBatch com parâmetros de serviço web

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

No exemplo json acima:

* O serviço Web azure machine learning implantado utiliza um leitor e um módulo de escritor para ler/escrever dados de/para uma Base de Dados Azure SQL. Este serviço Web expõe os seguintes quatro parâmetros: Nome do servidor de base de dados, nome da base de dados, nome da conta do servidor e palavra-passe da conta do servidor.
* As datas de **início** e **de fim** devem estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **tempo final** é opcional. Se não especificar o valor para a propriedade **final,** é calculado como "**iniciar + 48 horas".** Para executar o pipeline de forma indefinida, especifique **9999-09-09** como o valor da propriedade **end**. Veja [Referência de Processamento de Scripts JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter mais detalhes sobre as propriedades de JSON.

### <a name="other-scenarios"></a>Outros cenários
#### <a name="web-service-requires-multiple-inputs"></a>O serviço web requer várias inputs
Se o serviço web tiver várias inputs, utilize a propriedade **webServiceInputs** em vez de utilizar **webServiceInput**. Os conjuntos de dados referenciados pela **webServiceInputs** também devem ser incluídos nas **inputs**de Atividade .

Na sua experiência de estúdio Azure Machine Learning, as portas de entrada e saída do serviço web e os parâmetros globais têm nomes padrão ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e definições globalParameters devem corresponder exatamente aos nomes das experiências. Pode ver a carga útil do pedido de amostra na página de Ajuda de Execução de Lote para o seu ponto final do estúdio Azure Machine Learning para verificar o mapeamento esperado.

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
Os serviços web de execução de lotes de estúdio Azure Machine Learning podem ser usados para executar quaisquer fluxos de trabalho, por exemplo scripts R ou Python, que podem não exigir quaisquer entradas. Ou, a experiência pode ser configurada com um módulo Reader que não expõe nenhum GlobalParameters. Nesse caso, a Atividade de Execução de LoteAmento AzureML seria configurada da seguinte forma:

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

#### <a name="web-service-does-not-require-an-inputoutput"></a>O Serviço Web não requer entrada/saída
O serviço web de execução de lotes de execução do estúdio Azure Machine Learning pode não ter qualquer saída do Serviço Web configurada. Neste exemplo, não existe entrada ou saída do Serviço Web, nem nenhum GlobalParameters está configurado. Ainda existe uma saída configurada na própria atividade, mas não é dada como uma webServiceOutput.

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

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>O Web Service usa leitores e escritores, e a atividade só funciona quando outras atividades foram bem sucedidas
O leitor de serviçoweb do estúdio Azure Machine Learning e os módulos de escritores podem estar configurados para funcionar com ou sem Quaisquer GlobalParameters. No entanto, pode querer incorporar chamadas de serviço num pipeline que utiliza dependências de conjuntos de dados para invocar o serviço apenas quando algum processamento a montante tiver concluído. Também pode desencadear outra ação após a execução do lote ter concluído usando esta abordagem. Nesse caso, pode expressar as dependências utilizando inputs e saídas de atividade, sem nomear nenhuma delas como entradas ou saídas do Serviço Web.

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

* Se o seu ponto final de experiência utilizar um webServiceInput: é representado por um conjunto de dados blob e está incluído nas inputs de atividade e na propriedade webServiceInput. Caso contrário, a propriedade webServiceInputé omitida.
* Se o seu ponto final de experiência utilizar webServiceOutput(s): eles são representados por conjuntos de dados blob e estão incluídos nas saídas de atividade e na propriedade webServiceOutputs. As saídas de atividade e os webServiceOutputs são mapeados pelo nome de cada saída na experiência. Caso contrário, a propriedade webServiceOutputs é omitida.
* Se o seu ponto final de experiência expõe globalParameter(s), eles são dado na propriedade globalParameters de atividade como chave, pares de valor. Caso contrário, a propriedade globalParameters é omitida. As chaves são sensíveis à caixa. As funções da [Azure Data Factory](data-factory-functions-variables.md) podem ser utilizadas nos valores.
* Conjuntos de dados adicionais podem ser incluídos nas propriedades de inputs e saídas de atividade, sem serem referenciados no tipo de atividadePropriedades. Estes conjuntos de dados regem a execução usando dependências de fatias, mas são ignorados pela Atividade de Execução de Lotes AzureML.


## <a name="updating-models-using-update-resource-activity"></a>Atualizar modelos usando a atividade de recursos atualizados
Depois de terminar a reconversão, atualize o serviço web de pontuação (experiência preditiva exposta como um serviço web) com o modelo recém-treinado utilizando o **estúdio de Aprendizagem automática Azure Update Resource Activity**. Consulte a atualização de modelos utilizando o artigo [de Atividade de Recursos Atualizados](data-factory-azure-ml-update-resource-activity.md) para mais detalhes.

### <a name="reader-and-writer-modules"></a>Módulos de Leitor e Escritor
Um cenário comum para a utilização de parâmetros de serviço Web é a utilização de Leitores e Escritores Azure SQL. O módulo de leitor é usado para carregar dados numa experiência de serviços de gestão de dados fora do Azure Machine Learning Studio. O módulo de escritor é para guardar dados das suas experiências em serviços de gestão de dados fora do Azure Machine Learning Studio.

Para mais detalhes sobre o leitor/escritor Azure Blob/Azure SQL, consulte os tópicos [do Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [do Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) na Biblioteca MSDN. O exemplo na secção anterior usou o leitor de Blob Azure e o escritor Azure Blob. Esta secção discute a utilização do leitor Azure SQL e do escritor Azure SQL.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Q:** Tenho vários ficheiros que são gerados pelos meus grandes oleodutos. Posso usar a Atividade de Execução azureMLbatch para trabalhar em todos os ficheiros?

**R:** Sim. Consulte o **módulo Use a Reader para ler dados de vários ficheiros na secção Azure Blob** para obter mais detalhes.

## <a name="azure-machine-learning-studio-batch-scoring-activity"></a>Atividade de pontuação do estúdio de aprendizagem automática Azure Machine Learning
Se estiver a utilizar a atividade **AzureMLBatchScoring** para integrar com o Azure Machine Learning, recomendamos que utilize a mais recente atividade **de Execução AzureMLBatch.**

A atividade AzureMLBatchExecution é introduzida no lançamento de agosto de 2015 da Azure SDK e da Azure PowerShell.

Se pretender continuar a utilizar a atividade AzureMLBatchScoring, continue a ler através desta secção.

### <a name="azure-machine-learning-studio-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Estúdio de Aprendizagem automática Azure Batch Scoring atividade usando armazenamento Azure para entrada/saída

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

### <a name="web-service-parameters"></a>Parâmetros do serviço web
Para especificar valores para os parâmetros do serviço Web, adicione uma secção **typeProperties** à secção **AzureMLBatchScoringActivity** no pipeline JSON, como mostra o seguinte exemplo:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
Também pode utilizar funções de fábrica de dados em [valores](data-factory-functions-variables.md) de passagem para os parâmetros do serviço Web, como mostra o seguinte exemplo:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Os parâmetros do serviço Web são sensíveis a casos, por isso certifique-se de que os nomes que especifica na atividade JSON correspondem aos expostos pelo serviço Web.
>
>

## <a name="see-also"></a>Veja também
* [Post de blog azure: Começar com Azure Data Factory e Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/
