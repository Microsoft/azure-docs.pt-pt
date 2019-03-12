---
title: Transformar dados com o Databricks Python - Azure | Documentos da Microsoft
description: Saiba como processar ou transformar dados através da execução do Databricks Python.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 60aafd983d1c21777276683a8685376a247d11f5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541711"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transforme dados executando uma atividade de Python no Azure Databricks

A atividade de Python do Azure Databricks numa [pipeline do Data Factory](concepts-pipelines-activities.md) executa um ficheiro de Python no seu cluster do Azure Databricks. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados. O Azure Databricks é uma plataforma gerida para a execução do Apache Spark.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definição de atividade do Databricks Python

Eis a definição de JSON de exemplo de uma atividade do Databricks Python:

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

## <a name="databricks-python-activity-properties"></a>Propriedades de atividade do Databricks Python

A tabela seguinte descreve as propriedades JSON utilizadas na definição de JSON:

|Propriedade|Descrição|Necessário|
|---|---|---|
|name|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que faz a atividade.|Não|
|tipo|Para a atividade do Databricks Python, o tipo de atividade é DatabricksSparkPython.|Sim|
|linkedServiceName|Nome do serviço ligado do Databricks em que a atividade de Python é executado. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo.|Sim|
|pythonFile|O URI do ficheiro de Python a ser executado. Apenas os caminhos de DBFS são suportados.|Sim|
|parâmetros|Parâmetros de linha de comandos que serão passados para o ficheiro de Python. Esta é uma matriz de cadeias de caracteres.|Não|
|Bibliotecas|Uma lista de bibliotecas seja instalado no cluster que executará a tarefa. Pode ser uma matriz de < string, objeto >|Não|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas de suporte para atividades do databricks

Na definição de atividade do Databricks acima, para especificar esses tipos de biblioteca: *jar*, *ovo*, *maven*, *pypi*,  *cran*.

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

Para obter mais detalhes, consulte [documentação Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Utilizar a área de trabalho do Databricks da interface do Usuário](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho de dbfs da biblioteca foi adicionado com interface do Usuário, pode usar [CLI do Databricks (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas de Jar são armazenadas em dbfs: / FileStore/jars ao utilizar a interface do Usuário. Pode listar todos os através da CLI: *databricks fs ls dbfs: / FileStore/jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Biblioteca de cópia com a CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks fs cp SparkPi-assemblagem-0.1.jar dbfs: / FileStore/jars*