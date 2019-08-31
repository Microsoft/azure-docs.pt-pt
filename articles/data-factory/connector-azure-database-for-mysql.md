---
title: Copiar dados de e para o Azure Database para MySQL usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados para e do banco de dados do Azure para MySQL usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: jingwang
ms.openlocfilehash: b6d96ef2d2cdd79bec35f2581876823990e4a971
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172606"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Copiar dados de e para o Azure Database para MySQL usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do banco de dados do Azure para MySQL. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

Esse conector é especializado para o [serviço de banco de dados do Azure para MySQL](../mysql/overview.md). Para copiar dados do banco de dado do MySQL genérico localizado localmente ou na nuvem, use o [conector do MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Você pode copiar dados de um banco de dados do Azure para MySQL para qualquer armazenamento de dado de coletor com suporte. Ou então, você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Database para MySQL. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

O Azure Data Factory fornece um driver incorporado para permitir a conectividade, portanto não precisa de instalar manualmente a qualquer driver utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do banco de dados do Azure para MySQL.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do banco de dados do Azure para MySQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **AzureMySql** | Sim |
| connectionString | Especifique as informações necessárias para se conectar à instância do banco de dados do Azure para MySQL. <br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar a senha em Azure Key Vault e efetuar pull da `password` configuração da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais no artigo Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

É uma cadeia de ligação típica `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Mais de propriedades que pode ser definido por seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| SSLMode | Esta opção especifica se o driver usa a criptografia SSL e a verificação ao se conectar ao MySQL. Por exemplo, `SSLMode=<0/1/2/3/4>`| DISABLED (0)/preferencial (1) **(padrão)** /obrigatório (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Não |
| UseSystemTrustStore | Esta opção especifica se um certificado de autoridade de certificação deve ser usado do repositório de confiança do sistema ou de um arquivo PEM especificado. Por exemplo, `UseSystemTrustStore=<0/1>;`| Habilitado (1)/desabilitado (0) **(padrão)** | Não |

**Example:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a senha no Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo banco de dados do Azure para MySQL.

Para copiar dados do banco de dados do Azure para MySQL, defina a propriedade Type do DataSet como **AzureMySqlTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **AzureMySqlTable** | Sim |
| tableName | Nome da tabela no banco de dados MySQL. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do banco de dados do Azure para MySQL.

### <a name="azure-database-for-mysql-as-source"></a>Banco de dados do Azure para MySQL como fonte

Para copiar dados do Azure Database para MySQL, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **AzureMySqlSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |
| queryCommandTimeout | O tempo de espera antes que a solicitação de consulta expire. O padrão é 120 minutos (02:00:00) | Não |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Banco de dados do Azure para MySQL como coletor

Para copiar dados para o banco de dado do Azure para MySQL, há suporte para as seguintes propriedades na seção **coletor** de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do coletor da atividade de cópia deve ser definida como: **AzureMySqlSink** | Sim |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar dados no banco de dado do Azure para MySQL em cada execução. Você pode usar essa propriedade para limpar os dados pré-carregados. | Não |
| writeBatchSize | Insere os dados na tabela do Azure Database para MySQL quando o tamanho do buffer atinge writeBatchSize.<br>O valor permitido é um inteiro que representa o número de linhas. | Não (o padrão é 10.000) |
| writeBatchTimeout | Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite.<br> 
Os valores permitidos são TimeSpan. Um exemplo é 00:30:00 (30 minutos). | Não (o padrão é 00:00:30) |

**Example:**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapeamento de tipo de dados para o banco de dado do Azure para MySQL

Durante a cópia dos dados do banco de dados do Azure para MySQL, os seguintes mapeamentos são usados de tipos de dado do MySQL para Azure Data Factory tipos de dados provisórios. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados Azure database for MySQL | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
