---
title: Transformar dados com a atividade do Spark no Azure Data Factory | Documentos da Microsoft
description: Saiba como transformar dados, executando programas de Spark a partir de um pipeline de fábrica de dados do Azure com a atividade do Spark.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: cdf4dba3996668b3c9fe31df10050ff2cbff6cb3
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576205"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformar dados com a atividade do Spark no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-spark.md)
> * [Versão atual](transform-data-using-spark.md)

A atividade do Spark numa fábrica de dados [pipeline](concepts-pipelines-activities.md) executa um programa do Spark no [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster do HDInsight. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados. Quando utiliza um serviço de ligado do Spark a pedido, o Data Factory cria automaticamente um cluster do Spark para just-in-time processar os dados e, em seguida, elimina o cluster depois do processamento é concluído. 

> [!IMPORTANT]
> Atividade do Spark não suporta clusters do Spark do HDInsight que utilizam um Store do Azure Data Lake como armazenamento primário.

## <a name="spark-activity-properties"></a>Propriedades de atividade do Spark
Eis a definição de JSON de exemplo de uma atividade do Spark:    

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
        "rootPath": "adfspark\\pyFiles",
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

A tabela seguinte descreve as propriedades JSON utilizadas na definição de JSON:

| Propriedade              | Descrição                              | Necessário |
| --------------------- | ---------------------------------------- | -------- |
| nome                  | Nome da atividade no pipeline.    | Sim      |
| descrição           | Texto que descreve o que faz a atividade.  | Não       |
| tipo                  | Para a atividade do Spark, o tipo de atividade é HDInsightSpark. | Sim      |
| linkedServiceName     | Nome do serviço ligado de HDInsight Spark em que o programa Spark é executado. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo. | Sim      |
| SparkJobLinkedService | Serviço que contém o Spark, o ficheiro de tarefa, dependências e registos de ligado de armazenamento do Azure.  Se não especificar um valor para esta propriedade, o armazenamento associado com o cluster do HDInsight é utilizado. O valor desta propriedade só pode ser um serviço ligado do armazenamento do Azure. | Não       |
| rootPath              | O contentor de Blobs do Azure e a pasta que contém o ficheiro de Spark. O nome de ficheiro diferencia maiúsculas de minúsculas. Consulte a estrutura de pastas secção (secção seguinte) para obter detalhes sobre a estrutura dessa pasta. | Sim      |
| entryFilePath         | Caminho relativo para a pasta raiz do código/pacote Spark. O ficheiro de entrada tem de ser um ficheiro Python ou um ficheiro. JAR. | Sim      |
| className             | Classe de principal da aplicação Java/Spark      | Não       |
| argumentos             | Uma lista de argumentos da linha de comandos para o programa Spark. | Não       |
| proxyUser             | A conta de utilizador para representar a execução do programa Spark | Não       |
| sparkConfig           | Especifique os valores de propriedades de configuração de Spark listadas no tópico: [A configuração – propriedades da aplicação do spark](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não       |
| getDebugInfo          | Especifica quando os ficheiros de registo do Spark são copiados para o armazenamento do Azure utilizado pelo cluster do HDInsight (ou) especificado pelo sparkJobLinkedService. Valores permitidos: Nenhum, sempre, ou a falha. Valor predefinido: Nenhum. | Não       |

## <a name="folder-structure"></a>estrutura de pastas
Tarefas do Spark são mais extensíveis das tarefas do Pig/Hive. Para tarefas do Spark, pode fornecer múltiplas dependências, tais como jar pacotes (colocados no java CLASSPATH), ficheiros de python (colocados sobre o PYTHONPATH) e outros ficheiros.

Crie a seguinte estrutura de pasta no armazenamento de Blobs do Azure referenciado pelo serviço ligado do HDInsight. Em seguida, carregue ficheiros dependentes para as subpastas apropriado na pasta raiz representado pelo **entryFilePath**. Por exemplo, carregue os ficheiros de python para a subpasta pyFiles e ficheiros jar para a subpasta jars da pasta raiz. No tempo de execução, o serviço Data Factory espera que a seguinte estrutura de pasta no armazenamento de Blobs do Azure:     

| Caminho                  | Descrição                              | Necessário | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (root)            | O caminho da raiz da tarefa do Spark no serviço ligado do armazenamento | Sim      | Pasta |
| &lt;definido pelo utilizador &gt; | O caminho que aponta para o ficheiro de entrada da tarefa do Spark | Sim      | Ficheiro   |
| ./jars                | Todos os ficheiros nessa pasta são carregados e colocados no caminho da classe de java do cluster | Não       | Pasta |
| ./pyFiles             | Todos os ficheiros nessa pasta são carregados e colocados em PYTHONPATH do cluster | Não       | Pasta |
| ./files               | Todos os ficheiros nessa pasta são carregados e colocados no diretório de trabalho do executor | Não       | Pasta |
| . / arquiva            | Todos os ficheiros nessa pasta são descomprimidos | Não       | Pasta |
| ./logs                | A pasta que contém os registos do cluster do Spark. | Não       | Pasta |

Eis um exemplo para um armazenamento que contém dois arquivos de tarefa do Spark no armazenamento de Blobs do Azure referenciada pelo serviço ligado do HDInsight.

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
## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do Hive](transform-data-using-hadoop-hive.md)
* [Atividade PIg](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de transmissão em fluxo do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lotes de Aprendizado de máquina](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
