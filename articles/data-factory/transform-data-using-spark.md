---
title: Transformar dados usando a atividade da Faísca
description: Aprenda a transformar dados executando programas Spark a partir de um oleoduto Azure Data Factory utilizando a Atividade de Faísca.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e5c50d2cbd16ad2808dab485ad2b2870d6f3d350
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392362"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformar dados usando a atividade da Spark na Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-spark.md)
> * [Versão atual](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade Spark num [oleoduto](concepts-pipelines-activities.md) data factory executa um programa Spark por [conta própria](compute-linked-services.md#azure-hdinsight-linked-service) ou a pedido [do](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)  cluster HDInsight. Este artigo baseia-se no artigo de atividades de [transformação](transform-data.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas. Quando utiliza um serviço ligado a pedido do Spark, a Data Factory cria automaticamente um cluster Spark para que você processe os dados a tempo e, em seguida, elimina o cluster uma vez que o processamento esteja concluído. 


## <a name="spark-activity-properties"></a>Propriedades de atividade de faísca
Aqui está a definição JSON de uma atividade de faísca:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

A tabela a seguir descreve as propriedades JSON utilizadas na definição JSON:

| Propriedade              | Descrição                              | Necessário |
| --------------------- | ---------------------------------------- | -------- |
| name                  | O nome da atividade no oleoduto.    | Yes      |
| descrição           | Texto descrevendo o que a atividade faz.  | No       |
| tipo                  | Para a Atividade de Faísca, o tipo de atividade é HDInsightSpark. | Yes      |
| linkedServiceName     | Nome do Serviço HDInsight Spark Linked no qual o programa Spark é executado. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md) | Yes      |
| SparkJobLinkedService | O serviço de armazenamento Azure que detém o ficheiro de trabalho spark, dependências e registos. Apenas os serviços ligados a **[Azure Blob](./connector-azure-blob-storage.md)** e **[ADLS Gen2](./connector-azure-data-lake-storage.md)** são suportados aqui. Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster HDInsight é utilizado. O valor deste imóvel só pode ser um serviço ligado ao Azure Storage. | No       |
| rootPath              | O recipiente e pasta Azure Blob que contém o ficheiro Spark. O nome do ficheiro é sensível a casos. Consulte a secção de estrutura da pasta (secção seguinte) para obter mais detalhes sobre a estrutura desta pasta. | Yes      |
| ingressoFilePata         | Caminho relativo para a pasta raiz do código/embalagem Spark. O ficheiro de entrada deve ser um ficheiro Python ou um ficheiro .jar. | Yes      |
| nome de classeName             | Classe principal java/faísca da aplicação      | No       |
| argumentos             | Uma lista de argumentos de linha de comando para o programa Spark. | No       |
| proxyUser             | A conta de utilizador a personificar para executar o programa Spark | No       |
| sparkConfig           | Especifique os valores das propriedades de configuração spark listadas no tópico: [Configuração de faíscas - Propriedades da aplicação](https://spark.apache.org/docs/latest/configuration.html#available-properties). | No       |
| obterDebugInfo          | Especifica quando os ficheiros de registo spark são copiados para o armazenamento Azure utilizado pelo cluster HDInsight (ou) especificado pelo sparkJobLinkedService. Valores permitidos: Nenhum, Sempre ou Fracasso. Valor predefinido: Nenhum. | No       |

## <a name="folder-structure"></a>Estrutura de pasta
Os trabalhos de faísca são mais extensíveis do que os trabalhos de Porco/Colmeia. Para trabalhos com Spark, você pode fornecer várias dependências, tais como pacotes de frascos (colocados no java CLASSPATH), ficheiros python (colocados no PYTHONPATH) e quaisquer outros ficheiros.

Crie a seguinte estrutura de pasta no armazenamento Azure Blob referenciado pelo serviço ligado a HDInsight. Em seguida, faça o upload de ficheiros dependentes para as sub-pastas apropriadas na pasta raiz representada pela **entradaFilePath**. Por exemplo, faça o upload dos ficheiros python para o sub-dobrador de pyFiles e para os ficheiros de frascos da pasta raiz. No tempo de funcionamento, o serviço Data Factory espera a seguinte estrutura de pasta no armazenamento Azure Blob:     

| Caminho                  | Descrição                              | Necessário | Tipo   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (raiz)            | O caminho fundamental do trabalho de faísca no serviço ligado ao armazenamento | Sim      | Pasta |
| &lt;utilizador definido &gt; | O caminho que aponta para o ficheiro de entrada do trabalho de Faísca | Yes      | Ficheiro   |
| ./frascos                | Todos os ficheiros desta pasta são carregados e colocados no classe java do cluster | No       | Pasta |
| ./pyFiles             | Todos os ficheiros desta pasta são carregados e colocados no PYTHONPATH do cluster | No       | Pasta |
| ./ficheiros               | Todos os ficheiros desta pasta são carregados e colocados no diretório de funcionamento do executor | No       | Pasta |
| ./arquivos            | Todos os ficheiros desta pasta não estão comprimidos | No       | Pasta |
| ./logs                | A pasta que contém troncos do cluster Spark. | No       | Pasta |

Aqui está um exemplo para um armazenamento contendo dois ficheiros de trabalho Spark no Azure Blob Storage referenciado pelo serviço ligado a HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade u-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do porco](transform-data-using-hadoop-pig.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Apache Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (clássico) Atividade de execução de lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
