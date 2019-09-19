---
title: Copiar dados de e para o Azure Database para PostgreSQL usando Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de e para o Azure Database para PostgreSQL usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: jingwang
ms.openlocfilehash: 4cb6f420b6d084539dc98a09632d0760a1344012
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092181"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-using-azure-data-factory"></a>Copiar dados de e para o Azure Database para PostgreSQL usando Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados da base de dados do Azure para PostgreSQL. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

Esse conector é especializado para o [serviço banco de dados do Azure para PostgreSQL](../postgresql/overview.md). Para copiar dados do banco de dado PostgreSQL genérico localizado localmente ou na nuvem, use o [conector PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Esse conector do banco de dados do Azure para PostgreSQL tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Pode copiar dados da base de dados do Azure para PostgreSQL para qualquer arquivo de dados de sink suportados. Ou então, você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Database para PostgreSQL. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade, portanto não precisa de instalar manualmente a qualquer driver utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas à base de dados do Azure para PostgreSQL conector.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para a base de dados do Azure para o serviço PostgreSQL ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **AzurePostgreSql** | Sim |
| connectionString | Uma cadeia de ligação de ODBC para ligar à base de dados do Azure para PostgreSQL.<br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar a senha em Azure Key Vault e efetuar pull da `password` configuração da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

É uma cadeia de ligação típica `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Mais de propriedades que pode ser definido por seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| O método o driver usa para encriptar os dados enviados entre o driver e o servidor de base de dados. Por exemplo,`EncryptionMethod=<0/1/6>;`| 0 (sem encriptação) **(predefinição)** / 1 (SSL) / 6 (RequestSSL) | Não |
| ValidateServerCertificate (VSC) | Determina se o driver valida o certificado que é enviado pelo servidor de base de dados quando estiver ativada a encriptação SSL (método de encriptação = 1). Por exemplo,`ValidateServerCertificate=<0/1>;`| 0 (desativado) **(predefinição)** / 1 (ativado) | Não |

**Example:**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
            }
        }
    }
}
```

**Exemplo: armazenar a senha no Azure Key Vault**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela base de dados do Azure para o conjunto de dados PostgreSQL.

Para copiar dados da base de dados do Azure para PostgreSQL, defina a propriedade de tipo de conjunto de dados para **AzurePostgreSqlTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **AzurePostgreSqlTable** | Sim |
| tableName | Nome da tabela. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela base de dados do Azure para PostgreSQL origem.

### <a name="azure-database-for-postgresql-as-source"></a>Base de dados do Azure para PostgreSql como origem

Para copiar dados da base de dados do Azure para PostgreSQL, defina o tipo de origem na atividade de cópia para **AzurePostgreSqlSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **AzurePostgreSqlSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Banco de dados do Azure para PostgreSQL como coletor

Para copiar dados para o banco de dado do Azure para PostgreSQL, há suporte para as seguintes propriedades na seção **coletor** de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como: **AzurePostgreSQLSink** | Sim |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar os dados no Azure Database para PostgreSQL em cada execução. Você pode usar essa propriedade para limpar os dados pré-carregados. | Não |
| writeBatchSize | Insere dados na tabela Azure Database para PostgreSQL quando o tamanho do buffer atinge writeBatchSize.<br>O valor permitido é um inteiro que representa o número de linhas. | Não (o padrão é 10.000) |
| writeBatchTimeout | Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite.<br>Os valores permitidos são TimeSpan. Um exemplo é 00:30:00 (30 minutos). | Não (o padrão é 00:00:30) |

**Example:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
