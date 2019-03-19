---
title: Copiar dados do Cassandra com o Azure Data Factory | Documentos da Microsoft
description: Saiba como copiar dados do Cassandra para arquivos de dados de sink suportado com uma atividade de cópia num pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: 743dad6032547f8f535543413adff416efb56ac0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998391"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Copiar dados do Cassandra com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Versão atual](connector-cassandra.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados do Cassandra. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados do Cassandra para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, oferece suporte a este conector Cassandra:

- Cassandra **versões 2.x e 3.x**.
- Copiar dados utilizando **básica** ou **anónimo** autenticação.

>[!NOTE]
>Para a atividade em execução no Runtime de integração autoalojado, Cassandra 3.x é suportada desde o runtime de integração versão 3.7 e posteriores.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados de uma base de dados do Cassandra que não está acessível ao público, terá de configurar um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para saber mais detalhes. O Runtime de integração fornece um driver de Cassandra incorporado, portanto não precisa de instalar manualmente a qualquer driver quando se copiam dados de/para o Cassandra.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector do Cassandra.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Cassandra:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida como: **Cassandra** |Sim |
| anfitrião |Um ou mais endereços IP ou nomes de anfitrião dos servidores de Cassandra.<br/>Especifica uma lista separada por vírgulas de endereços IP ou nomes de anfitrião para ligar a todos os servidores em simultâneo. |Sim |
| porta |A porta TCP que o servidor Cassandra utiliza para escutar ligações de cliente. |Não (a predefinição é 9042) |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados Cassandra.<br/>Valores permitidos são: **Básica**, e **anônimo**. |Sim |
| o nome de utilizador |Especifique o nome de utilizador para a conta de utilizador. |Sim, se authenticationType está definido para básico. |
| palavra-passe |Especifique a palavra-passe da conta de utilizador. Marcar esse campo como uma SecureString armazena de forma segura na fábrica de dados, ou [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim, se authenticationType está definido para básico. |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração autoalojado ou Runtime de integração do Azure (se o seu armazenamento de dados está acessível ao público). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. |Não |

>[!NOTE]
>Atualmente a conexão com o Cassandra através de SSL não é suportada.

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados do Cassandra.

Para copiar dados do Cassandra, defina a propriedade de tipo de conjunto de dados para **CassandraTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados deve ser definida como: **CassandraTable** | Sim |
| keyspace |Nome do keyspace ou esquema na base de dados do Cassandra. |Não (se for especificada "consulta" para "CassandraSource") |
| tableName |Nome da tabela na base de dados do Cassandra. |Não (se for especificada "consulta" para "CassandraSource") |

**Exemplo:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy


Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de Cassandra.

### <a name="cassandra-as-source"></a>Cassandra como origem

Para copiar dados do Cassandra, defina o tipo de origem na atividade de cópia para **CassandraSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo de origem de atividade de cópia tem de ser definida: **CassandraSource** | Sim |
| consulta |Utilize a consulta personalizada para ler dados. Consulta de SQL-92 ou consulta CQL. Ver [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique **keyspace name.table nome** para representar a tabela que pretende consultar. |Não (se for "tableName" e "keyspace" no conjunto de dados são especificados). |
| consistencyLevel |O nível de consistência Especifica o número de réplicas devem responder a uma solicitação de leitura antes de retornar dados para a aplicação cliente. Cassandra verifica o número especificado de réplicas de dados satisfazer a solicitação de leitura. Ver [configurar a consistência dos dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes.<br/><br/>Valores permitidos são: **UM**, **duas**, **três**, **QUÓRUM**, **todos os**, **LOCAL_QUORUM**, **EACH_QUORUM**, e **LOCAL_ONE**. |Não (a predefinição é `ONE`) |

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

## <a name="data-type-mapping-for-cassandra"></a>Tipo de dados de mapeamento para o Cassandra

Ao copiar dados do Cassandra, os seguintes mapeamentos são utilizados entre tipos de dados do Cassandra para tipos de dados intermediárias do Azure Data Factory. Ver [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

| Tipo de dados do Cassandra | Tipo de dados intermediárias de fábrica de dados |
|:--- |:--- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| VALOR BOOLEANO |Booleano |
| DECIMAL |Decimal |
| VALOR DE DUPLO |Double |
| NÚMERO DE VÍRGULA FLUTUANTE |Single |
| INET |String |
| INT |Int32 |
| TEXTO |String |
| CARIMBO DE DATA/HORA |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |Decimal |

> [!NOTE]
> Para a coleção de tipos (mapa, conjunto, lista, etc.), consulte [trabalhar com tipos de coleção do Cassandra com a tabela virtual](#work-with-collections-using-virtual-table) secção.
>
> Não são suportados tipos definidos pelo utilizador.
>
> O comprimento de comprimentos de coluna binária e a coluna de cadeia de caracteres não pode ser superior a 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções por meio de tabela virtual

O Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados de sua base de dados do Cassandra. Para tipos de coleção, incluindo o mapa, o conjunto e lista, o driver renormalizes os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contém quaisquer colunas de coleção, o driver gera as tabelas virtuais seguintes:

* R **tabela base**, que contém os mesmos dados que a tabela real, exceto as colunas de coleção. A tabela base utiliza o mesmo nome, como a tabela real que representa.
* R **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam as coleções são nomeadas usando o nome da tabela real, um separador de "*vt*" e o nome da coluna.

Tabelas virtuais referem-se aos dados na tabela real, permitindo que o driver acessar os dados desnormalizados. Consulte a secção de exemplo para obter detalhes. Pode acessar o conteúdo das coleções de Cassandra consultando e associar as tabelas virtuais.

### <a name="example"></a>Exemplo

Por exemplo, "Seguinte ExampleTable" é uma tabela de base de dados do Cassandra que contém a coluna de chave primária de um número inteiro com o nome "pk_int", uma coluna de texto valor com nome, uma coluna de lista, uma coluna de mapa e uma coluna de conjunto (com o nome "StringSet").

| pk_int | Value | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valor de exemplo 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O controlador irá gerar várias tabelas virtuais para representar nesta única tabela. As colunas de chave estrangeiras nas tabelas virtuais referenciam as colunas de chave primárias na tabela real e indicam que linhas da tabela real a linha da tabela virtual corresponde à.

A primeira tabela virtual é a tabela de base com o nome "ExampleTable" é mostrada na tabela a seguir: 

| pk_int | Value |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"valor de exemplo 3" |

A tabela base contém os mesmos dados da tabela de base de dados original, exceto para as coleções, o que são omitidos desta tabela e expandido em outras tabelas virtuais.

As tabelas seguintes mostram as tabelas de virtual renormalizar dados das colunas de lista, o mapa e StringSet. As colunas com nomes que terminam com "_index" ou "c_have" indicam a posição dos dados dentro da lista original ou do mapa. As colunas com nomes que terminam com value"contêm os dados expandidos da coleção.

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

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).
