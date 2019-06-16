---
title: Copiar dados de DB2 com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados de DB2 para arquivos de dados de sink suportado através de uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: 4bf4c5c8339c8c56d91737fa1ff62f55b9c38696
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60786378"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiar dados de DB2 através do Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-db2-connector.md)
> * [Versão atual](connector-db2.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados DB2. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de base de dados DB2 para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de DB2 suporta as seguintes plataformas de IBM DB2 e versões distribuído relacional da base de dados arquitetura (DRDA) SQL acesso Manager (SQLAM) versão 9, 10 e 11:

* IBM DB2 para 11.1 de z/OS
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

> [!TIP]
> Se receber uma mensagem de erro que indica "não foi encontrado o pacote correspondente a uma solicitação de execução de instrução de SQL. SQLSTATE = 51002 SQLCODE =-805 ", o motivo é um pacote necessário não foi criado para um usuário normal nesse sistema operacional. Siga estas instruções, de acordo com o tipo de servidor do DB2:
> - DB2 para i (AS400): permitir que o utilizador de poder criar a coleção para o utilizador de início de sessão antes de utilizar a atividade de cópia. Comando: `create collection <username>`
> - DB2 para z/OS ou LUW: utilizar uma conta de privilégio alto - utilizador avançado ou administrador com autoridades de pacote e o ENLACE, BINDADD, GRANT executar para permissões públicas - para executar a atividade de cópia de uma vez, em seguida, o pacote necessário é criado automaticamente durante a cópia. Em seguida, pode voltar a mudar para usuário normal para suas execuções de cópia subsequentes.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar dados de cópia de uma base de dados DB2, que não está acessível ao público, terá de configurar um Runtime de integração autoalojado. Para saber mais sobre os runtimes de integração autoalojado, veja [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo. O Runtime de integração fornece um driver de DB2 incorporado, portanto não precisa de instalar manualmente a qualquer driver quando se copiam dados de DB2.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de DB2.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço de DB2 ligado:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **Db2** | Sim |
| server |Nome do servidor DB2. Pode especificar o número da porta após o nome de servidor delimitado por vírgula, por exemplo, `server:port`. |Sim |
| database |Nome da base de dados DB2. |Sim |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados DB2.<br/>O valor permitido é: **Básico**. |Sim |
| username |Especifique o nome de utilizador para ligar à base de dados DB2. |Sim |
| password |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados DB2.

Para copiar dados de DB2, defina a propriedade de tipo de conjunto de dados para **RelationalTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **RelationalTable** | Sim |
| tableName | Nome da tabela na base de dados DB2. | Não (se for especificada "consulta" na origem de atividade) |

**Exemplo**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de DB2.

### <a name="db2-as-source"></a>DB2 como origem

Para copiar dados de DB2, definir o tipo de origem na atividade de cópia para **RelationalSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **RelationalSource** | Sim |
| query | Utilize a consulta SQL personalizada para ler os dados. Por exemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Não (se for especificado "tableName" no conjunto de dados) |

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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Tipo de dados de mapeamento para DB2

Ao copiar dados de DB2, os seguintes mapeamentos são utilizados entre tipos de dados de DB2 para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de base de dados DB2 | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| Date |Datetime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| Integer |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| Numeric |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| Timestamp |DateTime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |


## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
