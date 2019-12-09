---
title: Transformar dados com o databricks Notebook
description: Saiba como processar ou transformar dados executando um notebook do databricks.
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
ms.openlocfilehash: c7a2aec35511ef066033c3d6462143ac31660e76
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923050"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformar dados executando um notebook do databricks

A atividade de Azure Databricks notebook em um [pipeline de data Factory](concepts-pipelines-activities.md) executa um notebook do databricks em seu espaço de trabalho do Azure Databricks. Este artigo se baseia nas [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte. Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definição da atividade do databricks Notebook

Aqui está a definição de JSON de exemplo de uma atividade do databricks notebook:

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

## <a name="databricks-notebook-activity-properties"></a>Propriedades da atividade do bloco de anotações do databricks

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|Descrição|Obrigatório|
|---|---|---|
|nome|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que a atividade faz.|Não|
|tipo|Para a atividade databricks notebook, o tipo de atividade é DatabricksNotebook.|Sim|
|linkedServiceName|Nome do serviço vinculado do databricks no qual o notebook do databricks é executado. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) .|Sim|
|notebookPath|O caminho absoluto do bloco de anotações a ser executado no espaço de trabalho do databricks. Esse caminho deve começar com uma barra.|Sim|
|baseparameters|Uma matriz de pares chave-valor. Os parâmetros de base podem ser usados para cada execução de atividade. Se o notebook usar um parâmetro que não é especificado, o valor padrão do notebook será usado. Encontre mais sobre parâmetros em [notebooks do databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Não|
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster que executará o trabalho. Pode ser uma matriz de cadeia de caracteres de \<, > de objeto.|Não|


## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas com suporte para atividades do databricks

Na definição da atividade do databricks acima, você especifica esses tipos de biblioteca: *jar*, *ovo*, *WHL*, *Maven*, *PyPI*, *Cran*.

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

Para obter mais detalhes, consulte a [documentação do databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Passando parâmetros entre blocos de anotações e Data Factory

Você pode passar data factory parâmetros para blocos de anotações usando a propriedade *baseparameters* na atividade do databricks. 

Em determinados casos, talvez seja necessário repassar determinados valores do bloco de anotações para data factory, que pode ser usado para o fluxo de controle (verificações condicionais) no data factory ou ser consumido pelas atividades downstream (o limite de tamanho é 2MB). 

1. No bloco de anotações, você pode chamar [dbutils. notebook. Exit ("ReturnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) e o "ReturnValue" correspondente será retornado para data Factory.

2. Você pode consumir a saída em data factory usando uma expressão como `'@activity('databricks notebook activity name').output.runOutput'`. 

   > [!IMPORTANT]
   > Se você estiver passando um objeto JSON, poderá recuperar valores acrescentando nomes de propriedade. Exemplo: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Usando a interface do usuário do databricks Workspace](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho de dBFS da biblioteca adicionada usando a interface do usuário, você pode usar a [CLI do databricks (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas jar são armazenadas em dBFS:/filestore/jars ao usar a interface do usuário. Você pode listar tudo por meio da CLI: *databricks FS ls dBFS:/filestore/jars*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copiar biblioteca usando a CLI do databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks FS CP sparkpi-assembly-0,1. jar dBFS:/filestore/jars*
