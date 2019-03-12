---
title: Copiar dados do Amazon Redshift, com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como copiar dados do Amazon Redshift armazenamentos de dados de sink suportados através do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 7fcf08c36d8babd0a318ed5b912823c344f4ce64
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549957"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copiar dados do Amazon Redshift, com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-amazon-redshift-connector.md)
> * [Versão atual](connector-amazon-redshift.md)


Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de um Amazon Redshift. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados do Amazon Redshift para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Amazon Redshift suporta a recuperação de dados do Redshift através de consulta ou suporte incorporado de descarregar Redshift.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados do Redshift, considere usar o descarregar Redshift internos através do Amazon S3. Ver [descarregamento de utilização para copiar dados do Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) secção para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver a copiar para um dados no local do arquivo de dados usando [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md), conceder o acesso ao cluster do Amazon Redshift de Runtime de integração (endereço IP da utilização da máquina). Ver [autorizar o acesso ao cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções.
* Se estiver a copiar dados para um arquivo de dados do Azure, veja [intervalos de IP do Centro de dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) para o endereço IP de computação e os intervalos de SQL utilizados pelos dados do Azure centra-se.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector Amazon Redshift.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Amazon Redshift:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **AmazonRedshift** | Sim |
| servidor |Nome anfitrião ou endereço IP do servidor do Amazon Redshift. |Sim |
| porta |O número da porta TCP que o servidor do Amazon Redshift utiliza para escutar ligações de cliente. |Não, a predefinição é 5439 |
| base de dados |Nome da base de dados do Amazon Redshift. |Sim |
| nome do utilizador |Nome de utilizador que tenha acesso à base de dados. |Sim |
| palavra-passe |Palavra-passe da conta de utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Amazon Redshift.

Para copiar dados do Amazon Redshift, defina a propriedade de tipo de conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados deve ser definida como: **RelationalTable** | Sim |
| tableName | Nome da tabela em que o Amazon Redshift. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas por origem do Amazon Redshift.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift como origem

Para copiar dados do Amazon Redshift, defina o tipo de origem na atividade de cópia para **AmazonRedshiftSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **AmazonRedshiftSource** | Sim |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecionar * de MyTable. |Não (se for especificado "tableName" no conjunto de dados) |
| redshiftUnloadSettings | Grupo de propriedade ao utilizar UNLOAD do Amazon Redshift. | Não |
| s3LinkedServiceName | Refere-se a um Amazon S3 to-be-utilizado como um armazenamento provisório, especificando um nome de serviço ligado do tipo "AmazonS3". | Sim, se utilizar o descarregamento |
| bucketName | Indica o registo de S3 para armazenar os dados intermediárias. Se não for indicado, serviço Data Factory gera automaticamente.  | Sim, se utilizar o descarregamento |

**Exemplo: Origem do Amazon Redshift na atividade de cópia com o descarregamento**

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

Saiba mais sobre como utilizar UNLOAD para copiar dados do Amazon Redshift com eficiência a partir da secção seguinte.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Utilizar o descarregamento para copiar dados do Amazon Redshift

[DESCARREGAR](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) é um mecanismo fornecido pelo Amazon Redshift, que pode descarregar os resultados de uma consulta para um ou mais ficheiros no Amazon Simple Storage Service (Amazon S3). É a forma recomendada pela Amazon para copiar o conjunto de dados grande do Redshift.

**Exemplo: copiar dados do Amazon Redshift para o Azure SQL Data Warehouse, utilizando o descarregamento, cópia faseada e o PolyBase**

Para este exemplo de caso de utilização, copiar atividade descarregamentos de dados do Amazon Redshift Amazon S3 conforme configurado na "redshiftUnloadSettings" e, em seguida, copie os dados do Amazon S3 para BLOBs do Azure conforme especificado em "stagingSettings", por último utilizam o PolyBase para carregar dados para dados do SQL Armazém. O formato intermediário é manipulado pela atividade de cópia corretamente.

![Redshift para o fluxo de trabalho do armazém de dados do SQL cópia](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

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

## <a name="data-type-mapping-for-amazon-redshift"></a>Mapeamento de tipo de dados para o Amazon Redshift

Ao copiar dados do Amazon Redshift, os seguintes mapeamentos são utilizados entre tipos de dados do Amazon Redshift aos tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do Amazon Redshift | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BIGINT |Int64 |
| VALOR BOOLEANO |String |
| CHAR |String |
| DATA |DateTime |
| DECIMAL |Decimal |
| PRECISÃO DUPLA |Double |
| NÚMERO INTEIRO |Int32 |
| REAL |Single |
| SMALLINT |Int16 |
| TEXTO |String |
| TIMESTAMP |DateTime |
| VARCHAR |String |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
