---
title: Transformar dados com o bloco de notas do Databricks - Azure | Documentos da Microsoft
description: Saiba como processar ou transformar dados ao executar um Databricks notebook.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 8036a8694bb8c8d0db236eba831f13dc2bf47d0a
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576825"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformar dados através da execução de um bloco de notas do Databricks

A atividade do Azure Databricks Notebook numa [pipeline do Data Factory](concepts-pipelines-activities.md) executa um Databricks notebook na sua área de trabalho do Azure Databricks. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados. O Azure Databricks é uma plataforma gerida para a execução do Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definição de atividade do Databricks Notebook

Eis a definição de JSON de exemplo de uma atividade do Databricks Notebook:

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

## <a name="databricks-notebook-activity-properties"></a>Propriedades da atividade Databricks Notebook

A tabela seguinte descreve as propriedades JSON utilizadas na definição de JSON:

|Propriedade|Descrição|Necessário|
|---|---|---|
|nome|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que faz a atividade.|Não|
|tipo|Para a atividade do Databricks Notebook, o tipo de atividade é DatabricksNotebook.|Sim|
|linkedServiceName|Nome do serviço ligado do Databricks em que é executado o bloco de notas do Databricks. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo.|Sim|
|notebookPath|O caminho absoluto do bloco de notas para ser executado na área de trabalho do Databricks. Este caminho tem de começar com uma barra.|Sim|
|baseParameters|Uma matriz de pares chave-valor. Parâmetros bases podem ser utilizados para cada execução de atividade. Se o bloco de notas assume um parâmetro que não for especificado, será utilizado o valor predefinido do bloco de notas. Encontrar mais informações sobre parâmetros na [blocos de notas do Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Não|
|Bibliotecas|Uma lista de bibliotecas seja instalado no cluster que executará a tarefa. Pode ser uma matriz de \<de cadeias de caracteres, de objeto >.|Não|


## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas de suporte para atividades do Databricks

Na definição de atividade Databricks acima, especificar esses tipos de biblioteca: *jar*, *ovo*, *maven*, *pypi*,  *cran*.

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

Para obter mais detalhes, consulte a [documentação Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Utilizar a área de trabalho do Databricks da interface do Usuário](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho de dbfs da biblioteca foi adicionado com interface do Usuário, pode utilizar o [CLI do Databricks (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas de Jar são armazenadas em dbfs: / FileStore/jars ao utilizar a interface do Usuário. Pode listar todos os através da CLI: *databricks fs ls dbfs: / FileStore/jars*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Biblioteca de cópia com a CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks fs cp SparkPi-assemblagem-0.1.jar dbfs: / FileStore/jars*
