---
title: Transformar dados usando a atividade do Spark no Azure Data Factory
description: Saiba como transformar dados executando programas Spark de um pipeline de data factory do Azure usando a atividade do Spark.
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
ms.openlocfilehash: 5f3bb88d3a2e43abe1776a4b46e4ab35490db8ec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683761"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformar dados usando a atividade do Spark no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-spark.md)
> * [Versão atual](transform-data-using-spark.md)

A atividade do Spark em um [pipeline](concepts-pipelines-activities.md) data Factory executa um programa Spark em [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo [atividades de transformação de dados](transform-data.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte. Quando você usa um serviço vinculado do Spark sob demanda, o Data Factory cria automaticamente um cluster Spark para você just-in-time para processar os dados e, em seguida, exclui o cluster quando o processamento é concluído. 


## <a name="spark-activity-properties"></a>Propriedades da atividade do Spark
Aqui está a definição de JSON de exemplo de uma atividade do Spark:    

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

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

| Propriedade              | Descrição                              | Necessário |
| --------------------- | ---------------------------------------- | -------- |
| nome                  | Nome da atividade no pipeline.    | Sim      |
| descrição           | Texto que descreve o que a atividade faz.  | Não       |
| tipo                  | Para a atividade do Spark, o tipo de atividade é HDInsightSpark. | Sim      |
| linkedServiceName     | Nome do serviço vinculado do HDInsight Spark no qual o programa Spark é executado. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) . | Sim      |
| sparkJobLinkedService | O serviço vinculado do armazenamento do Azure que contém o arquivo de trabalho do Spark, as dependências e os logs.  Se você não especificar um valor para essa propriedade, o armazenamento associado ao cluster HDInsight será usado. O valor dessa propriedade só pode ser um serviço vinculado do armazenamento do Azure. | Não       |
| rootPath              | O contêiner de blob do Azure e a pasta que contém o arquivo Spark. O nome do arquivo diferencia maiúsculas de minúsculas. Consulte a seção estrutura de pastas (próxima seção) para obter detalhes sobre a estrutura dessa pasta. | Sim      |
| entryFilePath         | Caminho relativo para a pasta raiz do código/pacote do Spark. O arquivo de entrada deve ser um arquivo Python ou um arquivo. jar. | Sim      |
| className             | Classe principal Java/Spark do aplicativo      | Não       |
| argumentos             | Uma lista de argumentos de linha de comando para o programa Spark. | Não       |
| proxyUser             | A conta de usuário a ser representada para executar o programa Spark | Não       |
| sparkConfig           | Especifique valores para as propriedades de configuração do Spark listadas no tópico: [configuração do Spark-Propriedades do aplicativo](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não       |
| getDebugInfo          | Especifica quando os arquivos de log do Spark são copiados para o armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: nenhum, sempre ou falha. Valor padrão: nenhum. | Não       |

## <a name="folder-structure"></a>Estrutura de pastas
Os trabalhos do Spark são mais extensíveis do que os trabalhos do Pig/Hive. Para trabalhos do Spark, você pode fornecer várias dependências, como pacotes JAR (colocados no CLASSPATH do Java), arquivos Python (colocados no PYTHONPATH) e quaisquer outros arquivos.

Crie a seguinte estrutura de pastas no armazenamento de BLOBs do Azure referenciado pelo serviço vinculado do HDInsight. Em seguida, carregue os arquivos dependentes para as subpastas apropriadas no diretório raiz representado por **entryFilePath**. Por exemplo, carregue arquivos Python na subpasta pyFiles e nos arquivos jar para a subpasta jars da pasta raiz. Em tempo de execução, Data Factory serviço espera a seguinte estrutura de pasta no armazenamento de BLOBs do Azure:     

| Caminho                  | Descrição                              | Necessário | Tipo   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (raiz)            | O caminho raiz do trabalho do Spark no serviço vinculado de armazenamento | Sim      | Pasta |
| &lt;&gt; definidas pelo usuário | O caminho que aponta para o arquivo de entrada do trabalho do Spark | Sim      | Ficheiro   |
| ./jars                | Todos os arquivos nessa pasta são carregados e colocados no classpath Java do cluster | Não       | Pasta |
| ./pyFiles             | Todos os arquivos nessa pasta são carregados e colocados no PYTHONPATH do cluster | Não       | Pasta |
| ./files               | Todos os arquivos nesta pasta são carregados e colocados no diretório de trabalho do executor | Não       | Pasta |
| ./archives            | Todos os arquivos nesta pasta são descompactados | Não       | Pasta |
| ./logs                | A pasta que contém os logs do cluster Spark. | Não       | Pasta |

Aqui está um exemplo de armazenamento que contém dois arquivos de trabalho do Spark no armazenamento de BLOBs do Azure referenciados pelo serviço vinculado do HDInsight.

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
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [Atividade de U-SQL](transform-data-using-data-lake-analytics.md)
* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de Machine Learning lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
