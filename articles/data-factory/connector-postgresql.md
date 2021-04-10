---
title: Copiar dados do PostgreSQL utilizando a Azure Data Factory
description: Saiba como copiar dados da PostgreSQL para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num pipeline da Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: jingwang
ms.openlocfilehash: c0d8783c4b07c8c87e57ccd9a5d65983825347c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384100"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Copiar dados da PostgreSQL utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Versão atual](connector-postgresql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados PostgreSQL. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector PostgreSQL é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados PostgreSQL para qualquer loja de dados de sumidouro suportado. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector PostgreSQL suporta a **versão 7.4 e superior** do PostgreSQL .

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Tempo de Execução de Integração fornece um controlador PostgreSQL incorporado a partir da versão 3.7, pelo que não precisa de instalar manualmente nenhum controlador.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector PostgreSQL.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado postgreSQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **PostgreSql** | Yes |
| conexãoStragem | Um fio de ligação ODBC para ligar à Base de Dados Azure para PostgreSQL. <br/>Também pode colocar a palavra-passe no Cofre da Chave Azure e retirar a `password` configuração da cadeia de ligação. Consulte as seguintes amostras e [guarde as credenciais no artigo do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

Uma cadeia de ligação típica é `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Mais propriedades que pode definir por seu caso:

| Propriedade | Descrição | Opções | Necessário |
|:--- |:--- |:--- |:--- |
| Método de Encriptação (EM)| O método que o condutor utiliza para encriptar os dados enviados entre o controlador e o servidor de base de dados. Por exemplo,  `EncryptionMethod=<0/1/6>;`| 0 (Sem encriptação) **(Padrão)** / 1 (SSL) / 6 (RequestSSL) | No |
| ValidaçãoServerCertificato (VSC) | Determina se o controlador valida o certificado enviado pelo servidor de base de dados quando a encriptação SSL está ativada (Método de Encriptação=1). Por exemplo,  `ValidateServerCertificate=<0/1>;`| 0 (Desativado) **(Predefinição)** / 1 (Habilitado) | No |

**Exemplo:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
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
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;",
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

Se você estava usando o serviço ligado PostgreSQL com a seguinte carga útil, ele ainda é suportado como está, enquanto você é sugerido para usar o novo em frente.

**Carga útil anterior:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados PostgreSQL.

Para copiar dados da PostgreSQL, suportam-se as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **PostgreSqlTable** | Yes |
| esquema | O nome do esquema. |Não (se for especificada "consulta" na fonte de atividade)  |
| table | O nome da mesa. |Não (se for especificada "consulta" na fonte de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Uso `schema` e para nova carga de `table` trabalho. | Não (se for especificada "consulta" na fonte de atividade) |

**Exemplo**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "PostgreSqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se estava a usar `RelationalTable` conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que use o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL como fonte

Para copiar dados da PostgreSQL, as seguintes propriedades são suportadas na secção **de origem** da atividade da cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **PostgreSqlSource** | Yes |
| consulta | Utilize a consulta SQL personalizada para ler dados. Por exemplo: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Não (se for especificado "tableName" no conjunto de dados) |

> [!NOTE]
> Schema e nomes de mesa são sensíveis a casos. Enr junto-os `""` em (cotações duplas) na consulta.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "PostgreSqlSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se estava a usar `RelationalSource` uma fonte dactilografada, ainda é suportada como está, enquanto é sugerido que utilize a nova que vai para a frente.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
