---
title: Copiar dados do Amazon redshift usando o Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: 38eb57b6f6a04dac7d77bbdddf54695af50a43c6
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71008590"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copiar dados do Amazon redshift usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-amazon-redshift-connector.md)
> * [Versão atual](connector-amazon-redshift.md)


Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um Amazon redshift. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do Amazon redshift tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Amazon redshift para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector do Amazon redshift dá suporte à recuperação de dados do redshift usando consulta ou suporte interno a UNLOAD de redshift.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados do redshift, considere o uso do descarregamento redshift interno por meio do Amazon S3. Consulte [a seção usar Unload para copiar dados do Amazon redshift](#use-unload-to-copy-data-from-amazon-redshift) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

* Se você estiver copiando dados para um armazenamento de dados local usando o [autohospedado Integration Runtime](create-self-hosted-integration-runtime.md), conceda Integration Runtime (use o endereço IP da máquina) o acesso ao cluster do Amazon redshift. Consulte [autorizar o acesso ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções.
* Se você estiver copiando dados para um armazenamento de dados do Azure, consulte [intervalos de IP do Data Center do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para o endereço IP de computação e intervalos SQL usados pelos data centers do Azure.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do Amazon redshift.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do Amazon redshift:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **AmazonRedshift** | Sim |
| server |Endereço IP ou nome do host do servidor Amazon redshift. |Sim |
| port |O número da porta TCP que o servidor Amazon redshift usa para escutar conexões de cliente. |Não, o padrão é 5439 |
| database |Nome do banco de dados do Amazon redshift. |Sim |
| username |Nome do usuário que tem acesso ao banco de dados. |Sim |
| password |Senha da conta de usuário. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de redshift do Amazon.

Para copiar dados do Amazon redshift, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **AmazonRedshiftTable** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| table | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Use `schema` e`table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

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

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela fonte do Amazon redshift.

### <a name="amazon-redshift-as-source"></a>Amazon redshift como fonte

Para copiar dados do Amazon redshift, defina o tipo de fonte na atividade de cópia como **AmazonRedshiftSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **AmazonRedshiftSource** | Sim |
| query |Use a consulta personalizada para ler os dados. Por exemplo: selecione * em MyTable. |Não (se for especificado "tableName" no conjunto de dados) |
| redshiftUnloadSettings | Grupo de propriedades ao usar o descarregamento do Amazon redshift. | Não |
| s3LinkedServiceName | Refere-se a um Amazon S3 a ser usado como um armazenamento provisório, especificando um nome de serviço vinculado do tipo "AmazonS3". | Sim se estiver usando UNLOAD |
| bucketName | Indique o Bucket S3 para armazenar os dados provisórios. Se não for fornecido, Data Factory serviço o gerará automaticamente.  | Sim se estiver usando UNLOAD |

**Exemplo: Origem do Amazon redshift na atividade de cópia usando UNLOAD**

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

Ao copiar dados do Amazon redshift, os seguintes mapeamentos são usados de tipos de dados do Amazon redshift para Azure Data Factory tipos de dados provisórios. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do Amazon redshift | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |Cadeia |
| º |Cadeia |
| DATE |DateTime |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| REAL |Single |
| SMALLINT |Int16 |
| TEXT |Cadeia |
| TIMESTAMP |DateTime |
| VARCHAR |Cadeia |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
