---
title: Copiar dados de e para a Base de Dados Azure para PostgreSQL
description: Saiba como copiar dados de e para a Azure Database for PostgreSQL utilizando uma atividade de cópia num pipeline da Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2020
ms.openlocfilehash: 11e0d3336f085ccae9a7fb83ed050d69a15ce42b
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296510"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiar dados de e para a base de dados Azure para PostgreSQL utilizando a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a funcionalidade de atividade de cópia na Azure Data Factory para copiar dados da Base de Dados Azure para PostgreSQL. Baseia-se na atividade Copy no artigo [da Azure Data Factory,](copy-activity-overview.md) que apresenta uma visão geral da atividade da cópia.

Este conector é especializado para o [serviço Azure Database for PostgreSQL](../postgresql/overview.md). Para copiar dados de uma base de dados pós-QL genérica localizada no local ou na nuvem, utilize o [conector PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Esta Base de Dados Azure para conector PostgreSQL é suportada para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com uma [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da Base de Dados Azure para PostgreSQL para qualquer loja de dados de sumidouro suportado. Ou, pode copiar dados de qualquer loja de dados de origem suportada para a Base de Dados Azure para PostgreSQL. Para obter uma lista de lojas de dados que a atividade de cópia suporta como fontes e sumidouros, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

A Azure Data Factory fornece um controlador incorporado para permitir a conectividade. Por isso, não é necessário instalar manualmente nenhum controlador para utilizar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções oferecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas da Base de Dados Azure para conector PostgreSQL.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para a Base de Dados Azure para o serviço ligado a PostgreSQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **AzurePostgreSql**. | Yes |
| conexãoStragem | Um fio de ligação ODBC para ligar à Base de Dados Azure para PostgreSQL.<br/>Também pode colocar uma palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no Cofre da Chave Azure](store-credentials-in-key-vault.md) para obter mais detalhes. | Yes |
| connectVia | Esta propriedade representa o [tempo de integração](concepts-integration-runtime.md) a ser usado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada em rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

Uma cadeia de ligação típica é `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Aqui estão mais propriedades que pode definir por seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| Método de Encriptação (EM)| O método que o condutor utiliza para encriptar os dados enviados entre o controlador e o servidor de base de dados. Por exemplo  `EncryptionMethod=<0/1/6>;`| 0 (Sem encriptação) **(Padrão)** / 1 (SSL) / 6 (RequestSSL) | No |
| ValidaçãoServerCertificato (VSC) | Determina se o controlador valida o certificado enviado pelo servidor de base de dados quando a encriptação SSL está ativada (Método de Encriptação=1). Por exemplo  `ValidateServerCertificate=<0/1>;`| 0 (Desativado) **(Predefinição)** / 1 (Habilitado) | No |

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

**_Armazenar senha em Azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte [datasets na Azure Data Factory](concepts-datasets-linked-services.md). Esta secção fornece uma lista de propriedades que a Base de Dados Azure para PostgreSQL suporta em conjuntos de dados.

Para copiar os dados da Base de Dados Azure para PostgreSQL, defina a propriedade tipo do conjunto de dados para _*AzurePostgreSqlTable***. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para **AzurePostgreSqlTable** | Yes |
| tableName | Nome da mesa | Não (se for especificada "consulta" na fonte de atividade) |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir [atividades, consulte Pipelines e atividades na Azure Data Factory.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por uma Base de Dados Azure para fonte PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Base de Dados Azure para PostgreSql como fonte

Para copiar os dados da Base de Dados Azure para PostgreSQL, deslote o tipo de origem na atividade da cópia para **AzurePostgreSqlSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para **AzurePostgreSqlSource** | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `SELECT * FROM mytable` ou `SELECT * FROM "MyTable"` . . Nota em PostgreSQL, o nome da entidade é tratado como caso-insensível se não for citado. | Não (se a propriedade tableName no conjunto de dados for especificada) |

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
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Base de Dados Azure para PostgreSQL como pia

Para copiar dados para a Base de Dados Azure para PostgreSQL, as seguintes propriedades são suportadas na secção de **lavatório** de atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do lavatório de atividade de cópia deve ser definida para **AzurePostgreSQLSink**. | Yes |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia a executar antes de escrever dados na Base de Dados Azure para PostgreSQL em cada execução. Pode utilizar esta propriedade para limpar os dados pré-carregados. | No |
| escreverBatchSize | Insere dados na base de dados Azure para a tabela PostgreSQL quando o tamanho do tampão atinge o writeBatchSize.<br>Valor permitido é um número inteiro que representa o número de linhas. | Não (o padrão é 10.000) |
| escreverBatchTimeout | Tempo de espera para que o funcionamento do encaixe do lote esteja concluído antes de esgotar o tempo.<br>Os valores permitidos são as cordas timespan. Um exemplo é 00:30:00 (30 minutos). | Não (padrão é 00:00:30) |

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

Para obter mais informações sobre as propriedades, consulte [a atividade da Lookup na Azure Data Factory.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte [lojas de dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
