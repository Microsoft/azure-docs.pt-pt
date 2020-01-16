---
title: Criar pipelines de dados de previsão
description: Saiba como criar um pipeline de previsão usando a atividade de execução de Azure Machine Learning em lote no Azure Data Factory.
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
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029985"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Criar pipelines preditivas usando Azure Machine Learning e Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Versão atual](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite criar, testar e implantar soluções de análise preditiva. De um ponto de vista de alto nível, isso é feito em três etapas:

1. **Crie um teste de treinamento**. Você faz essa etapa usando o Azure Machine Learning Studio (clássico). Azure Machine Learning Studio (clássico) é um ambiente de desenvolvimento Visual colaborativo que você usa para treinar e testar um modelo de análise preditiva usando dados de treinamento.
2. **Converta-o em um experimento de previsão**. Depois que o modelo tiver sido treinado com os dados existentes e você estiver pronto para usá-lo para pontuar novos dados, prepare e simplifique seu experimento para pontuação.
3. **Implante-o como um serviço Web**. Você pode publicar seu experimento de Pontuação como um serviço Web do Azure. Você pode enviar dados para seu modelo por meio deste ponto de extremidade de serviço Web e receber previsões de resultado do modelo.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory e Machine Learning em conjunto
Azure Data Factory permite que você crie facilmente pipelines que usam um serviço Web publicado [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) para análise preditiva. Usando a **atividade de execução em lote** em um pipeline Azure data Factory, você pode invocar um serviço Web Azure Machine Learning Studio (clássico) para fazer previsões sobre os dados no lote.

Ao longo do tempo, os modelos de previsão nos experimentos de Pontuação Azure Machine Learning Studio (clássico) precisam ser retreinados usando novos conjuntos de dados de entrada. Você pode treinar novamente um modelo de um pipeline Data Factory realizando as seguintes etapas:

1. Publique o teste de treinamento (sem experimentos de previsão) como um serviço Web. Você faz essa etapa na Azure Machine Learning Studio (clássica) como fez para expor o experimento de previsão como um serviço Web no cenário anterior.
2. Use a atividade de execução de lote Azure Machine Learning Studio (clássica) para invocar o serviço Web para o teste de treinamento. Basicamente, você pode usar a atividade de execução de lote Azure Machine Learning Studio (clássica) para invocar o serviço Web de treinamento e o serviço Web de pontuação.

Depois de fazer o novo treinamento, atualize o serviço Web de Pontuação (teste de previsão exposto como um serviço Web) com o modelo treinado recentemente usando a **atividade de recurso de atualização Azure Machine Learning Studio (clássica)** . Veja [atualizando modelos usando o artigo atualizar atividade de recursos](update-machine-learning-models.md) para obter detalhes.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning serviço vinculado

Você cria um serviço vinculado do **Azure Machine Learning** para vincular um serviço Web do Azure Machine Learning a uma data Factory do Azure. O serviço vinculado é usado por Azure Machine Learning atividade de execução em lote e [Atualizar atividade de recurso](update-machine-learning-models.md).

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

Consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) para obter descrições sobre as propriedades na definição de JSON.

O Azure Machine Learning dá suporte a serviços Web clássicos e novos serviços Web para seu experimento de previsão. Você pode escolher o direito a ser usado em Data Factory. Para obter as informações necessárias para criar o serviço vinculado do Azure Machine Learning, acesse https://services.azureml.net, onde todos os seus (novos) serviços Web e serviços Web clássicos estão listados. Clique no serviço Web que você deseja acessar e clique em **consumir** página. Copie a **chave primária** para a propriedade **ApiKey** e **as solicitações em lote** para a propriedade **mlEndpoint** .

![Azure Machine Learning serviços Web](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Atividade de execução de Azure Machine Learning lote

O trecho JSON a seguir define uma atividade de execução de Azure Machine Learning lote. A definição da atividade tem uma referência para o serviço vinculado Azure Machine Learning que você criou anteriormente.

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

| Propriedade          | Descrição                              | Obrigatório |
| :---------------- | :--------------------------------------- | :------- |
| nome              | Nome da atividade no pipeline     | Sim      |
| descrição       | Texto que descreve o que a atividade faz.  | Não       |
| tipo              | Para Data Lake Analytics atividade U-SQL, o tipo de atividade é **AzureMLBatchExecution**. | Sim      |
| linkedServiceName | Serviços vinculados ao serviço vinculado do Azure Machine Learning. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) . | Sim      |
| webServiceInputs  | Pares de chave, valor, mapeando os nomes de Azure Machine Learning entradas do serviço Web. A chave deve corresponder aos parâmetros de entrada definidos no serviço Web publicado Azure Machine Learning. Valor é um par de propriedades de serviços vinculados do armazenamento do Azure e FilePath que especificam os locais de blob de entrada. | Não       |
| webServiceOutputs | Pares de chave, valor, mapeando os nomes de Azure Machine Learning saídas do serviço Web. A chave deve corresponder aos parâmetros de saída definidos no serviço Web publicado Azure Machine Learning. Value é um par de propriedades de serviços vinculados do armazenamento do Azure e FilePath que especificam os locais de blob de saída. | Não       |
| globalParameters  | Pares de chave, valor a serem passados para o ponto de extremidade do serviço de execução em lote Azure Machine Learning Studio (clássico). As chaves devem corresponder aos nomes dos parâmetros de serviço Web definidos no serviço Web publicado Azure Machine Learning Studio (clássico). Os valores são passados na propriedade Globalparameters da solicitação de execução em lote Azure Machine Learning Studio (clássica) | Não       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Cenário 1: experimentos usando entradas/saídas do serviço Web que se referem aos dados no armazenamento de BLOBs do Azure

