---
title: Criar oleodutos de dados preditivos
description: Aprenda a criar um oleoduto preditivo utilizando o Azure Machine Learning Studio (clássico) - Atividade de execução de lote na Azure Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: 37a31891c3c1d812b396548036c4b59cc6523c2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375668"
---
# <a name="create-a-predictive-pipeline-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>Crie um oleoduto preditivo utilizando o Azure Machine Learning Studio (clássico) e a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versão atual](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[O Azure Machine Learning Studio (clássico)](https://azure.microsoft.com/documentation/services/machine-learning/) permite-lhe construir, testar e implementar soluções de análise preditiva. De um ponto de vista de alto nível, é feito em três etapas:

1. **Crie uma experiência de treino.** Faça este passo usando o Azure Machine Learning Studio (clássico). O Azure Machine Learning Studio (clássico) é um ambiente de desenvolvimento visual colaborativo que utiliza para treinar e testar um modelo de análise preditiva usando dados de treino.
2. **Converta-o numa experiência preditiva.** Uma vez que o seu modelo tenha sido treinado com dados existentes e esteja pronto a usá-lo para obter novos dados, prepara e agiliza a sua experiência para a pontuação.
3. **Implemente-o como um serviço web.** Pode publicar a sua experiência de pontuação como um serviço web Azure. Pode enviar dados para o seu modelo através deste ponto final do serviço web e receber previsões de resultados do modelo.

