---
title: Copiar dados do MySQL utilizando a Azure Data Factory
description: Saiba mais sobre o conector MySQL na Azure Data Factory que permite copiar dados de uma base de dados MySQL para uma loja de dados suportada como pia.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: jingwang
ms.openlocfilehash: b6a2253a4a124fe5e3725863c799f91714e66cab
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375260"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Copiar dados do MySQL utilizando a Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-mysql-connector.md)
> * [Versão atual](connector-mysql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados MySQL. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

>[!NOTE]
>Para copiar dados de ou para Azure Database para o serviço [MySQL,](../mysql/overview.md) utilize a [Base de Dados Azure especializada para o conector MySQL](connector-azure-database-for-mysql.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector MySQL é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados MySQL para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector MySQL suporta a versão MySQL **5.6, 5.7 e 8.0**.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Tempo de Execução de Integração fornece um controlador MySQL incorporado a partir da versão 3.7, pelo que não precisa de instalar manualmente nenhum controlador.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector MySQL.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado ao MySQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **MySql** | Yes |
| conexãoStragem | Especifique as informações necessárias para ligar à Base de Dados Azure para a ocorrência do MySQL.<br/> Também pode colocar a palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no artigo do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

Uma cadeia de ligação típica é `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Mais propriedades que pode definir por seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| SSLMode | Esta opção especifica se o controlador utiliza encriptação e verificação TLS ao ligar-se ao MySQL. Por exemplo,  `SSLMode=<0/1/2/3/4>` .| DESATIVADO (0) / PREFERIDO (1) **(Predefinido)** / OBRIGATÓRIO (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | No |
| SSLCert | O percurso e o nome completos de um ficheiro .pem contendo o certificado SSL utilizado para comprovar a identidade do cliente. <br/> Para especificar uma chave privada para encriptar este certificado antes de enviá-lo para o servidor, utilize a `SSLKey` propriedade.| | Sim, se utilizar a verificação SSL bidirecionais. |
| SSLKey | O percurso e o nome completos de um ficheiro que contém a chave privada utilizada para encriptar o certificado do lado do cliente durante a verificação SSL bidirecional.|  | Sim, se utilizar a verificação SSL bidirecionais. |
| UseSystemTrustStore | Esta opção especifica se deve utilizar um certificado de CA da loja de fidedignidade do sistema ou de um ficheiro PEM especificado. Por exemplo, `UseSystemTrustStore=<0/1>;`| Ativado (1) / Desativado (0) **(Predefinição)** | No |

**Exemplo:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar senha em Azure Key Vault**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Se você estava usando o serviço ligado MySQL com a seguinte carga útil, ele ainda é suportado como está, enquanto você é sugerido para usar o novo para a frente.

**Carga útil anterior:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados MySQL.

Para copiar dados do MySQL, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **MySqlTable** | Yes |
| tableName | O nome da tabela na base de dados MySQL. | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se estiver a utilizar `RelationalTable` conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas pela fonte mySQL.

### <a name="mysql-as-source"></a>MySQL como fonte

Para copiar dados do MySQL, as seguintes propriedades são suportadas na secção **fonte de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **MySqlSource** | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se estava a usar `RelationalSource` uma fonte dactilografada, ainda é suportada como está, enquanto é sugerido que utilize a nova que vai para a frente.

## <a name="data-type-mapping-for-mysql"></a>Mapeamento do tipo de dados para o MySQL

Ao copiar dados do MySQL, os seguintes mapeamentos são usados desde os tipos de dados do MySQL até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados MySQL | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
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
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