Nesse cenário, o serviço Web Azure Machine Learning faz previsões usando dados de um arquivo em um armazenamento de BLOBs do Azure e armazena os resultados da previsão no armazenamento de BLOBs. O JSON a seguir define um pipeline de Data Factory com uma atividade AzureMLBatchExecution. Os dados de entrada e saída no armazenamento de BLOBs do Azure são referenciados usando um par Vinculadoname e FilePath. No exemplo de serviço vinculado de entradas e saídas são diferentes, você pode usar diferentes serviços vinculados para cada uma de suas entradas/saídas para Data Factory ser capaz de pegar os arquivos corretos e enviar para o serviço Web Azure Machine Learning Studio (clássico).

> [!IMPORTANT]
> No experimento Azure Machine Learning Studio (clássico), as portas de entrada e saída do serviço Web e os parâmetros globais têm nomes padrão ("entrada1", "entrada2") que você pode personalizar. Os nomes que você usa para as configurações webServiceInputs, webServiceOutputs e globalparameters devem corresponder exatamente aos nomes nos experimentos. Você pode exibir o conteúdo de solicitação de exemplo na página de ajuda de execução do lote para seu ponto de extremidade Azure Machine Learning Studio (clássico) para verificar o mapeamento esperado.
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
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Cenário 2: experimentos usando módulos de leitor/gravador para se referir a dados em vários armazenamentos
Outro cenário comum ao criar experimentos de Azure Machine Learning Studio (clássico) é usar os módulos importar dados e dados de saída. O módulo importar dados é usado para carregar dados em um experimento e o módulo de dados de saída é para salvar dados de seus experimentos. Para obter detalhes sobre importar dados e módulos de dados de saída, consulte os tópicos [importar](https://msdn.microsoft.com/library/azure/dn905997.aspx) dados e [dados de saída](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN.

Ao usar os módulos importar dados e dados de saída, é recomendável usar um parâmetro de serviço Web para cada propriedade desses módulos. Esses parâmetros da Web permitem que você configure os valores durante o tempo de execução. Por exemplo, você pode criar um experimento com um módulo importar dados que usa um banco de dado SQL do Azure: XXX.database.windows.net. Depois que o serviço Web tiver sido implantado, você deverá habilitar os consumidores do serviço Web para especificar outro SQL Server do Azure chamado `YYY.database.windows.net`. Você pode usar um parâmetro de serviço Web para permitir que esse valor seja configurado.

> [!NOTE]
> A entrada e saída do serviço Web são diferentes dos parâmetros do serviço Web. No primeiro cenário, você viu como uma entrada e saída podem ser especificadas para um serviço Web Azure Machine Learning Studio (clássico). Nesse cenário, você passa parâmetros para um serviço Web que correspondem às propriedades de módulos de dados de importação/saída.
>
>

Vejamos um cenário para usar parâmetros de serviço Web. Você tem um serviço Web Azure Machine Learning implantado que usa um módulo leitor para ler dados de uma das fontes de dados com suporte do Azure Machine Learning (por exemplo: banco de dado SQL do Azure). Depois que a execução do lote é executada, os resultados são gravados usando um módulo gravador (banco de dados SQL do Azure).  Nenhuma entrada e saída de serviço Web é definida nos experimentos. Nesse caso, recomendamos que você configure os parâmetros de serviço Web relevantes para os módulos leitor e gravador. Essa configuração permite que os módulos de leitor/gravador sejam configurados ao usar a atividade AzureMLBatchExecution. Você especifica parâmetros de serviço Web na seção **globalparameters** na atividade JSON da seguinte maneira.

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
> Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, certifique-se de que os nomes que você especificar na atividade JSON correspondam àqueles expostos pelo serviço Web.
>

Depois de fazer o novo treinamento, atualize o serviço Web de Pontuação (teste de previsão exposto como um serviço Web) com o modelo treinado recentemente usando a **atividade de recurso de atualização Azure Machine Learning Studio (clássica)** . Veja [atualizando modelos usando o artigo atualizar atividade de recursos](update-machine-learning-models.md) para obter detalhes.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
