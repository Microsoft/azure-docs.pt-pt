---
title: Mover dados do Cassandra usando o Data Factory
description: Saiba mais sobre como mover dados de um banco de Cassandra local usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 05cee60fb1f4d43d1b4ce371aa9f22650b4782da
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931812"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Mover dados de um banco de Cassandra local usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-cassandra-connector.md)
> * [Versão 2 (versão atual)](../connector-cassandra.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do Cassandra na v2](../connector-cassandra.md).

Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um banco de Cassandra local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados Cassandra local para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Atualmente, o data Factory dá suporte apenas à movimentação de dados de um armazenamento de dados Cassandra para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um armazenamento de dados Cassandra.

## <a name="supported-versions"></a>Versões suportadas
O conector do Cassandra dá suporte às seguintes versões do Cassandra: 2. x e 3. x. Para atividades em execução no Integration Runtime autohospedado, o Cassandra 3. x tem suporte desde a versão do IR 3,7 e superior.

## <a name="prerequisites"></a>Pré-requisitos
Para que o serviço de Azure Data Factory seja capaz de se conectar ao seu banco de dados Cassandra local, você deve instalar um gateway de Gerenciamento de Dados no mesmo computador que hospeda o banco de dados ou em um computador separado para evitar a competição por recursos com o banco de dados. Gerenciamento de Dados gateway é um componente que conecta fontes de dados locais a serviços de nuvem de maneira segura e gerenciada. Consulte o artigo [Gerenciamento de dados gateway](data-factory-data-management-gateway.md) para obter detalhes sobre o gateway de gerenciamento de dados. Consulte o artigo [mover dados do local para a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo sobre como configurar o gateway como um pipeline de dados para mover dados.

Você deve usar o gateway para se conectar a um banco de dados Cassandra mesmo se o banco de dados estiver hospedado na nuvem, por exemplo, em uma VM IaaS do Azure. Y você pode ter o gateway na mesma VM que hospeda o banco de dados ou em uma VM separada, contanto que o gateway possa se conectar ao banco de dados.

Quando você instala o gateway, ele instala automaticamente um driver ODBC do Microsoft Cassandra usado para se conectar ao banco de dados Cassandra. Portanto, você não precisa instalar manualmente nenhum driver no computador do gateway ao copiar dados do banco de Cassandra.

> [!NOTE]
> Consulte [solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre como solucionar problemas relacionados à conexão/gateway.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que move dados de um armazenamento de dados Cassandra local usando diferentes ferramentas/APIs.

- A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.
- Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um armazenamento de dados Cassandra local, confira [a seção exemplo de JSON: copiar dados do Cassandra para o blob do Azure](#json-example-copy-data-from-cassandra-to-azure-blob) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para um armazenamento de dados Cassandra:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado Cassandra.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OnPremisesCassandra** |Sim |
| anfitrião |Um ou mais endereços IP ou nomes de host de servidores Cassandra.<br/><br/>Especifique uma lista separada por vírgulas de endereços IP ou nomes de host para se conectar a todos os servidores simultaneamente. |Sim |
| porta |A porta TCP que o servidor Cassandra usa para escutar conexões de cliente. |Não, valor padrão: 9042 |
| authenticationType |Básico ou anônimo |Sim |
| o nome de utilizador |Especifique o nome de usuário para a conta de usuário. |Sim, se authenticationType estiver definido como básico. |
| palavra-passe |Especifique a senha para a conta de usuário. |Sim, se authenticationType estiver definido como básico. |
| gatewayName |O nome do gateway que é usado para se conectar ao banco de dados Cassandra local. |Sim |
| encryptedCredential |Credencial criptografada pelo Gateway. |Não |

>[!NOTE]
>Atualmente, a conexão com o Cassandra usando SSL não tem suporte.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties para o conjunto de um do tipo **CassandraTable** tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| keyspace |Nome do keyspace ou esquema no banco de dados Cassandra. |Sim (se a **consulta** para **CassandraSource** não estiver definida). |
| tableName |Nome da tabela no banco de dados Cassandra. |Sim (se a **consulta** para **CassandraSource** não estiver definida). |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na seção typeproperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Quando a fonte é do tipo **CassandraSource**, as seguintes propriedades estão disponíveis na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Consulta SQL-92 ou consulta CQL. Consulte [referência de CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao usar a consulta SQL, especifique o **nome do keyspace. nome da tabela** para representar a tabela que você deseja consultar. |Não (se TableName e keyspace no conjunto de conjuntos forem definidos). |
| consistencyLevel |O nível de consistência especifica quantas réplicas devem responder a uma solicitação de leitura antes de retornar dados para o aplicativo cliente. Cassandra verifica o número especificado de réplicas em busca de dados para atender à solicitação de leitura. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Consulte [Configurando a consistência de dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. |Não. O valor padrão é um. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Exemplo de JSON: copiar dados do Cassandra para o blob do Azure
Este exemplo fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de um banco de Cassandra local para um armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

> [!IMPORTANT]
> Este exemplo fornece trechos de código JSON. Ele não inclui instruções passo a passo para criar o data factory. Confira o artigo [movendo dados entre os locais e a nuvem no local](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo.

O exemplo tem as seguintes entidades de data factory:

* Um serviço vinculado do tipo [OnPremisesCassandra](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [CassandraTable](#dataset-properties).
* Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [CassandraSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Serviço vinculado do Cassandra:**

Este exemplo usa o serviço vinculado **Cassandra** . Consulte a seção [serviço vinculado Cassandra](#linked-service-properties) para obter as propriedades com suporte neste serviço vinculado.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Serviço vinculado do armazenamento do Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Conjunto de dados de entrada Cassandra:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

A configuração de **external** como **true** informa ao serviço de data Factory que o dataset é externo ao data Factory e não é produzido por uma atividade no data Factory.

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia em um pipeline com origem Cassandra e coletor de blob:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **CassandraSource** e o tipo de **coletor** está definido como **BlobSink**.

Consulte [Propriedades do tipo RelationalSource](#copy-activity-properties) para obter a lista de propriedades com suporte pelo RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Mapeamento de tipo para Cassandra
| Tipo de Cassandra | Tipo baseado em .NET |
| --- | --- |
| LOCALIZADOS |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |Booleano |
| DECIMAL |Decimal |
| DOUBLE |Double |
| FLOAT |Único |
| INET |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
| Timeuuid |GUID |
| UUID |GUID |
| VARCHAR |String |
| VARINT |Decimal |

> [!NOTE]
> Para tipos de coleção (mapa, conjunto, lista, etc.), consulte [trabalhar com tipos de coleção Cassandra usando a seção da tabela virtual](#work-with-collections-using-virtual-table) .
>
> Não há suporte para tipos definidos pelo usuário.
>
> O comprimento de coluna binária e comprimentos de coluna de cadeia de caracteres não pode ser maior que 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções usando a tabela virtual
O Azure Data Factory usa um driver ODBC interno para se conectar e copiar dados do seu Cassandra Database. Para tipos de coleção, incluindo MAP, set e List, o driver renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver qualquer coluna de coleção, o driver gerará as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados que a tabela real, exceto para as colunas de coleção. A tabela base usa o mesmo nome que a tabela real que ela representa.
* Uma **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam as coleções são nomeadas usando o nome da tabela real, um separador "*VT*" e o nome da coluna.

As tabelas virtuais referem-se aos dados na tabela real, permitindo que o driver acesse os dados desnormalizados. Consulte a seção de exemplo para obter detalhes. Você pode acessar o conteúdo das coleções Cassandra consultando e unindo as tabelas virtuais.

Você pode usar o [Assistente de cópia](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) para exibir intuitivamente a lista de tabelas no banco de dados Cassandra, incluindo as tabelas virtuais, e visualizar os dados dentro. Você também pode construir uma consulta no assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, o seguinte "Exemplotable" é uma tabela de banco de dados Cassandra que contém uma coluna de chave primária de inteiro denominada "pk_int", uma coluna de texto denominada Value, uma coluna de lista, uma coluna de mapa e uma coluna Set (denominada "stringSet").

| pk_int | Valor | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valor de exemplo 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O driver geraria várias tabelas virtuais para representar essa única tabela. As colunas de chave estrangeira nas tabelas virtuais fazem referência às colunas de chave primária na tabela real e indicam a qual linha da tabela real a linha da tabela virtual corresponde.

A primeira tabela virtual é a tabela base chamada "ExampleTable", mostrada na tabela a seguir. A tabela base contém os mesmos dados que a tabela de banco de dado original, exceto as coleções, que são omitidas desta tabela e expandidas em outras tabelas virtuais.

| pk_int | Valor |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"valor de exemplo 3" |

As tabelas a seguir mostram as tabelas virtuais que renormalizam os dados da lista, do mapa e das colunas de cadeia de caracteres. As colunas com nomes que terminam com "_index" ou "_key" indicam a posição dos dados na lista ou no mapa original. As colunas com nomes que terminam com "_value" contêm os dados expandidos da coleção.

#### <a name="table-exampletable_vt_list"></a>Tabela "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabela "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Table “ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, confira [mapeando colunas Azure data Factory do conjunto de](data-factory-map-columns.md)informações

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
