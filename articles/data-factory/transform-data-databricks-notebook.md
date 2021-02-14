---
title: Transformar dados com o caderno databricks
description: Saiba como processar ou transformar dados executando um caderno Databricks na Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 486dc2ab3a14917e8c7bdddf8b5b9c6f9da1a1dc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374002"
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

|Propriedade|Descrição|Necessário|
|---|---|---|
|name|O nome da atividade no oleoduto.|Yes|
|descrição|Texto descrevendo o que a atividade faz.|No|
|tipo|Para a Atividade do Caderno databricks, o tipo de atividade é DatabricksNotebook.|Yes|
|linkedServiceName|Nome do Serviço Linked Databricks no qual funciona o portátil Databricks. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md)|Yes|
|notebookPath|O caminho absoluto do caderno a ser executado no Espaço de Trabalho databricks. Este caminho deve começar com um corte.|Yes|
|basesParametros|Uma variedade de pares Key-Value. Os parâmetros de base podem ser utilizados para cada execução de atividade. Se o caderno tiver um parâmetro que não esteja especificado, o valor predefinido do portátil será utilizado. Saiba mais sobre os parâmetros nos [cadernos databricks.](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)|No|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma variedade \<string, object> de.|No|

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

Para mais detalhes, consulte a [documentação databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) para os tipos de biblioteca.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Passando parâmetros entre cadernos e Fábrica de Dados

Pode passar parâmetros de fábrica de dados para cadernos usando a propriedade *baseParameters* na atividade de databricks.

Em certos casos, poderá ser necessário repercutir certos valores do caderno de trás para a fábrica de dados, que pode ser utilizado para o fluxo de controlo (controlos condicional) na fábrica de dados ou ser consumido por atividades a jusante (o limite de tamanho é de 2MB).

1. No seu caderno, poderá ligar para [dbutils.notebook.exit ("returnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit) e o correspondente "ReturnValue" será devolvido à fábrica de dados.

2. Pode consumir a produção na fábrica de dados utilizando expressões como `'@activity('databricks notebook activity name').output.runOutput'` .

   > [!IMPORTANT]
   > Se estiver a passar o objeto JSON, poderá obter valores através da anexação de nomes de propriedade. Exemplo: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca em Databricks

### <a name="you-can-use-the-workspace-ui"></a>Pode utilizar a UI workspace:

1. [Utilize o espaço de trabalho Databricks UI](/azure/databricks/libraries/#create-a-library)

2. Para obter o caminho dbfs da biblioteca adicionado usando UI, você pode usar [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli).

   Normalmente, as bibliotecas jar são armazenadas em dbfs:/FileStore/jars durante a utilização da UI. Pode listar todos através do CLI: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Ou pode usar o Databricks CLI:

1. Siga [copiar a biblioteca utilizando databricks CLI](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Utilizar databricks CLI [(etapas de instalação)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Como exemplo, para copiar um JAR para dbfs: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
