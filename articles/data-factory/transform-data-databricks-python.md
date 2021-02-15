---
title: Transformar dados com Databricks Python
description: Saiba como processar ou transformar dados executando uma atividade databricks Python num oleoduto Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373951"
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

|Propriedade|Descrição|Necessário|
|---|---|---|
|name|O nome da atividade no oleoduto.|Yes|
|descrição|Texto descrevendo o que a atividade faz.|No|
|tipo|Para databricks Python Activity, o tipo de atividade é DatabricksSparkPython.|Yes|
|linkedServiceName|Nome do Serviço Ligado databricks no qual funciona a atividade Python. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md)|Yes|
|pythonFile|O URI do ficheiro Python a ser executado. Apenas os caminhos DBFS são suportados.|Yes|
|parâmetros|Parâmetros da linha de comando que serão passados para o ficheiro Python. Esta é uma variedade de cordas.|No|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma variedade de <cordas, objeto>|No|

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

Para mais detalhes, consulte [a documentação databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca em Databricks

### <a name="you-can-use-the-workspace-ui"></a>Pode utilizar a UI workspace:

1. [Utilize o espaço de trabalho Databricks UI](/azure/databricks/libraries/#create-a-library)

2. Para obter o caminho dbfs da biblioteca adicionado usando UI, você pode usar [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli).

   Normalmente, as bibliotecas jar são armazenadas em dbfs:/FileStore/jars durante a utilização da UI. Pode listar todos através do CLI: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Ou pode usar o Databricks CLI:

1. Siga [copiar a biblioteca utilizando databricks CLI](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Utilizar databricks CLI [(etapas de instalação)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Como exemplo, para copiar um JAR para dbfs: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
