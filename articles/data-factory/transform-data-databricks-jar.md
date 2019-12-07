---
title: Transformar dados com o JAR do databricks
description: Saiba como processar ou transformar dados executando um jar do databricks.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 4a01a21259c4957b6f497bf213a3ef53f940bab7
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893965"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformar dados executando uma atividade jar no Azure Databricks

A atividade de Azure Databricks jar em um [pipeline de data Factory](concepts-pipelines-activities.md) executa um jar do Spark em seu cluster de Azure Databricks. Este artigo se baseia nas [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral da transformação de dados e das atividades de transformação com suporte. Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definição da atividade de jar do databricks

Aqui está a definição de JSON de exemplo de uma atividade de jar do databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Propriedades da atividade de jar do databricks

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|Descrição|Obrigatório|
|:--|---|:-:|
|nome|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que a atividade faz.|Não|
|tipo|Para a atividade jar do databricks, o tipo de atividade é DatabricksSparkJar.|Sim|
|linkedServiceName|Nome do serviço vinculado do databricks no qual a atividade jar é executada. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) .|Sim|
|mainClassName|O nome completo da classe que contém o método principal a ser executado. Essa classe deve estar contida em um JAR fornecido como uma biblioteca.|Sim|
|parâmetros|Parâmetros que serão passados para o método Main.  Esta é uma matriz de cadeias de caracteres.|Não|
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster que executará o trabalho. Pode ser uma matriz de cadeia de caracteres <, objeto >|Sim (pelo menos uma contendo o método mainClassName)|

> [!NOTE]
> **Problema conhecido** – ao usar o mesmo [cluster interativo](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) para executar atividades do jar do databricks simultâneos (sem a reinicialização do cluster), há um problema conhecido no databricks em que os parâmetros da 1ª atividade também serão usados pelas atividades a seguir. Portanto, isso resulta em parâmetros incorretos que estão sendo passados para os trabalhos subsequentes. Para atenuar isso, use um [cluster de trabalho](compute-linked-services.md#example---using-new-job-cluster-in-databricks) . 

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

Normalmente, as bibliotecas jar são armazenadas em dBFS:/filestore/jars ao usar a interface do usuário. Você pode listar tudo por meio da CLI: *databricks FS ls dBFS:/filestore/Job-jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copiar biblioteca usando a CLI do databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Use a CLI do databricks [(etapas de instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Exemplo-copiando JAR para dBFS: *dBFS CP sparkpi-assembly-0,1. jar dBFS:/docs/SparkPi. jar*
