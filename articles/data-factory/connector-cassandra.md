---
title: Copiar dados de Cassandra usando azure data factory
description: Saiba como copiar dados da Cassandra para lojas de dados de sink suportadas utilizando uma atividade de cópia num oleoduto Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 9339fff820c0a0d915258ce3a0bc5371242ad50d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892834"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiar dados de Cassandra usando azure data factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Versão atual](connector-cassandra.md)

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados da Cassandra. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Cassandra é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados da Cassandra para qualquer loja de dados suportada. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Cassandra suporta:

- Cassandra **versões 2.x e 3.x**.
- Copiar dados utilizando autenticação **Básica** ou **Anónima.**

>[!NOTE]
>Para a atividade em execução no Tempo de Integração Auto-hospedado, Cassandra 3.x é suportada desde a versão IR 3.7 ou superior.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Tempo de Funcionamento de Integração fornece um controlador Cassandra incorporado, por isso não precisa de instalar manualmente qualquer controlador ao copiar dados de/para Cassandra.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas do conector Cassandra.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado a Cassandra:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **Cassandra** |Sim |
| anfitrião |Um ou mais endereços IP ou nomes de anfitriões dos servidores Cassandra.<br/>Especifique uma lista separada da vírmula de endereços IP ou nomes de anfitriões para ligar a todos os servidores simultaneamente. |Sim |
| porta |A porta TCP que o servidor Cassandra usa para ouvir as ligações do cliente. |Não (padrão é 9042) |
| authenticationType | Tipo de autenticação usado para ligar à base de dados de Cassandra.<br/>Os valores permitidos são: **Básico,** e **Anónimo.** |Sim |
| o nome de utilizador |Especifique o nome do utilizador para a conta de utilizador. |Sim, se a autenticaçãoType estiver definida para Basic. |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). |Sim, se a autenticaçãoType estiver definida para Basic. |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não especificado, utiliza o tempo de funcionar de integração azure padrão. |Não |

>[!NOTE]
>Atualmente, a ligação à Cassandra utilizando o SSL não é suportada.

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados cassandra.

Para copiar dados de Cassandra, detete a propriedade tipo do conjunto de dados para **CassandraTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **CassandraTable** | Sim |
| espaço chave |Nome do espaço-chave ou esquema na base de dados de Cassandra. |Não (se for especificada "consulta" para "CassandraSource" |
| tableName |Nome da mesa na base de dados de Cassandra. |Não (se for especificada "consulta" para "CassandraSource" |

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


Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por Cassandra fonte.

### <a name="cassandra-as-source"></a>Cassandra como fonte

Para copiar dados da Cassandra, delineie o tipo de origem na atividade de cópia para **CassandraSource**. As seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **CassandraSource** | Sim |
| consulta |Use a consulta personalizada para ler dados. Consulta SQL-92 ou consulta CQL. Ver [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique o **nome do espaço-chave.nome** da tabela para representar a tabela que pretende consultar. |Não (se for especificado "tableName" e "keyspace" no conjunto de dados). |
| consistênciaN |O nível de consistência especifica quantas réplicas devem responder a um pedido de leitura antes de devolver os dados à aplicação do cliente. Cassandra verifica o número especificado de réplicas para obter dados para satisfazer o pedido de leitura. Consulte [a configuração](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) da consistência dos dados para obter mais detalhes.<br/><br/>Os valores permitidos são: **UM,** **DOIS,** **TRÊS**, **QUORUM,** **ALL,** **LOCAL_QUORUM,** **EACH_QUORUM**e **LOCAL_ONE.** |Não (padrão `ONE`é) |

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

Ao copiar dados da Cassandra, os seguintes mapeamentos são usados desde tipos de dados cassandra para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados cassandra | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| ASCII |Cadeia |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |Booleano |
| DECIMAL |Decimal |
| DUPLO |Double |
| BOIA |Único |
| INET |Cadeia |
| INT |Int32 |
| TEXT |Cadeia |
| CARIMBO TEMPORAL |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| RIO VARCHAR |Cadeia |
| VARINT |Decimal |

> [!NOTE]
> Para tipos de recolha (mapa, conjunto, lista, etc.), consulte o Trabalho com tipos de recolha Cassandra utilizando a secção de [tabela virtual.](#work-with-collections-using-virtual-table)
>
> Os tipos definidos pelo utilizador não são suportados.
>
> O comprimento da Coluna Binária e dos comprimentos da Coluna de Cordas não pode ser superior a 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções usando mesa virtual

A Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados da sua base de dados Cassandra. Para tipos de recolha, incluindo mapa, conjunto e lista, o controlador renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver colunas de recolha, o condutor gera as seguintes tabelas virtuais:

* Uma **tabela base,** que contém os mesmos dados que a tabela real, exceto as colunas de recolha. A tabela base usa o mesmo nome que a verdadeira tabela que representa.
* Uma **tabela virtual** para cada coluna de recolha, que expande os dados aninhados. As tabelas virtuais que representam coleções são nomeadas usando o nome da mesa real, um separador "*vt*" e o nome da coluna.

As tabelas virtuais referem-se aos dados na tabela real, permitindo ao condutor aceder aos dados desnormalizados. Consulte a secção Exemplo para mais detalhes. Você pode aceder ao conteúdo das coleções Cassandra consultando e juntando as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, o seguinte "ExampleTable" é uma tabela de bases de dados cassandra que contém uma coluna de chave primária intrometo chamada "pk_int", uma coluna de texto chamada valor, uma coluna de lista, uma coluna de mapas e uma coluna de conjunto (chamada "StringSet").

| pk_int | Valor | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Valor da amostra 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Valor da amostra 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O condutor geraria várias tabelas virtuais para representar esta única tabela. As colunas de teclas estrangeiras nas tabelas virtuais referem as colunas-chave primárias na tabela real, e indicam a que verdadeira linha de mesa a linha de mesa virtual corresponde.

A primeira tabela virtual é a tabela base denominada "ExampleTable" é mostrada na tabela seguinte: 

| pk_int | Valor |
| --- | --- |
| 1 |"Valor da amostra 1" |
| 3 |"Valor da amostra 3" |

A tabela base contém os mesmos dados que a tabela de bases de dados original, com exceção das coleções, que são omitidas desta tabela e expandidas noutras tabelas virtuais.

As tabelas seguintes mostram as tabelas virtuais que renormalizam os dados das colunas List, Map e StringSet. As colunas com nomes que terminam com "_index" ou "_key" indicam a posição dos dados dentro da lista ou mapa original. As colunas com nomes que terminam com "_value" contêm os dados expandidos da recolha.

**Quadro "ExampleTable_vt_List":**

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
