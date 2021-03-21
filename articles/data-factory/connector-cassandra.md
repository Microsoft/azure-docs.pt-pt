---
title: Copiar dados de Cassandra usando Azure Data Factory
description: Saiba como copiar dados da Cassandra para lojas de dados de sumidouros suportados utilizando uma atividade de cópia num oleoduto Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383709"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiar dados de Cassandra usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Versão atual](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados cassandra. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector Cassandra é apoiado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados cassandra para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Cassandra suporta:

- Cassandra **versões 2.x e 3.x**.
- Copiar dados utilizando a autenticação **básica** ou **anónima.**

>[!NOTE]
>Para a atividade em execução no Tempo de Execução de Integração Auto-hospedado, Cassandra 3.x é suportada desde a versão 3.7 e superior.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

O Tempo de Execução de Integração fornece um controlador Cassandra incorporado, pelo que não precisa de instalar manualmente nenhum controlador ao copiar dados de/para Cassandra.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector Cassandra.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à Cassandra:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |A propriedade tipo deve ser definida para: **Cassandra** |Yes |
| anfitrião |Um ou mais endereços IP ou nomes de anfitrião de servidores Cassandra.<br/>Especifique uma lista separada de vírgulas de endereços IP ou nomes de anfitrião para ligar a todos os servidores simultaneamente. |Yes |
| porta |A porta TCP que o servidor Cassandra usa para ouvir as ligações do cliente. |Não (padrão é 9042) |
| authenticationType | Tipo de autenticação usada para ligar à base de dados cassandra.<br/>Os valores permitidos são: **Básico,** e **Anónimo.** |Yes |
| nome de utilizador |Especifique o nome de utilizador para a conta do utilizador. |Sim, se a autenticaçãoType estiver definida como Basic. |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador. Marque este campo como um SecureString para armazená-lo de forma segura na Data Factory, ou [fazer referência a um segredo armazenado no Cofre da Chave Azure](store-credentials-in-key-vault.md). |Sim, se a autenticaçãoType estiver definida como Basic. |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Saiba mais na secção [Pré-Requisitos.](#prerequisites) Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. |No |

>[!NOTE]
>Atualmente, a ligação com a Cassandra usando TLS não é suportada.

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas por Conjunto de dados Cassandra.

Para copiar dados da Cassandra, defina a propriedade tipo do conjunto de dados para **a CassandraTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo do conjunto de dados deve ser definida para: **CassandraTable** | Yes |
| keyspace |Nome do espaço-chave ou esquema na base de dados de Cassandra. |Não (se for especificada "consulta" para "CassandraSource") |
| tableName |O nome da mesa na base de dados da Cassandra. |Não (se for especificada "consulta" para "CassandraSource") |

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


Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades apoiadas por fonte Cassandra.

### <a name="cassandra-as-source"></a>Cassandra como fonte

Para copiar dados da Cassandra, desagrafe o tipo de origem na atividade de cópia à **CassandraSource**. As seguintes propriedades são suportadas na secção fonte de **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo da fonte de atividade de cópia deve ser definida para: **CassandraSource** | Yes |
| consulta |Utilize a consulta personalizada para ler dados. Consulta SQL-92 ou consulta CQL. Consulte [a referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique **o nome do keyspace.table** para representar a tabela que pretende consultar. |Não (se forem especificados "tableName" e "keyspace" no conjunto de dados). |
| consistênciaLevel |O nível de consistência especifica quantas réplicas devem responder a um pedido de leitura antes de devolver os dados à aplicação do cliente. Cassandra verifica o número especificado de réplicas de dados para satisfazer o pedido de leitura. Consulte [a consistência dos dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter informações mais pormenores.<br/><br/>Os valores permitidos são: **UM,** **DOIS,** **TRÊS,** **QUORUM**, **ALL**, **LOCAL_QUORUM,** **EACH_QUORUM**, e **LOCAL_ONE**. |Não (o padrão `ONE` é) |

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

## <a name="data-type-mapping-for-cassandra"></a>Mapeamento do tipo de dados para Cassandra

Ao copiar dados da Cassandra, os seguintes mapeamentos são usados desde os tipos de dados de Cassandra até aos tipos de dados provisórios da Azure Data Factory. Consulte [os mapeamentos de schema e tipo de dados](copy-activity-schema-and-type-mapping.md) para saber como a atividade da cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados cassandra | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEANA |Booleano |
| DECIMAL |Decimal |
| DUPLO |Double (Duplo) |
| FLUTUAR |Único |
| INET |String |
| INT |Int32 |
| TEXT |String |
| CARIMBO DE DATA/HORA |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| RIO VARCHAR |String |
| RIO VARINT |Decimal |

> [!NOTE]
> Para os tipos de recolha (mapa, conjunto, lista, etc.), consulte os tipos de recolha de Trabalho com Cassandra utilizando a secção [de tabela virtual.](#work-with-collections-using-virtual-table)
>
> Os tipos definidos pelo utilizador não são suportados.
>
> O comprimento dos comprimentos da Coluna Binária e da Coluna de Cordas não pode ser superior a 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções usando mesa virtual

A Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados da sua base de dados Cassandra. Para tipos de recolha, incluindo mapa, conjunto e lista, o condutor renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver quaisquer colunas de recolha, o condutor gera as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados que a tabela real, com exceção das colunas de recolha. A tabela base usa o mesmo nome que a mesa real que representa.
* Uma **tabela virtual** para cada coluna de recolha, que expande os dados aninhados. As tabelas virtuais que representam coleções são nomeadas usando o nome da mesa real, um separador "*vt*" e o nome da coluna.

As tabelas virtuais referem-se aos dados na tabela real, permitindo ao condutor aceder aos dados denormalizados. Consulte a secção Exemplo para mais detalhes. Você pode aceder ao conteúdo das coleções Cassandra consultando e juntando as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, o seguinte "ExemploTable" é uma tabela de base de dados Cassandra que contém uma coluna-chave primária chamada "pk_int", uma coluna de texto denominada valor, uma coluna de lista, uma coluna de mapas e uma coluna de conjunto (chamada "StringSet").

| pk_int | Valor | Lista | Mapa | Conjunto de Cordas |
| --- | --- | --- | --- | --- |
| 1 |"Valor da amostra 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Valor da amostra 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O condutor geraria várias tabelas virtuais para representar esta única tabela. As colunas-chave estrangeiras nas tabelas virtuais referem as colunas-chave primárias na tabela real, e indicam a que linha de mesa real a linha de tabela virtual corresponde.

A primeira tabela virtual é a tabela base denominada "ExemploTable" é mostrada na tabela seguinte: 

| pk_int | Valor |
| --- | --- |
| 1 |"Valor da amostra 1" |
| 3 |"Valor da amostra 3" |

A tabela base contém os mesmos dados que a tabela de bases de dados original, com exceção das coleções, que são omitidas desta tabela e expandidas noutras tabelas virtuais.

As tabelas seguintes mostram as tabelas virtuais que renormalizam os dados das colunas List, Map e StringSet. As colunas com nomes que terminam com "_index" ou "_key" indicam a posição dos dados dentro da lista ou mapa original. As colunas com nomes que terminam com "_value" contêm os dados expandidos da recolha.

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

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
