---
title: Transformar dados com Databricks Python
description: Aprenda a processar ou transformar dados executando uma Databricks Python.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
manager: anandsub
ms.custom: devx-track-python
ms.openlocfilehash: 7e80fad02a186173868a6aa78aedeac0801f199a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496896"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transforme os dados executando uma atividade Python em Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


A atividade python da Azure Databricks num [oleoduto da Data Factory](concepts-pipelines-activities.md) executa um ficheiro Python no seu cluster Azure Databricks. Este artigo baseia-se no artigo de atividades de [transformação](transform-data.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas. Azure Databricks é uma plataforma gerida para executar Apache Spark.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python definição de atividade

Aqui está a definição JSON de uma atividade python de dados:

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

## <a name="databricks-python-activity-properties"></a>Databricks Python propriedades de atividade

A tabela a seguir descreve as propriedades JSON utilizadas na definição JSON:

|Propriedade|Descrição|Obrigatório|
|---|---|---|
|name|O nome da atividade no oleoduto.|Sim|
|descrição|Texto descrevendo o que a atividade faz.|Não|
|tipo|Para databricks Python Activity, o tipo de atividade é DatabricksSparkPython.|Sim|
|linkedServiceName|Nome do Serviço Ligado databricks no qual funciona a atividade Python. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md)|Sim|
|pythonFile|O URI do ficheiro Python a ser executado. Apenas os caminhos DBFS são suportados.|Sim|
|parâmetros|Parâmetros da linha de comando que serão passados para o ficheiro Python. Esta é uma variedade de cordas.|Não|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma variedade de <cordas, objeto>|Não|

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

Normalmente, as bibliotecas jar são armazenadas em dbfs:/FileStore/jars durante a utilização da UI. Pode listar todos através do CLI: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Biblioteca de cópias usando Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
