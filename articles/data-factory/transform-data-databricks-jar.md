---
title: Transformar dados com databricks Jar
description: Aprenda a processar ou transformar dados executando um Frasco databricks.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 6b010000a674e351051c664dd5eeacd40e802439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81414615"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transforme os dados executando uma atividade jar em Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade do frasco de dados Azure num [oleoduto da Data Factory](concepts-pipelines-activities.md) executa um jarro de faísca no seu cluster Azure Databricks. Este artigo baseia-se no artigo de atividades de [transformação](transform-data.md)de   dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.Azure Databricks é uma plataforma gerida para executar Apache Spark.

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

|Propriedade|Descrição|Obrigatório|
|:--|---|:-:|
|name|O nome da atividade no oleoduto.|Sim|
|descrição|Texto descrevendo o que a atividade faz.|Não|
|tipo|Para a Atividade do Jar databricks, o tipo de atividade é DatabricksSparkJar.|Sim|
|linkedServiceName|Nome do Serviço Ligado databricks no qual a atividade do Jar é executado. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md)  |Sim|
|mainClassName|O nome completo da classe que contém o método principal a ser executado. Esta classe deve ser contida num JAR fornecido como biblioteca.|Sim|
|parâmetros|Parâmetros que serão passados para o método principal.  Esta é uma variedade de cordas.|Não|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma variedade de <cordas, objeto>|Sim (pelo menos um contendo o método principal Do Nome De Classe)|

> [!NOTE]
> **Problema conhecido** - Ao utilizar o mesmo [cluster interativo](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) para executar atividades simultâneas do Databricks Jar (sem o reinício do cluster), existe um problema conhecido em Databricks onde nos parâmetros da 1ª atividade também serão utilizados seguindo as atividades. Daí que os parâmetros incorretos sejam passados para os postos de trabalho subsequentes. Para mitigar este uso, use um [cluster Job.](compute-linked-services.md#example---using-new-job-cluster-in-databricks) 

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas apoiadas para atividades de databricks

Na definição de atividade de Databricks acima, especifica estes tipos de biblioteca: *frasco,* *ovo,* *maven,* *pypi,* *cran*.

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

Para mais detalhes, consulte [a documentação databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca em Databricks

#### <a name="using-databricks-workspace-ui"></a>[Utilização do espaço de trabalho Databricks UI](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho dbfs da biblioteca adicionado usando UI, pode utilizar [Databricks CLI (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas jar são armazenadas em dbfs:/FileStore/jars durante a utilização da UI. Pode listar todos através do CLI: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Biblioteca de cópias usando Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Utilize databricks CLI [(etapas de instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Exemplo - copiar JAR para dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
