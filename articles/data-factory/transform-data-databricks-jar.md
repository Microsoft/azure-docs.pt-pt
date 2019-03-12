---
title: Transformar dados com o Jar do Databricks - Azure | Documentos da Microsoft
description: Saiba como processar ou transformar dados executando um Jar do Databricks.
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
ms.openlocfilehash: d299a785d50657ef40c0c49cb2dce33b8939fd02
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575873"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transforme dados executando uma atividade de Jar no Azure Databricks

O Azure Databricks Jar atividade num [pipeline do Data Factory](concepts-pipelines-activities.md) executa um Jar do Spark no seu cluster do Azure Databricks. Este artigo baseia-se a [atividades de transformação de dados](transform-data.md) artigo, que apresenta uma visão geral de transformação de dados e as atividades de transformação suportados. O Azure Databricks é uma plataforma gerida para a execução do Apache Spark.

Para uma introdução e demonstração de onze minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definição de atividade do Databricks Jar

Eis a definição de JSON de exemplo de uma atividade do Databricks Jar:

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

## <a name="databricks-jar-activity-properties"></a>Propriedades de atividade do Databricks Jar

A tabela seguinte descreve as propriedades JSON utilizadas na definição de JSON:

|Propriedade|Descrição|Necessário|
|:--|---|:-:|
|name|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que faz a atividade.|Não|
|tipo|Para a atividade do Databricks Jar, o tipo de atividade é DatabricksSparkJar.|Sim|
|linkedServiceName|Nome do serviço ligado Databricks no qual a atividade de Jar é executada. Para saber mais sobre este serviço ligado, veja [serviços ligados de computação](compute-linked-services.md) artigo.|Sim|
|mainClassName|O nome completo da classe que contém o método principal a ser executado. Essa classe tem de estar contida num POTE fornecido como uma biblioteca.|Sim|
|parâmetros|Parâmetros que serão passados para o método principal.  Esta é uma matriz de cadeias de caracteres.|Não|
|Bibliotecas|Uma lista de bibliotecas seja instalado no cluster que executará a tarefa. Pode ser uma matriz de < string, objeto >|Sim (pelo menos um que contém o método mainClassName)|

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

Normalmente, as bibliotecas de Jar são armazenadas em dbfs: / FileStore/jars ao utilizar a interface do Usuário. Pode listar todos os através da CLI: *databricks fs ls dbfs: / FileStore/tarefa-jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Biblioteca de cópia com a CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Utilizar a CLI do Databricks [(passos de instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Exemplo - copiar JAR para dbfs: *dbfs cp SparkPi-assemblagem-0.1.jar dbfs:/docs/sparkpi.jar*
