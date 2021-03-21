---
title: Mover dados de Cassandra usando data factory
description: Saiba como mover dados de uma base de dados cassandra no local usando a Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 005fd85a152ee2765facda0d961bd9119d1598e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387415"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Mover dados de uma base de dados cassandra no local usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-cassandra-connector.md)
> * [Versão 2 (versão atual)](../connector-cassandra.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Cassandra em V2](../connector-cassandra.md).

Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma base de dados cassandra no local. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de uma loja de dados da Cassandra no local para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma loja de dados Cassandra para outras lojas de dados, mas não para transferir dados de outras lojas de dados para uma loja de dados cassandra.

## <a name="supported-versions"></a>Versões suportadas
O conector Cassandra suporta as seguintes versões de Cassandra: 2.x e 3.x. Para a atividade em execução no Tempo de Execução de Integração Auto-hospedado, Cassandra 3.x é suportada desde a versão 3.7 e superior.

## <a name="prerequisites"></a>Pré-requisitos
Para que o serviço Azure Data Factory possa ligar-se à base de dados cassandra no local, deve instalar um Gateway de Gestão de Dados na mesma máquina que acolhe a base de dados ou numa máquina separada para evitar competir por recursos com a base de dados. Data Management Gateway é um componente que liga fontes de dados no local aos serviços na nuvem de forma segura e gerida. Consulte o artigo [do Data Management Gateway](data-factory-data-management-gateway.md) para obter mais informações sobre o Data Management Gateway. Consulte [os dados de mover os dados das instalações para o](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para obter instruções passo a passo sobre a configuração do gateway de um pipeline de dados para mover dados.

Você deve usar o gateway para ligar a uma base de dados Cassandra mesmo que a base de dados esteja hospedada na nuvem, por exemplo, em um Azure IaaS VM. Y Você pode ter o gateway no mesmo VM que hospeda a base de dados ou em um VM separado, desde que o gateway possa ligar-se à base de dados.

Quando instala o gateway, instala automaticamente um controlador ODBC da Microsoft Cassandra utilizado para ligar à base de dados cassandra. Por isso, não é necessário instalar manualmente qualquer controlador na máquina de gateway ao copiar dados da base de dados cassandra.

> [!NOTE]
> Consulte [os problemas de gateway de resolução de problemas](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre questões relacionadas com a ligação de resolução de problemas/gateways.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma loja de dados cassandra no local, utilizando diferentes ferramentas/APIs.

- A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados cassandra no local, consulte [o exemplo JSON: Copiar dados de Cassandra para a secção Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades da JSON que são usadas para definir entidades da Data Factory específicas de uma loja de dados Cassandra:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado à Cassandra.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesCassandra** |Yes |
| anfitrião |Um ou mais endereços IP ou nomes de anfitrião de servidores Cassandra.<br/><br/>Especifique uma lista separada de vírgulas de endereços IP ou nomes de anfitrião para ligar a todos os servidores simultaneamente. |Yes |
| porta |A porta TCP que o servidor Cassandra usa para ouvir as ligações do cliente. |Não, valor predefinido: 9042 |
| authenticationType |Básico, ou Anónimo |Yes |
| nome de utilizador |Especifique o nome de utilizador para a conta do utilizador. |Sim, se a autenticaçãoType estiver definida como Basic. |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador. |Sim, se a autenticaçãoType estiver definida como Basic. |
| gatewayName |O nome do portal que é usado para ligar à base de dados cassandra no local. |Yes |
| criptografadoCredential |Credencial encriptada pelo portal. |No |

>[!NOTE]
>Atualmente, a ligação com a Cassandra usando TLS não é suportada.

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção de tipos de direitos para conjunto de dados do tipo **CassandraTable** tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| keyspace |Nome do espaço-chave ou esquema na base de dados de Cassandra. |Sim (Se **a consulta** para **CassandraSource** não estiver definida). |
| tableName |O nome da mesa na base de dados da Cassandra. |Sim (Se **a consulta** para **CassandraSource** não estiver definida). |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e política estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na secção de tipos de atividade variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Quando a fonte é do tipo **CassandraSource,** as seguintes propriedades estão disponíveis na secção de tipos de propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Consulta SQL-92 ou consulta CQL. Consulte [a referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique **o nome do keyspace.table** para representar a tabela que pretende consultar. |Não (se o nome de tabela e o espaço de teclas no conjunto de dados forem definidos). |
| consistênciaLevel |O nível de consistência especifica quantas réplicas devem responder a um pedido de leitura antes de devolver os dados à aplicação do cliente. Cassandra verifica o número especificado de réplicas de dados para satisfazer o pedido de leitura. |UM, DOIS, TRÊS, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Consulte [a consistência dos dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter informações mais pormenores. |N.º O valor predefinido é ONE. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Exemplo JSON: Copiar dados de Cassandra para Azure Blob
Este exemplo fornece definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar dados de uma base de dados cassandra no local para um Azure Blob Storage. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

> [!IMPORTANT]
> Esta amostra fornece snippets JSON. Não inclui instruções passo a passo para a criação da fábrica de dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo.

A amostra tem as seguintes entidades de fábrica de dados:

* Um serviço ligado do tipo [OnPremisesCassandra.](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [CassandraTable](#dataset-properties).
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [CassandraSource](#copy-activity-properties) e [BlobSink.](data-factory-azure-blob-connector.md#copy-activity-properties)

**Serviço ligado a Cassandra:**

Este exemplo utiliza o serviço ligado à **Cassandra.** Consulte a secção [de serviços ligadas à Cassandra](#linked-service-properties) para os imóveis suportados por este serviço ligado.

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

**Serviço ligado a Azure Storage:**

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

**Conjunto de dados de entrada cassandra:**

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

A definição **externa** à **verdadeira** informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1).

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

**Copiar a atividade num oleoduto com a fonte Cassandra e a pia Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **CassandraSource** e o tipo **de pia** é definido para **BlobSink**.

Consulte [as propriedades do tipo RelationalSource](#copy-activity-properties) para a lista de propriedades suportadas pela RelationalSource.

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

### <a name="type-mapping-for-cassandra"></a>Tipo de mapeamento para Cassandra
| Tipo Cassandra | .NET Tipo de base |
| --- | --- |
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
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções usando mesa virtual
A Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados da sua base de dados Cassandra. Para tipos de recolha, incluindo mapa, conjunto e lista, o condutor renormaliza os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contiver quaisquer colunas de recolha, o condutor gera as seguintes tabelas virtuais:

* Uma **tabela base**, que contém os mesmos dados que a tabela real, com exceção das colunas de recolha. A tabela base usa o mesmo nome que a mesa real que representa.
* Uma **tabela virtual** para cada coluna de recolha, que expande os dados aninhados. As tabelas virtuais que representam coleções são nomeadas usando o nome da mesa real, um separador "*vt*" e o nome da coluna.

As tabelas virtuais referem-se aos dados na tabela real, permitindo ao condutor aceder aos dados denormalizados. Consulte a secção Exemplo para mais detalhes. Você pode aceder ao conteúdo das coleções Cassandra consultando e juntando as tabelas virtuais.

Pode utilizar o [Copy Wizard](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) para visualizar intuitivamente a lista de tabelas na base de dados de Cassandra, incluindo as tabelas virtuais, e visualizar os dados no seu interior. Também pode construir uma consulta no Copy Wizard e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, o seguinte "ExemploTable" é uma tabela de base de dados Cassandra que contém uma coluna-chave primária chamada "pk_int", uma coluna de texto denominada valor, uma coluna de lista, uma coluna de mapas e uma coluna de conjunto (chamada "StringSet").

| pk_int | Valor | Lista | Mapa | Conjunto de Cordas |
| --- | --- | --- | --- | --- |
| 1 |"Valor da amostra 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"Valor da amostra 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O condutor geraria várias tabelas virtuais para representar esta única tabela. As colunas-chave estrangeiras nas tabelas virtuais referem as colunas-chave primárias na tabela real, e indicam a que linha de mesa real a linha de tabela virtual corresponde.

A primeira tabela virtual é a tabela base chamada "ExemploTable" é mostrada na tabela seguinte. A tabela base contém os mesmos dados que a tabela de bases de dados original, com exceção das coleções, que são omitidas desta tabela e expandidas noutras tabelas virtuais.

| pk_int | Valor |
| --- | --- |
| 1 |"Valor da amostra 1" |
| 3 |"Valor da amostra 3" |

As tabelas seguintes mostram as tabelas virtuais que renormalizam os dados das colunas List, Map e StringSet. As colunas com nomes que terminam com "_index" ou "_key" indicam a posição dos dados dentro da lista ou mapa original. As colunas com nomes que terminam com "_value" contêm os dados expandidos da recolha.

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

#### <a name="table-exampletable_vt_stringset"></a>Tabela "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para obter informações sobre as colunas de mapeamento em conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
