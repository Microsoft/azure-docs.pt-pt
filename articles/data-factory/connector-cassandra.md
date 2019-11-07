---
title: Copiar dados do Cassandra usando o Azure Data Factory
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
ms.openlocfilehash: 9068874704233b3bbb5f38345648bc3455433768
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681099"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiar dados do Cassandra usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Versão atual](connector-cassandra.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de Cassandra. Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do Cassandra tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do Cassandra Database para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

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

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Cassandra:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade Type deve ser definida como: **Cassandra** |Sim |
| hospedeira |Um ou mais endereços IP ou nomes de host de servidores Cassandra.<br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para se conectar a todos os servidores simultaneamente. |Sim |
| porta |A porta TCP que o servidor Cassandra usa para escutar conexões de cliente. |Não (o padrão é 9042) |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados Cassandra.<br/>Os valores permitidos são: **básico**e **anônimo**. |Sim |
| o nome de utilizador |Especifique o nome de usuário para a conta de usuário. |Sim, se authenticationType estiver definido como básico. |
| palavra-passe |Especifique a senha para a conta de usuário. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). |Sim, se authenticationType estiver definido como básico. |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção de [pré-requisitos](#prerequisites) . Se não for especificado, ele usará o Azure Integration Runtime padrão. |Não |

>[!NOTE]
>Atualmente, a conexão com o Cassandra usando SSL não tem suporte.

**Exemplo:**

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

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de Cassandra.

Para copiar dados do Cassandra, defina a propriedade Type do conjunto de dado como **CassandraTable**. As propriedades a seguir têm suporte:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type do conjunto de conjuntos deve ser definida como: **CassandraTable** | Sim |
| keyspace |Nome do keyspace ou esquema no banco de dados Cassandra. |Não (se "Query" para "CassandraSource" for especificado) |
| tableName |Nome da tabela no banco de dados Cassandra. |Não (se "Query" para "CassandraSource" for especificado) |

**Exemplo:**

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


Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do Cassandra.

### <a name="cassandra-as-source"></a>Cassandra como fonte

Para copiar dados do Cassandra, defina o tipo de origem na atividade de cópia como **CassandraSource**. As propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **CassandraSource** | Sim |
| consulta |Use a consulta personalizada para ler os dados. Consulta SQL-92 ou consulta CQL. Consulte [referência de CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique o **nome do keyspace. nome da tabela** para representar a tabela que você deseja consultar. |Não (se "TableName" e "keyspace" no DataSet forem especificados). |
| consistencyLevel |O nível de consistência especifica quantas réplicas devem responder a uma solicitação de leitura antes de retornar dados para o aplicativo cliente. Cassandra verifica o número especificado de réplicas em busca de dados para atender à solicitação de leitura. Consulte [Configurando a consistência de dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes.<br/><br/>Os valores permitidos são: **One**, **Two**, **três**, **Quorum**, **All**, **LOCAL_QUORUM**, **EACH_QUORUM**e **LOCAL_ONE**. |Não (o padrão é `ONE`) |

**Exemplo:**

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

Ao copiar dados do Cassandra, os seguintes mapeamentos são usados de tipos de dados do Cassandra para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

| Tipo de dados Cassandra | Tipo de dados provisório do data Factory |
|:--- |:--- |
| LOCALIZADOS |String |
| BIGINT |Int64 |
| BLOB |Byte [] |
| BOOLEAN |Booleano |
| VÍRGULA |Vírgula |
| Clique |Clique |
| BARRA |Único |
| INET |String |
| INT |Int32 |
| TEXTO |String |
| ESTAMPA |DateTime |
| Timeuuid |GUID |
| PERSONALIZADO |GUID |
| VARCHAR |String |
| VARINT |Vírgula |

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

| pk_int | Valor | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valor de exemplo 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O driver geraria várias tabelas virtuais para representar essa única tabela. As colunas de chave estrangeira nas tabelas virtuais fazem referência às colunas de chave primária na tabela real e indicam a qual linha da tabela real a linha da tabela virtual corresponde.

A primeira tabela virtual é a tabela base chamada "ExampleTable", mostrada na tabela a seguir: 

| pk_int | Valor |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"valor de exemplo 3" |

A tabela base contém os mesmos dados que a tabela de banco de dado original, exceto as coleções, que são omitidas desta tabela e expandidas em outras tabelas virtuais.

As tabelas a seguir mostram as tabelas virtuais que renormalizam os dados da lista, do mapa e das colunas de cadeia de caracteres. As colunas com nomes que terminam com "_index" ou "_key" indicam a posição dos dados na lista ou no mapa original. As colunas com nomes que terminam com "_value" contêm os dados expandidos da coleção.

**Tabela "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabela "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabela "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
