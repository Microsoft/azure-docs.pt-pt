---
title: Transformar dados com o caderno databricks
description: Saiba como processar ou transformar dados executando um caderno Databricks.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 4679d06e877679f0a56ee782b9a43a5a8147d7a5
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608124"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transforme os dados executando um caderno Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Databricks Notebook Activity in a [Data Factory pipeline](concepts-pipelines-activities.md) gere um caderno Databricks no seu espaço de trabalho Azure Databricks. Este artigo baseia-se no artigo de atividades de [transformação](transform-data.md) de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas. Azure Databricks é uma plataforma gerida para executar Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definição de atividade de caderno de dados

Aqui está a definição JSON de uma atividade de caderno de dados:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propriedades de atividade do Caderno databricks

A tabela a seguir descreve as propriedades JSON utilizadas na definição JSON:

|Propriedade|Descrição|Obrigatório|
|---|---|---|
|name|O nome da atividade no oleoduto.|Sim|
|descrição|Texto descrevendo o que a atividade faz.|Não|
|tipo|Para a Atividade do Caderno databricks, o tipo de atividade é DatabricksNotebook.|Sim|
|linkedServiceName|Nome do Serviço Linked Databricks no qual funciona o portátil Databricks. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md)|Sim|
|notebookPath|O caminho absoluto do caderno a ser executado no Espaço de Trabalho databricks. Este caminho deve começar com um corte.|Sim|
|basesParametros|Uma variedade de pares Key-Value. Os parâmetros de base podem ser utilizados para cada execução de atividade. Se o caderno tiver um parâmetro que não esteja especificado, o valor predefinido do portátil será utilizado. Saiba mais sobre os parâmetros nos [cadernos databricks.](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)|Não|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma variedade \<string, object> de.|Não|


## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas apoiadas para atividades databricks

Na definição de atividade dos Databricks acima, você especifica estes tipos de biblioteca: *jar,* *ovo,* *whl,* *maven,* *pypi,* *cran*.

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

Para mais detalhes, consulte a [documentação databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para os tipos de biblioteca.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Passando parâmetros entre cadernos e Fábrica de Dados

Pode passar parâmetros de fábrica de dados para cadernos usando a propriedade *baseParameters* na atividade de databricks. 

Em certos casos, poderá ser necessário repercutir certos valores do caderno de trás para a fábrica de dados, que pode ser utilizado para o fluxo de controlo (controlos condicional) na fábrica de dados ou ser consumido por atividades a jusante (o limite de tamanho é de 2MB). 

1. No seu caderno, poderá ligar para [dbutils.notebook.exit ("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) e o correspondente "ReturnValue" será devolvido à fábrica de dados.

2. Pode consumir a produção na fábrica de dados utilizando expressões como `'@activity('databricks notebook activity name').output.runOutput'` . 

   > [!IMPORTANT]
   > Se estiver a passar o objeto JSON, poderá obter valores através da anexação de nomes de propriedade. Exemplo: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca em Databricks

#### <a name="using-databricks-workspace-ui"></a>[Utilização do espaço de trabalho Databricks UI](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho dbfs da biblioteca adicionado usando UI, pode utilizar o [Databricks CLI (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas jar são armazenadas em dbfs:/FileStore/jars durante a utilização da UI. Pode listar todos através do CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Biblioteca de cópias usando Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
