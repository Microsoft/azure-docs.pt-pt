---
title: Transformar dados com databricks Jar
description: Saiba como processar ou transformar dados executando um Frasco de Dados dentro de um oleoduto Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374019"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transforme os dados executando uma atividade jar em Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade do frasco de dados Azure num [oleoduto da Data Factory](concepts-pipelines-activities.md) executa um jarro de faísca no seu cluster Azure Databricks. Este artigo baseia-se no artigo de atividades de [transformação](transform-data.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas. Azure Databricks é uma plataforma gerida para executar Apache Spark.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definição de atividade de Databricks Jar

Aqui está a definição JSON de uma atividade de frasco de dados:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Propriedades de atividade do Databricks Jar

A tabela a seguir descreve as propriedades JSON utilizadas na definição JSON:

|Propriedade|Descrição|Necessário|
|:--|---|:-:|
|name|O nome da atividade no oleoduto.|Yes|
|descrição|Texto descrevendo o que a atividade faz.|No|
|tipo|Para a Atividade do Jar databricks, o tipo de atividade é DatabricksSparkJar.|Yes|
|linkedServiceName|Nome do Serviço Ligado databricks no qual a atividade do Jar é executado. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md)|Yes|
|mainClassName|O nome completo da classe que contém o método principal a ser executado. Esta classe deve ser contida num JAR fornecido como biblioteca. Um ficheiro JAR pode conter várias classes. Cada uma das classes pode conter um método principal.|Yes|
|parâmetros|Parâmetros que serão passados para o método principal. Esta propriedade é uma variedade de cordas.|No|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma variedade de <cordas, objeto>|Sim (pelo menos um contendo o método principal Do Nome De Classe)|

> [!NOTE]
> **Problema conhecido** - Ao utilizar o mesmo [cluster interativo](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) para executar atividades simultâneas do Databricks Jar (sem o reinício do cluster), existe um problema conhecido em Databricks onde nos parâmetros da 1ª atividade também serão utilizados seguindo as atividades. Daí que os parâmetros incorretos sejam passados para os postos de trabalho subsequentes. Para mitigar este uso, use um [cluster Job.](compute-linked-services.md#example---using-new-job-cluster-in-databricks)

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas apoiadas para atividades de databricks

Na definição de atividade anterior dos Databricks, especificou estes tipos de biblioteca: `jar` , , , . `egg` `maven` `pypi` `cran` .

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Para obter mais informações, consulte a [documentação databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) para os tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca em Databricks

### <a name="you-can-use-the-workspace-ui"></a>Pode utilizar a UI workspace:

1. [Utilize o espaço de trabalho Databricks UI](/azure/databricks/libraries/#create-a-library)

2. Para obter o caminho dbfs da biblioteca adicionado usando UI, você pode usar [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli).

   Normalmente, as bibliotecas jar são armazenadas em dbfs:/FileStore/jars durante a utilização da UI. Pode listar todos através do CLI: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Ou pode usar o Databricks CLI:

1. Siga [copiar a biblioteca utilizando databricks CLI](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Utilizar databricks CLI [(etapas de instalação)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Como exemplo, para copiar um JAR para dbfs: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Passos seguintes

Para uma introdução de onze minutos e demonstração desta funcionalidade, veja o [vídeo](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player).
