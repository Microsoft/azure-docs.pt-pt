---
title: Copiar dados do Amazon redshift usando o Azure Data Factory
description: Saiba mais sobre como copiar dados do Amazon redshift para armazenamentos de dados de coletor com suporte usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: jingwang
ms.openlocfilehash: 57152c7d4aa558c2d6dd7c4ef0ad2c62311fc0c6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681373"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copiar dados do Amazon redshift usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-amazon-redshift-connector.md)
> * [Versão atual](connector-amazon-redshift.md)


Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um Amazon redshift. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Amazon redshift tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Amazon redshift para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector do Amazon redshift dá suporte à recuperação de dados do redshift usando consulta ou suporte interno a UNLOAD de redshift.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados do redshift, considere o uso do descarregamento redshift interno por meio do Amazon S3. Consulte [a seção usar Unload para copiar dados do Amazon redshift](#use-unload-to-copy-data-from-amazon-redshift) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver copiando dados para um armazenamento de dados local usando o [autohospedado Integration Runtime](create-self-hosted-integration-runtime.md), conceda Integration Runtime (use o endereço IP da máquina) o acesso ao cluster do Amazon redshift. Consulte [autorizar o acesso ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções.
* Se você estiver copiando dados para um armazenamento de dados do Azure, consulte [intervalos de IP do Data Center do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para o endereço IP de computação e intervalos SQL usados pelos data centers do Azure.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do Amazon redshift.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Amazon redshift:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **AmazonRedshift** | Sim |
| servidor |Endereço IP ou nome do host do servidor Amazon redshift. |Sim |
| porta |O número da porta TCP que o servidor Amazon redshift usa para escutar conexões de cliente. |Não, o padrão é 5439 |
| base de dados |Nome do banco de dados do Amazon redshift. |Sim |
| o nome de utilizador |Nome do usuário que tem acesso ao banco de dados. |Sim |
| palavra-passe |Senha da conta de usuário. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar os Integration Runtime Azure Integration Runtime ou auto-hospedado (se o armazenamento de dados estiver localizado em rede privada). Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

**Exemplo:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de redshift do Amazon.

Para copiar dados do Amazon redshift, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **AmazonRedshiftTable** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| tabela | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Use `schema` e `table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estivesse usando `RelationalTable` dataset tipado, ele ainda tem suporte como está, enquanto você é sugerido para usar o novo no futuro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela fonte do Amazon redshift.

### <a name="amazon-redshift-as-source"></a>Amazon redshift como fonte

Para copiar dados do Amazon redshift, defina o tipo de fonte na atividade de cópia como **AmazonRedshiftSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **AmazonRedshiftSource** | Sim |
| consulta |Use a consulta personalizada para ler os dados. Por exemplo: selecione * em MyTable. |Não (se "TableName" no DataSet for especificado) |
| redshiftUnloadSettings | Grupo de propriedades ao usar o descarregamento do Amazon redshift. | Não |
| s3LinkedServiceName | Refere-se a um Amazon S3 a ser usado como um armazenamento provisório, especificando um nome de serviço vinculado do tipo "AmazonS3". | Sim se estiver usando UNLOAD |
| bucketName | Indique o Bucket S3 para armazenar os dados provisórios. Se não for fornecido, Data Factory serviço o gerará automaticamente.  | Sim se estiver usando UNLOAD |

**Exemplo: origem do Amazon redshift na atividade de cópia usando UNLOAD**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Saiba mais sobre como usar o UNLOAD para copiar dados do Amazon redshift com eficiência na próxima seção.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Usar UNLOAD para copiar dados do Amazon redshift

[Unload](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) é um mecanismo fornecido pelo Amazon redshift, que pode descarregar os resultados de uma consulta em um ou mais arquivos no Amazon S3 (Simple Storage Service). É a maneira recomendada pela Amazon para copiar um conjunto de dados grandes do redshift.

**Exemplo: copiar dados do Amazon redshift para o Azure SQL Data Warehouse usando UNLOAD, cópia preparada e polybase**

Para este caso de uso de exemplo, a atividade de cópia descarrega os dados do Amazon redshift para o Amazon S3 conforme configurado em "redshiftUnloadSettings" e, em seguida, copia dados do Amazon S3 para o blob do Azure, conforme especificado em "stagingSettings", por fim, use o polybase para carregar dados em dados SQL Armazén. Todo o formato provisório é manipulado pela atividade de cópia corretamente.

![Fluxo de trabalho de cópia do redshift para SQL DW](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapeamento de tipo de dados para Amazon redshift

Ao copiar dados do Amazon redshift, os seguintes mapeamentos são usados de tipos de dados do Amazon redshift para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

| Tipo de dados do Amazon redshift | Tipo de dados provisório do data Factory |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |String |
| º |String |
| DATA |DateTime |
| VÍRGULA |Vírgula |
| PRECISÃO DUPLA |Clique |
| VALORES |Int32 |
| REAL |Único |
| SMALLINT |Int16 |
| TEXTO |String |
| ESTAMPA |DateTime |
| VARCHAR |String |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
