---
title: Transformar dados com o Python do databricks
description: Saiba como processar ou transformar dados executando um Python do databricks.
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
ms.openlocfilehash: be2e389a0f103983a566a3f74d201e5589d84586
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926729"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformar dados executando uma atividade do Python no Azure Databricks

A atividade Azure Databricks Python em um [pipeline data Factory](concepts-pipelines-activities.md) executa um arquivo Python em seu cluster de Azure Databricks. Este artigo se baseia nas [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte. Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definição de atividade do Python do databricks

Aqui está a definição de JSON de exemplo de uma atividade do Python do databricks:

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

## <a name="databricks-python-activity-properties"></a>Propriedades de atividade do Python do databricks

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|Descrição|Obrigatório|
|---|---|---|
|nome|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que a atividade faz.|Não|
|tipo|Para a atividade de Python do databricks, o tipo de atividade é DatabricksSparkPython.|Sim|
|linkedServiceName|Nome do serviço vinculado do databricks no qual a atividade do Python é executada. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) .|Sim|
|pythonFile|O URI do arquivo Python a ser executado. Há suporte apenas para caminhos DBFS.|Sim|
|parâmetros|Parâmetros de linha de comando que serão passados para o arquivo Python. Esta é uma matriz de cadeias de caracteres.|Não|
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster que executará o trabalho. Pode ser uma matriz de cadeia de caracteres <, objeto >|Não|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas com suporte para atividades do databricks

Na definição de atividade do databricks acima, você especifica esses tipos de biblioteca: *jar*, *ovo*, *Maven*, *PyPI*, *Cran*.

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

Para obter mais detalhes, consulte a [documentação do databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Usando a interface do usuário do databricks Workspace](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho de dBFS da biblioteca adicionada usando a interface do usuário, você pode usar a [CLI do databricks (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas jar são armazenadas em dBFS:/filestore/jars ao usar a interface do usuário. Você pode listar tudo por meio da CLI: *databricks FS ls dBFS:/filestore/jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copiar biblioteca usando a CLI do databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks FS CP sparkpi-assembly-0,1. jar dBFS:/filestore/jars*