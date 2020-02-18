---
title: Copiar dados do DB2 utilizando a Azure Data Factory
description: Saiba como copiar dados do DB2 para lojas de dados de sink suportadas utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: jingwang
ms.openlocfilehash: 22ecac12e049e58e533cdde0078f4a25f6bb2aa6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423832"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiar dados do DB2 utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-db2-connector.md)
> * [Versão atual](connector-db2.md)

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados DB2. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de base de dados DB2 é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Pode copiar dados da base de dados DB2 para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector DB2 suporta as seguintes plataformas e versões IBM DB2 com arquitetura de base de dados relacional distribuída (DRDA) SQL Access Manager (SQLAM) versão 9, 10 e 11:

* IBM DB2 para z/OS 12.1
* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.3
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

>[!TIP]
>O conector DB2 é construído em cima do Microsoft OLE DB Provider para DB2. Para resolver problemas com os erros do conector DB2, consulte [os Códigos](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors)de Erro do Fornecedor de Dados .

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Tempo de Funcionamento de Integração fornece um controlador DB2 incorporado, pelo que não necessita de instalar manualmente qualquer controlador ao copiar dados do DB2.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector DB2.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado ao DB2:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Db2** | Sim |
| servidor |Nome do servidor DB2. Pode especificar o número de porta seguindo o nome do servidor delimitado por cólon, por exemplo, `server:port`. |Sim |
| database |Nome da base de dados DB2. |Sim |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados DB2.<br/>O valor permitido é: **Básico**. |Sim |
| o nome de utilizador |Especifique o nome do utilizador para se ligar à base de dados DB2. |Sim |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim |
| pacoteColeção | Especifique em que os pacotes necessários são criados automaticamente pela ADF ao consultar a base de dados. | Não |
| certificadoNome Comum | Quando utilizar a encriptação Secure Sockets Layer (SSL) ou Transport Layer Security (TLS), deve introduzir um valor para o nome comum do Certificado. | Não |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

> [!TIP]
> Se receber uma mensagem de erro que indique `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`, a razão é que um pacote necessário não é criado para o utilizador. Por padrão, a ADF tentará criar um pacote sob a sua designação como o utilizador que usou para ligar ao DB2. Especifique a propriedade de recolha de pacotes para indicar onde pretende que a ADF crie os pacotes necessários ao consultar a base de dados.

**Exemplo:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas por conjunto de dados DB2.

Para copiar dados do DB2, são suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **Db2Table** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| table | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para retrocompatibilidade. Use `schema` e `table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

**Exemplo**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se estiver a utilizar `RelationalTable` conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte DB2.

### <a name="db2-as-source"></a>DB2 como fonte

Para copiar dados do DB2, as seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **Db2Source** | Sim |
| consulta | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Não (se for especificado "tableName" no conjunto de dados) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se estava a usar `RelationalSource` fonte dactilografada, ainda é suportada como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="data-type-mapping-for-db2"></a>Mapeamento de tipo de dados para DB2

Ao copiar dados do DB2, os seguintes mapeamentos são utilizados desde tipos de dados DB2 para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de base de dados DB2 | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BigInt |Int64 |
| Binário |Byte[] |
| Blobs |Byte[] |
| char |Cadeia |
| Clob |Cadeia |
| Data |Datetime |
| DB2DynArray |Cadeia |
| DbClob |Cadeia |
| decimal |decimal |
| DecimalFloat |decimal |
| Valor de duplo |Valor de duplo |
| Float |Valor de duplo |
| Graphic |Cadeia |
| Número inteiro |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |Cadeia |
| Longvargraphic |Cadeia |
| Numeric |decimal |
| Real |Único |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Carimbo de data/hora |DateTime |
| VarBinary |Byte[] |
| VarChar |Cadeia |
| Vargraphic |Cadeia |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
