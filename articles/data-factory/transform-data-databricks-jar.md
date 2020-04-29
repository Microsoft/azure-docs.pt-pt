---
title: Transforme dados com databricks jar
description: Aprenda a processar ou transformar dados executando um Databricks Jar.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414615"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transforme os dados executando uma atividade de Jar em Tijolos de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Azure Databricks Jar Activity num [oleoduto data factory](concepts-pipelines-activities.md) executa um Jar De faísca no seu cluster Azure Databricks. Este artigo baseia-se no artigo sobre [atividades](transform-data.md) de transformação de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.A Azure Databricks é uma plataforma gerida para executar a Apache Spark.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definição de atividade de Databricks Jar

Aqui está a definição json da amostra de uma Atividade de Frasco de Databricks:

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

## <a name="databricks-jar-activity-properties"></a>Propriedades da atividade do Databricks Jar

O quadro seguinte descreve as propriedades JSON utilizadas na definição JSON:

|Propriedade|Descrição|Necessário|
|:--|---|:-:|
|nome|Nome da atividade no oleoduto.|Sim|
|descrição|Texto descrevendo o que a atividade faz.|Não|
|tipo|Para databricks Jar Activity, o tipo de atividade é DatabricksSparkJar.|Sim|
|linkedServiceName|Nome do Serviço Ligado de Databricks em que a atividade do Jar funciona. Para conhecer este serviço ligado, consulte o artigo de  [serviços ligados à Compute.](compute-linked-services.md)|Sim|
|mainClassName|O nome completo da classe que contém o método principal a ser executado. Esta aula deve ser contida num JAR fornecido como biblioteca.|Sim|
|parâmetros|Parâmetros que serão passados para o método principal.  Esta é uma variedade de cordas.|Não|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma variedade de cordas <, objeto>|Sim (pelo menos um que contém o método mainClassName)|

> [!NOTE]
> **Problema Conhecido** - Ao utilizar o mesmo [cluster Interativo](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) para executar atividades simultâneas databricks Jar (sem reinício de cluster), existe uma questão conhecida nos Databricks onde em parâmetros da 1ª atividade também serão utilizados seguindo as atividades. Daí que os parâmetros incorretos sejam passados para os trabalhos subsequentes. Para mitigar isto use um [cluster de trabalho](compute-linked-services.md#example---using-new-job-cluster-in-databricks) em vez disso. 

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas suportadas para atividades de tijolos de dados

Na definição de atividade dos Databricks acima especifica-se estes tipos de biblioteca: *frasco,* *ovo,* *maven,* *pipi,* *cran*.

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

Para mais detalhes consulte [a documentação dos Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como fazer upload de uma biblioteca em Databricks

#### <a name="using-databricks-workspace-ui"></a>[Utilização do espaço de trabalho Databricks UI](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho dbfs da biblioteca adicionado usando UI, pode utilizar [databricks CLI (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas Jar são armazenadas sob dbfs:/FileStore/frascos durante a utilização do UI. Pode listar tudo através do CLI: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Biblioteca de cópias usando Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Utilizar os tijolos de dados CLI [(etapas de instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Exemplo - copiar JAR a dbfs: *dbfs cp SparkPi-assembl-assembléia-0.1.jar dbfs:/docs/sparkpi.jar*
