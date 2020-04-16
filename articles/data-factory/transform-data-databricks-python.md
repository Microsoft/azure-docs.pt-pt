---
title: Transforme dados com Databricks Python
description: Aprenda a processar ou transformar dados executando um Databricks Python.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
ms.openlocfilehash: e102b14d8471a19564f66edc27cc328c2a789c98
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414606"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transforme dados executando uma atividade python em Tijolos de Dados Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


O Azure Databricks Python Activity num [pipeline data factory](concepts-pipelines-activities.md) executa um ficheiro Python no seu cluster Azure Databricks. Este artigo baseia-se no artigo sobre [atividades](transform-data.md) de transformação de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.A Azure Databricks é uma plataforma gerida para executar a Apache Spark.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definição de atividade de Databricks Python

Aqui está a definição JSON da amostra de uma Atividade Python databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Propriedades de atividade de Databricks Python

O quadro seguinte descreve as propriedades JSON utilizadas na definição JSON:

|Propriedade|Descrição|Necessário|
|---|---|---|
|nome|Nome da atividade no oleoduto.|Sim|
|descrição|Texto descrevendo o que a atividade faz.|Não|
|tipo|Para databricks Python Activity, o tipo de atividade é DatabricksSparkPython.|Sim|
|linkedServiceName|Nome do Serviço Ligado de Databricks em que funciona a atividade python. Para conhecer este serviço ligado, consulte o artigo de  [serviços ligados à Compute.](compute-linked-services.md)|Sim|
|PythonFile|O URI do ficheiro Python a ser executado. Apenas os caminhos do DBFS são apoiados.|Sim|
|parâmetros|Parâmetros da linha de comando que serão passados para o ficheiro Python. Esta é uma variedade de cordas.|Não|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma variedade de cordas <, objeto>|Não|

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

Normalmente, as bibliotecas Jar são armazenadas sob dbfs:/FileStore/frascos durante a utilização do UI. Pode listar tudo através do CLI: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Biblioteca de cópias usando Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks fs cp SparkPi-assembl-0.1.jar dbfs:/FileStore/jars*