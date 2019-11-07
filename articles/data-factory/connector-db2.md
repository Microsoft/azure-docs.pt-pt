---
title: Copiar dados do DB2 usando o Azure Data Factory
description: Saiba como copiar dados do DB2 para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: dad28da0b481467633bebf664fea2be39a50200b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681044"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copiar dados do DB2 usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-db2-connector.md)
> * [Versão atual](connector-db2.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um banco de dado DB2. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector de banco de dados DB2 tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados DB2 para qualquer armazenamento de dado de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector do DB2 dá suporte às seguintes plataformas e versões do IBM DB2 com o DRDA (Distributed Relacionative Database Architecture) versão 9, 10 e 11:

* IBM DB2 para z/OS 12,1
* IBM DB2 para z/OS 11,1
* IBM DB2 para z/OS 10,1
* IBM DB2 para i 7,3
* IBM DB2 para i 7,2
* IBM DB2 para i 7,1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10,5
* IBM DB2 para LUW 10,1

> [!TIP]
> Se você receber uma mensagem de erro afirmando "o pacote correspondente a uma solicitação de execução de instrução SQL não foi encontrado. SQLSTATE = 51002 SQLCODE =-805 ", o motivo é que um pacote necessário não é criado para o usuário normal nesse sistema operacional. Siga estas instruções de acordo com o tipo de servidor DB2:
> - DB2 para i (AS400): Deixe o usuário avançado criar a coleção para o usuário de logon antes de usar a atividade de cópia. Comando: `create collection <username>`
> - DB2 para z/OS ou LUW: usar uma conta de alto privilégio – usuário avançado ou administrador com autoridades de pacote e BIND, BINDADD, conceder permissões executar para público-para executar a atividade de cópia uma vez, o pacote necessário é criado automaticamente durante a cópia. Posteriormente, você pode alternar de volta para o usuário normal para as execuções de cópia subsequentes.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Integration Runtime fornece um driver do DB2 interno, portanto, você não precisa instalar manualmente nenhum driver ao copiar dados do DB2.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do DB2.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do DB2:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **DB2** | Sim |
| servidor |Nome do servidor DB2. Você pode especificar o número da porta seguindo o nome do servidor delimitado por dois-pontos, por exemplo, `server:port`. |Sim |
| base de dados |Nome do banco de dados DB2. |Sim |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados DB2.<br/>O valor permitido é: **básico**. |Sim |
| o nome de utilizador |Especifique o nome de usuário para se conectar ao banco de dados DB2. |Sim |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de banco de e do DB2.

Para copiar dados do DB2, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **Db2Table** | Sim |
| schema | Nome do esquema. |Não (se for especificada "query" na origem de atividade)  |
| tabela | Nome da tabela. |Não (se for especificada "query" na origem de atividade)  |
| tableName | Nome da tabela com esquema. Essa propriedade tem suporte para compatibilidade com versões anteriores. Use `schema` e `table` para uma nova carga de trabalho. | Não (se for especificada "query" na origem de atividade) |

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

Se você estivesse usando `RelationalTable` dataset tipado, ele ainda tem suporte como está, enquanto você é sugerido para usar o novo no futuro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do DB2.

### <a name="db2-as-source"></a>DB2 como fonte

Para copiar dados do DB2, há suporte para as seguintes propriedades na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **Db2Source** | Sim |
| consulta | Use a consulta SQL personalizada para ler os dados. Por exemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Não (se "TableName" no DataSet for especificado) |

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

Se você estivesse usando `RelationalSource` fonte tipada, ainda há suporte no estado em que se encontra, enquanto você é sugerido para usar o novo no futuro.

## <a name="data-type-mapping-for-db2"></a>Mapeamento de tipo de dados para DB2

Ao copiar dados do DB2, os seguintes mapeamentos são usados de tipos de dados do DB2 para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

| Tipo de banco de dados DB2 | Tipo de dados provisório do data Factory |
|:--- |:--- |
| BigInt |Int64 |
| binário |Byte [] |
| Blobs |Byte [] |
| º |String |
| CLOB |String |
| Date |Horário |
| DB2DynArray |String |
| DbClob |String |
| Vírgula |Vírgula |
| DecimalFloat |Vírgula |
| Clique |Clique |
| Barra |Clique |
| Gráfico |String |
| Número inteiro |Int32 |
| LongVarBinary |Byte [] |
| LongVarChar |String |
| LongVarGraphic |String |
| numeric |Vírgula |
| Real |Único |
| SmallInt |Int16 |
| Hora |Período |
| Carimbo de data/hora |DateTime |
| VarBinary |Byte [] |
| VarChar |String |
| VarGraphic |String |
| XML |Byte [] |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
