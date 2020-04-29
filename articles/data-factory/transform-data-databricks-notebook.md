---
title: Transforme dados com Caderno de Tijolos de Dados
description: Aprenda a processar ou transformar dados executando um caderno databricks.
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
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419022"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transforme os dados executando um caderno databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Atividade do Caderno Azure Databricks num [pipeline data factory](concepts-pipelines-activities.md) executa um caderno databricks no seu espaço de trabalho Azure Databricks. Este artigo baseia-se no artigo sobre [atividades](transform-data.md) de transformação de dados, que apresenta uma visão geral da transformação de dados e das atividades de transformação apoiadas.A Azure Databricks é uma plataforma gerida para executar a Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definição de atividade do Caderno de Tijolos de Dados

Aqui está a definição json da amostra de uma Atividade de Caderno de Databricks:

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

## <a name="databricks-notebook-activity-properties"></a>Propriedades de atividade snotebook de databricks

O quadro seguinte descreve as propriedades JSON utilizadas na definição JSON:

|Propriedade|Descrição|Necessário|
|---|---|---|
|nome|Nome da atividade no oleoduto.|Sim|
|descrição|Texto descrevendo o que a atividade faz.|Não|
|tipo|Para databricks Portátil Atividade, o tipo de atividade é DatabricksNotebook.|Sim|
|linkedServiceName|Nome do Serviço Ligado aos Databricks em que o caderno Databricks executa. Para conhecer este serviço ligado, consulte o artigo de  [serviços ligados à Compute.](compute-linked-services.md)|Sim|
|cadernoCaminho|O caminho absoluto do caderno a ser executado no Espaço de Trabalho databricks. Este caminho deve começar com um corte.|Sim|
|parâmetros baseParam|Uma variedade de pares de valor-chave. Os parâmetros base podem ser usados para cada execução de atividade. Se o caderno tiver um parâmetro que não esteja especificado, será utilizado o valor predefinido do caderno. Saiba mais sobre os parâmetros nos [Cadernos databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Não|
|bibliotecas|Uma lista de bibliotecas a instalar no cluster que executará o trabalho. Pode ser uma \<variedade de cordas, objeto>.|Não|


## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas suportadas para atividades de Databricks

Na definição de atividade dos Databricks acima, especifica-se estes tipos de biblioteca: *frasco,* *ovo,* *whl,* *maven,* *pipi,* *cran.*

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

Para mais detalhes, consulte a [documentação dos Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Passar parâmetros entre cadernos e fábrica de dados

Pode passar parâmetros de fábrica de dados para cadernos utilizando propriedade *baseParameters* na atividade de databricks. 

Em certos casos, poderá ser necessário retransmitir certos valores do caderno de volta à fábrica de dados, que podem ser utilizados para o fluxo de controlo (controlos condicional) na fábrica de dados ou ser consumido por atividades a jusante (o limite de tamanho é de 2MB). 

1. No seu caderno, pode ligar para [dbutils.notebook.exit("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) e o correspondente "returnValue" será devolvido à fábrica de dados.

2. Pode consumir a produção na fábrica de `'@activity('databricks notebook activity name').output.runOutput'`dados utilizando expressão como . 

   > [!IMPORTANT]
   > Se estiver a passar o objeto JSON, pode recuperar valores através da adesão de nomes de propriedade. Exemplo: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Como fazer upload de uma biblioteca em Databricks

#### <a name="using-databricks-workspace-ui"></a>[Utilização do espaço de trabalho Databricks UI](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho dbfs da biblioteca adicionado usando UI, pode utilizar o [Databricks CLI (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas Jar são armazenadas sob dbfs:/FileStore/frascos durante a utilização do UI. Pode listar tudo através do CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Biblioteca de cópias usando Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks fs cp SparkPi-assembl-0.1.jar dbfs:/FileStore/jars*