### <a name="data-factory-and-azure-machine-learning-studio-classic-together"></a>Data Factory e Azure Machine Learning Studio (clássico) juntos
A Azure Data Factory permite-lhe criar facilmente oleodutos que utilizem um serviço web publicado [do Azure Machine Learning Studio (clássico)](https://azure.microsoft.com/documentation/services/machine-learning) para análise preditiva. Utilizando a **Atividade de Execução** de Lotes num oleoduto Azure Data Factory, pode invocar um serviço web Azure Machine Learning Studio (clássico) para fazer previsões sobre os dados em lote.

Com o tempo, os modelos preditivos no Azure Machine Learning Studio (clássico) experiências de pontuação precisam de ser retreinados usando novos conjuntos de dados de entrada. Pode reforçá-lo a partir de um oleoduto data factory fazendo os seguintes passos:

1. Publique a experiência de formação (não experiência preditiva) como um serviço web. Você faz este passo no Azure Machine Learning Studio (clássico) como fez para expor a experiência preditiva como um serviço web no cenário anterior.
2. Utilize o Azure Machine Learning Studio (clássico) Batch Execution Activity para invocar o serviço web para a experiência de treino. Basicamente, você pode usar a atividade de execução de lote Azure Machine Learning Studio (clássico) para invocar tanto o serviço web de formação como o serviço web de pontuação.

Depois de terminar a reconversão, atualize o serviço web de pontuação (experiência preditiva exposta como um serviço web) com o modelo recém-treinado utilizando o **Azure Machine Learning Studio (clássico) Update Resource Activity**. Consulte [os modelos de atualização utilizando](update-machine-learning-models.md) o artigo de Atualização da Atividade de Recursos para obter mais detalhes.

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (clássico) serviço ligado

Você cria um serviço ligado ao **Azure Machine Learning Studio (clássico)** para ligar um Azure Machine Learning Studio (clássico) a uma fábrica de dados Azure. O Serviço Linked é utilizado pelo Azure Machine Learning Studio (clássico) Atividade de execução de lote e [atividade de recursos de atualização.](update-machine-learning-models.md)

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

Consulte o artigo [de serviços ligados ao Compute](compute-linked-services.md) para descrições sobre propriedades na definição JSON.

O Azure Machine Learning Studio (clássico) suporta tanto os Serviços Web Clássicos como os Novos Serviços Web para a sua experiência preditiva. Pode escolher o direito a utilizar na Data Factory. Para obter as informações necessárias para criar o Azure Machine Learning Studio (clássico) Linked Service, vá para https://services.azureml.net , onde todos os seus (novos) Serviços Web e Serviços Web Clássicos estão listados. Clique no Serviço Web a que gostaria de aceder e clique na página **Consumir.** Copie **a chave primária** para a propriedade **apiKey** e **pedidos de lote** para propriedade **mlEndpoint.**

![Azure Machine Learning Studio (clássico) Web Services](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Azure Machine Learning Studio (clássico) Atividade de execução de lote

O seguinte snippet JSON define uma atividade de execução de lote Azure Machine Learning Studio (clássico). A definição de atividade tem uma referência ao Azure Machine Learning Studio (clássico) serviço ligado que criou anteriormente.

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
| name              | Nome da atividade no oleoduto     | Yes      |
| descrição       | Texto descrevendo o que a atividade faz.  | No       |
| tipo              | Para a atividade U-SQL do Data Lake Analytics, o tipo de atividade é **AzureMLBatchExecution**. | Yes      |
| linkedServiceName | Serviços ligados ao Azure Machine Learning Studio (clássico) Linked Service. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md) | Yes      |
| webServiceInputs  | Chave, pares de valor, mapeando os nomes do Azure Machine Learning Studio (clássico) Web Service Inputs. A chave deve corresponder aos parâmetros de entrada definidos no Azure Machine Learning Studio (clássico) Do Azure Machine Learning Studio (clássico). Value é um par de serviços ligados ao armazenamento Azure e par de propriedades FilePath especificando as localizações blob de entrada. | No       |
| webServiceOutputs | Chave, pares de valor, mapeando os nomes de Azure Machine Learning Studio (clássico) Web Service Outputs. A chave deve corresponder aos parâmetros de saída definidos no Azure Machine Learning Studio (clássico) Serviço Web. Value é um par de serviços ligados ao armazenamento Azure e um par de propriedades FilePath especificando as localizações blob de saída. | No       |
| globalParameters  | Chave, pares de valor a serem passados para o Azure Machine Learning Studio (clássico) Ponto final do Serviço de Execução de Lote. As teclas devem corresponder aos nomes dos parâmetros de serviço web definidos no serviço web Azure Machine Learning Studio (clássico) publicado. Os valores são passados na propriedade GlobalParameters do Azure Machine Learning Studio (clássico) pedido de execução de lote | No       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário 1: Experiências utilizando entradas/saídas de serviço Web que se referem a dados no Armazenamento Azure Blob

Neste cenário, o serviço Web Azure Machine Learning Studio (clássico) faz previsões usando dados de um ficheiro num armazenamento de bolhas Azure e armazena os resultados da previsão no armazenamento de bolhas. O seguinte JSON define um oleoduto de Fábrica de Dados com uma atividade AzureMLBatchExecution. Os dados de entrada e saída no Azure Blog Storage são referenciados usando um par LinkedName e FilePath. Na amostra Linked Service de entradas e saídas são diferentes, você pode usar diferentes Serviços Linked para cada uma das suas entradas/saídas para data factory para ser capaz de pegar os ficheiros certos e enviar para Azure Machine Learning Studio (clássico) Web Service.

> [!IMPORTANT]
> Na sua experiência Azure Machine Learning Studio (clássica), as portas de entrada e saída de serviço web e os parâmetros globais têm nomes padrão ("input1", "input2") que pode personalizar. Os nomes que utiliza para webServiceInputs, webServiceOutputs e configurações globais de Parameters devem corresponder exatamente aos nomes das experiências. Pode ver a carga útil do pedido de amostra na página de Ajuda à Execução do Lote para o seu Azure Machine Learning Studio (clássico) para verificar o mapeamento esperado.


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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário 2: Experiências utilizando módulos de leitor/escritor para se referir a dados em vários armazenamentos
Outro cenário comum ao criar experiências do Azure Machine Learning Studio (clássico) é utilizar módulos de dados de importação e dados de saída. O módulo De Dados de Importação é utilizado para carregar dados numa experiência e o módulo dados de saída é para guardar dados das suas experiências. Para mais detalhes sobre os módulos de dados de importação e dados de saída, consulte [dados de importação](/azure/machine-learning/studio-module-reference/import-data) e [dados de saída](/azure/machine-learning/studio-module-reference/export-data) na Biblioteca MSDN.

Ao utilizar os módulos de Dados de Importação e Dados de Saída, é uma boa prática utilizar um parâmetro de serviço Web para cada propriedade destes módulos. Estes parâmetros web permitem configurar os valores durante o tempo de funcionamento. Por exemplo, pode criar uma experiência com um módulo de Dados de Importação que utiliza uma Base de Dados Azure SQL: XXX.database.windows.net. Depois de o serviço web ter sido implementado, pretende permitir que os consumidores do serviço web especifiquem outro servidor lógico sql chamado `YYY.database.windows.net` . Pode utilizar um parâmetro de serviço Web para permitir que este valor seja configurado.

> [!NOTE]
> A entrada e saída do serviço web são diferentes dos parâmetros do serviço Web. No primeiro cenário, você viu como uma entrada e saída pode ser especificada para um serviço Web Azure Machine Learning Studio (clássico). Neste cenário, você aprova parâmetros para um serviço Web que corresponde às propriedades dos módulos de Dados de Importação/Saída.

Vamos olhar para um cenário para usar parâmetros de serviço Web. Tem um serviço web Azure Machine Learning Studio (clássico) que utiliza um módulo de leitor para ler dados de uma das fontes de dados suportadas pelo Azure Machine Learning Studio (clássico) (por exemplo: Azure SQL Database). Após a execução do lote, os resultados são escritos utilizando um módulo Writer (Base de Dados Azure SQL).  Não são definidas entradas e saídas de serviço web nas experiências. Neste caso, recomendamos que configuure os parâmetros de serviço web relevantes para os módulos de leitor e escritor. Esta configuração permite configurar os módulos leitor/escritor ao utilizar a atividade AzureMLBatchExecution. Especifica os parâmetros do serviço Web na secção **global de Parâmetros** na atividade JSON da seguinte forma.

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
> Os parâmetros do serviço Web são sensíveis a casos, por isso certifique-se de que os nomes especificados na atividade JSON correspondem aos expostos pelo serviço Web.

Depois de terminar a reconversão, atualize o serviço web de pontuação (experiência preditiva exposta como um serviço web) com o modelo recém-treinado utilizando o **Azure Machine Learning Studio (clássico) Update Resource Activity**. Consulte [os modelos de atualização utilizando](update-machine-learning-models.md) o artigo de Atualização da Atividade de Recursos para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos que explicam como transformar dados de outras formas:

* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do porco](transform-data-using-hadoop-pig.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Apache Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)