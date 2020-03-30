---
title: Copiar dados de e para a Base de Dados Azure para mySQL
description: Saiba como copiar dados de e para a Base de Dados Azure para o MySQL utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2019
ms.openlocfilehash: f5203de43a3d12c40e6cb5bbb33547769f5824c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440784"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>Copiar dados de e para a Base de Dados Azure para mySQL utilizando a Azure Data Factory

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados da Base de Dados Azure para o MySQL. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

Este conector é especializado para a Base de [Dados Azure para o serviço MySQL](../mysql/overview.md). Para copiar dados da base de dados genérica mySQL localizada no local ou na nuvem, utilize o [conector MySQL](connector-mysql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Esta Base de Dados Azure para conector MySQL é suportada para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Base de Dados Azure para o MySQL para qualquer loja de dados de sink suportado. Ou pode copiar dados de qualquer loja de dados de origem suportada para a Base de Dados Azure para o MySQL. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade, pelo que não necessita de instalar manualmente qualquer controlador utilizando este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas da Base de Dados Azure para o conector MySQL.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para a Base de Dados Azure para o serviço ligado ao MySQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzureMySql** | Sim |
| conexãoString | Especifique as informações necessárias para se ligar à base de dados Azure para a instância MySQL. <br/> Também pode colocar palavra-passe no Cofre `password` de Chaves Azure e retirar a configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no](store-credentials-in-key-vault.md) artigo do Cofre chave Azure com mais detalhes. | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

Uma corda de `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`ligação típica é . Mais propriedades que pode definir no seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| SSLMode | Esta opção especifica se o condutor utiliza encriptação e verificação SSL ao ligar-se ao MySQL. Por exemplo, `SSLMode=<0/1/2/3/4>`| Desativado (0) / PREFERRED (1) **(Predefinido)** / EXIGIDO (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Não |
| UseSystemTrustStore | Esta opção especifica se deve utilizar um certificado CA a partir da loja fiduciário do sistema, ou de um ficheiro PEM especificado. Por exemplo, `UseSystemTrustStore=<0/1>;`| Ativado (1) / Desativado (0) **(Predefinido)** | Não |

**Exemplo:**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: palavra-passe de loja no Cofre de Chaves Azure**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pela Base de Dados Azure para dataset MySQL.

Para copiar dados da Base de Dados Azure para o MySQL, detete a propriedade do tipo do conjunto de dados para **AzureMySqlTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **AzureMySqlTable** | Sim |
| tableName | Nome da tabela na base de dados MySQL. | Não (se for especificada a "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela Azure Database para a fonte e pia MySQL.

### <a name="azure-database-for-mysql-as-source"></a>Base de Dados Azure para MySQL como fonte

Para copiar dados da Base de Dados Azure para o MySQL, as seguintes propriedades são suportadas na secção de **origem** de atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **AzureMySqlSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |
| consultaCommandTimeout | O tempo de espera antes da consulta pedir tempo saem. O predefinido é de 120 minutos (02:00:00) | Não |

**Exemplo:**

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

### <a name="azure-database-for-mysql-as-sink"></a>Base de Dados Azure para MySQL como pia

Para copiar dados para a Base de Dados Azure para o MySQL, as seguintes propriedades são suportadas na secção de **sink da** atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para: **AzureMySqlSink** | Sim |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia a executar antes de escrever dados na Base de Dados Azure para MySQL em cada execução. Pode usar esta propriedade para limpar os dados pré-carregados. | Não |
| escreverBatchSize | Insere os dados na base de dados Azure para a tabela MySQL quando o tamanho do tampão atinge o writeBatchSize.<br>O valor permitido é inteiro representando o número de linhas. | Não (o padrão é de 10.000) |
| escreverBatchTimeout | Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair.<br>Os valores permitidos são Timespan. Um exemplo é 00:30:00 (30 minutos). | Não (o padrão é 00:00:30) |

**Exemplo:**

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Mapeamento de tipo de dados para Base de Dados Azure para MySQL

Ao copiar dados da Base de Dados Azure para o MySQL, os seguintes mapeamentos são utilizados desde tipos de dados MySQL para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Base de Dados Azure para o tipo de dados MySQL | Tipo de dados provisórios da fábrica de dados |
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

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
