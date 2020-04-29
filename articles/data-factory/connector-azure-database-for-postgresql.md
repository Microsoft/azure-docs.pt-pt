---
title: Copiar dados de e para a Base de Dados Azure para PostgreSQL
description: Saiba como copiar dados de e para a Base de Dados Azure para PostgreSQL utilizando uma atividade de cópia num pipeline azure Data Factory.
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
ms.openlocfilehash: b85e72ae6698cd9fa018c940e158bfcf25279ed5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81410467"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiar dados de e para a Base de Dados Azure para PostgreSQL utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a funcionalidade de atividade de cópia na Azure Data Factory para copiar dados da Base de Dados Azure para PostgreSQL. Baseia-se na atividade copy no artigo da [Azure Data Factory,](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

Este conector é especializado para a Base de [Dados Azure para o serviço PostgreSQL](../postgresql/overview.md). Para copiar dados de uma base de dados genérica postgresQL localizada no local ou na nuvem, utilize o [conector PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Esta Base de Dados Azure para conector PostgreSQL é suportada para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com uma matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Base de Dados Azure para postgreSQL para qualquer loja de dados de sink suportado. Ou pode copiar dados de qualquer loja de dados de origem suportada para a Base de Dados Azure para PostgreSQL. Para obter uma lista de lojas de dados que a atividade de cópia suporta como fontes e pias, consulte a tabela de [lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade. Por isso, não é necessário instalar manualmente qualquer controlador para utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções oferecem detalhes sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas da Base de Dados Azure para conector PostgreSQL.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para a Base de Dados Azure para o serviço ligado postgreSQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzurePostgreSql**. | Sim |
| conexãoString | Uma cadeia de ligação ODBC para ligar à Base de Dados Azure para PostgreSQL.<br/>Também pode colocar uma palavra-passe no `password` Cofre de Chaves Azure e retirar a configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no Cofre de Chaves Azure](store-credentials-in-key-vault.md) para obter mais detalhes. | Sim |
| connectVia | Esta propriedade representa o tempo de execução da [integração](concepts-integration-runtime.md) a ser usado para se conectar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

Uma corda de `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`ligação típica é . Aqui estão mais propriedades que pode definir no seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| Método de Encriptação (EM)| O método que o condutor utiliza para encriptar os dados enviados entre o controlador e o servidor de base de dados. Por exemplo`EncryptionMethod=<0/1/6>;`| 0 (Sem encriptação) **(Padrão)** / 1 (SSL) / 6 (RequestSSL) | Não |
| ValidaserverCertificate (VSC) | Determina se o controlador valida o certificado que é enviado pelo servidor de base de dados quando a encriptação SSL está ativada (Método de Encriptação=1). Por exemplo`ValidateServerCertificate=<0/1>;`| 0 (Desativado) **(Predefinido)** / 1 (Ativado) | Não |

**Exemplo:**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Exemplo:**

***Armazenar senha no Cofre de Chaves Azure***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [Datasets na Azure Data Factory](concepts-datasets-linked-services.md). Esta secção fornece uma lista de propriedades que a Base de Dados Azure para PostgreSQL suporta em conjuntos de dados.

Para copiar dados da Base de Dados Azure para PostgreSQL, detete a propriedade do tipo do conjunto de dados para **AzurePostgreSqlTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **AzurePostgreSqlTable** | Sim |
| tableName | Nome da mesa | Não (se for especificada a "consulta" na fonte de atividade) |

**Exemplo:**

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

Para obter uma lista completa de secções e imóveis disponíveis para definir [atividades, consulte Pipelines e atividades na Azure Data Factory.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por uma Base de Dados Azure para fonte PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Base de Dados Azure para PostgreSql como fonte

Para copiar dados da Base de Dados Azure para PostgreSQL, delineie o tipo de origem na atividade de cópia para **AzurePostgreSqlSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para **AzurePostgreSqlSource** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"` | Não (se a propriedade tableName no conjunto de dados for especificada) |

**Exemplo:**

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

### <a name="azure-database-for-postgresql-as-sink"></a>Base de Dados Azure para PostgreSQL como pia

Para copiar dados para a Base de Dados Azure para postgresQL, as seguintes propriedades são suportadas na secção de **sink da** atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do sumidouro da atividade de cópia deve ser definida para **AzurePostgreSQLSink**. | Sim |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia a executar antes de escrever dados na Base de Dados Azure para PostgreSQL em cada execução. Pode usar esta propriedade para limpar os dados pré-carregados. | Não |
| escreverBatchSize | Insere os dados na base de dados Azure para a tabela PostgreSQL quando o tamanho do tampão atinge o writeBatchSize.<br>O valor permitido é um inteiro que representa o número de linhas. | Não (o padrão é de 10.000) |
| escreverBatchTimeout | Aguarde o tempo para que a operação de inserção do lote esteja concluída antes de sair.<br>Os valores permitidos são cordas Timespan. Um exemplo é 00:30:00 (30 minutos). | Não (o padrão é 00:00:30) |

**Exemplo:**

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para mais informações sobre as propriedades, consulte a [atividade de lookup na Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
