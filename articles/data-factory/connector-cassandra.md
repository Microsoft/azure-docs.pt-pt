---
title: Copiar dados do Cassandra usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do Cassandra para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 1531f2530af9c2fbc90d1bf25f04962fb4148a8d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090475"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiar dados do Cassandra usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Versão atual](connector-cassandra.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de Cassandra. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector do Cassandra tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Cassandra Database para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector do Cassandra dá suporte a:

- Cassandra **versões 2. x e 3. x**.
- Copiar dados usando a autenticação **básica** ou **anônima** .

>[!NOTE]
>Para atividades em execução no Integration Runtime autohospedado, o Cassandra 3. x tem suporte desde a versão do IR 3,7 e superior.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Integration Runtime fornece um driver Cassandra interno, portanto, você não precisa instalar manualmente nenhum driver ao copiar dados de/para Cassandra.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do Cassandra.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado do Cassandra:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type |A propriedade Type deve ser definida como: **Cassandra** |Sim |
| host |Um ou mais endereços IP ou nomes de host de servidores Cassandra.<br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para se conectar a todos os servidores simultaneamente. |Sim |
| port |A porta TCP que o servidor Cassandra usa para escutar conexões de cliente. |Não (o padrão é 9042) |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados Cassandra.<br/>Valores permitidos são: **Básico**e **anônimo**. |Sim |
| username |Especifique o nome de usuário para a conta de usuário. |Sim, se authenticationType estiver definido como básico. |
| password |Especifique a senha para a conta de usuário. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim, se authenticationType estiver definido como básico. |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Atualmente, a conexão com o Cassandra usando SSL não tem suporte.

**Example:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Cassandra.

Para copiar dados do Cassandra, defina a propriedade Type do conjunto de dado como **CassandraTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **CassandraTable** | Sim |
| keyspace |Nome do keyspace ou esquema no banco de dados Cassandra. |Não (se "Query" para "CassandraSource" for especificado) |
| tableName |Nome da tabela no banco de dados Cassandra. |Não (se "Query" para "CassandraSource" for especificado) |

**Example:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy


Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem do Cassandra.

### <a name="cassandra-as-source"></a>Cassandra como fonte

Para copiar dados do Cassandra, defina o tipo de origem na atividade de cópia como **CassandraSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type da fonte da atividade de cópia deve ser definida como: **CassandraSource** | Sim |
| query |Use a consulta personalizada para ler os dados. Consulta SQL-92 ou consulta CQL. Consulte [referência de CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique o **nome do keyspace. nome da tabela** para representar a tabela que você deseja consultar. |Não (se "TableName" e "keyspace" no DataSet forem especificados). |
| consistencyLevel |O nível de consistência especifica quantas réplicas devem responder a uma solicitação de leitura antes de retornar dados para o aplicativo cliente. Cassandra verifica o número especificado de réplicas em busca de dados para atender à solicitação de leitura. Consulte [Configurando a consistência de dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes.<br/><br/>Valores permitidos são: **Um**, **dois**, **três**, **Quorum**, **All**, **LOCAL_QUORUM**, **EACH_QUORUM**e **LOCAL_ONE**. |Não (o padrão `ONE`é) |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Mapeamento de tipo de dados para Cassandra

Ao copiar dados do Cassandra, os seguintes mapeamentos são usados de tipos de dados do Cassandra para Azure Data Factory tipos de dados provisórios. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados Cassandra | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| ASCII |Cadeia |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |Booleano |
| DECIMAL |Decimal |
| DOUBLE |Double |
| FLOAT |Single |
| INET |Cadeia |
| INT |Int32 |
| TEXT |Cadeia |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| PERSONALIZADO |Guid |
| VARCHAR |Cadeia |
| VARINT |Decimal |

> [!NOTE]
> Para tipos de coleção (mapa, conjunto, lista, etc.), consulte [trabalhar com tipos de coleção Cassandra usando a seção da tabela virtual](#work-with-collections-using-virtual-table) .
>
> Não há suporte para tipos definidos pelo usuário.
>
> O comprimento de coluna binária e comprimentos de coluna de cadeia de caracteres não pode ser maior que 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções usando a tabela virtual

O Azure Data Factory usa um driver ODBC interno para se conectar e copiar dados do seu Cassandra Database. Para tipos de coleção, incluindo MAP, set e List, o driver renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver qualquer coluna de coleção, o driver gerará as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados que a tabela real, exceto para as colunas de coleção. A tabela base usa o mesmo nome que a tabela real que ela representa.
* Uma **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam as coleções são nomeadas usando o nome da tabela real, um separador "*VT*" e o nome da coluna.

As tabelas virtuais referem-se aos dados na tabela real, permitindo que o driver acesse os dados desnormalizados. Consulte a seção de exemplo para obter detalhes. Você pode acessar o conteúdo das coleções Cassandra consultando e unindo as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, o seguinte "Exemplotable" é uma tabela de banco de dados Cassandra que contém uma coluna de chave primária de inteiro denominada "pk_int", uma coluna de texto denominada Value, uma coluna de lista, uma coluna de mapa e uma coluna Set (denominada "stringSet").

| pk_int | Value | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valor de exemplo 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O driver geraria várias tabelas virtuais para representar essa única tabela. As colunas de chave estrangeira nas tabelas virtuais fazem referência às colunas de chave primária na tabela real e indicam a qual linha da tabela real a linha da tabela virtual corresponde.

A primeira tabela virtual é a tabela base chamada "ExampleTable", mostrada na tabela a seguir: 

| pk_int | Value |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"valor de exemplo 3" |

A tabela base contém os mesmos dados que a tabela de banco de dado original, exceto as coleções, que são omitidas desta tabela e expandidas em outras tabelas virtuais.

As tabelas a seguir mostram as tabelas virtuais que renormalizam os dados da lista, do mapa e das colunas de cadeia de caracteres. As colunas com nomes que terminam com "_index" ou "_key" indicam a posição dos dados na lista ou no mapa original. As colunas com nomes que terminam com "_value" contêm os dados expandidos da coleção.

**Table "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Table "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Table "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
