---
title: Copiar dados de e para o banco de dado do Azure para PostgreSQL
description: Saiba como copiar dados de e para o Azure Database para PostgreSQL usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: 3729d43716a0f4e133fb175da2546aac560b0525
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931618"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiar dados de e para o banco de dado do Azure para PostgreSQL usando Azure Data Factory

Este artigo descreve como usar o recurso de atividade de cópia no Azure Data Factory para copiar dados do banco de dados do Azure para PostgreSQL. Ele se baseia na [atividade de cópia no artigo Azure data Factory](copy-activity-overview.md) , que apresenta uma visão geral da atividade de cópia.

Esse conector é especializado para o [serviço banco de dados do Azure para PostgreSQL](../postgresql/overview.md). Para copiar dados de um banco de dado PostgreSQL genérico localizado localmente ou na nuvem, use o [conector PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Esse conector do banco de dados do Azure para PostgreSQL tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com uma [fonte/matriz de coletor com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Pode copiar dados da base de dados do Azure para PostgreSQL para qualquer arquivo de dados de sink suportados. Ou então, você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Database para PostgreSQL. Para obter uma lista de armazenamentos de dados com suporte da atividade de cópia como fontes e coletores, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

O Azure Data Factory fornece um driver incorporado para permitir a conectividade. Portanto, você não precisa instalar manualmente nenhum driver para usar esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir oferecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas para o conector do banco de dados do Azure para PostgreSQL.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do banco de dados do Azure para PostgreSQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **AzurePostgreSql**. | Sim |
| connectionString | Uma cadeia de ligação de ODBC para ligar à base de dados do Azure para PostgreSQL.<br/>Marque este campo como uma SecureString para armazená-lo com segurança em Data Factory. Você também pode colocar uma senha em Azure Key Vault e extrair a configuração de `password` da cadeia de conexão. Consulte os exemplos a seguir e [armazene as credenciais em Azure Key Vault](store-credentials-in-key-vault.md) para obter mais detalhes. | Sim |
| connectVia | Essa propriedade representa o [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

É uma cadeia de ligação típica `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Aqui estão mais propriedades que podem ser definidas de acordo com o seu caso:

| Propriedade | Descrição | Opções | Obrigatório |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| O método o driver usa para encriptar os dados enviados entre o driver e o servidor de base de dados. Por exemplo, `EncryptionMethod=<0/1/6>;`| 0 (sem encriptação) **(predefinição)** / 1 (SSL) / 6 (RequestSSL) | Não |
| ValidateServerCertificate (VSC) | Determina se o driver valida o certificado enviado pelo servidor de banco de dados quando a criptografia SSL está habilitada (método de criptografia = 1). Por exemplo, `ValidateServerCertificate=<0/1>;`| 0 (desativado) **(predefinição)** / 1 (ativado) | Não |

**Exemplo**:

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

**Exemplo**:

***Armazenar a senha no Azure Key Vault***

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

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte [DataSets in Azure data Factory](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades às quais o banco de dados do Azure para PostgreSQL dá suporte em DataSets.

Para copiar dados da base de dados do Azure para PostgreSQL, defina a propriedade de tipo de conjunto de dados para **AzurePostgreSqlTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como **AzurePostgreSqlTable** | Sim |
| tableName | Nome da tabela | Não (se for especificada "query" na origem de atividade) |

**Exemplo**:

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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [pipelines e atividades no Azure data Factory](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte por uma fonte do banco de dados do Azure para PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Base de dados do Azure para PostgreSql como origem

Para copiar dados da base de dados do Azure para PostgreSQL, defina o tipo de origem na atividade de cópia para **AzurePostgreSqlSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como **AzurePostgreSqlSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"` | Não (se a Propriedade TableName no DataSet for especificada) |

**Exemplo**:

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

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade Type do coletor da atividade de cópia deve ser definida como **AzurePostgreSQLSink**. | Sim |
| preCopyScript | Especifique uma consulta SQL para que a atividade de cópia seja executada antes de gravar os dados no Azure Database para PostgreSQL em cada execução. Você pode usar essa propriedade para limpar os dados pré-carregados. | Não |
| writeBatchSize | Insere dados na tabela Azure Database para PostgreSQL quando o tamanho do buffer atinge writeBatchSize.<br>O valor permitido é um inteiro que representa o número de linhas. | Não (o padrão é 10.000) |
| writeBatchTimeout | Tempo de espera para que a operação de inserção em lote seja concluída antes de atingir o tempo limite.<br>Os valores permitidos são cadeias de caracteres TimeSpan. Um exemplo é 00:30:00 (30 minutos). | Não (o padrão é 00:00:30) |

**Exemplo**:

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

Para obter mais informações sobre as propriedades, consulte [atividade de pesquisa em Azure data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
