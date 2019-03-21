---
title: Mover dados do Cassandra com o Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados de uma base de dados de Cassandra no local com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0a3adbd082c68121e762fd03c2221a0c800f0bc5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57892647"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Mover dados de uma base de dados de Cassandra no local com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-onprem-cassandra-connector.md)
> * [Versão 2 (versão atual)](../connector-cassandra.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de Cassandra no V2](../connector-cassandra.md).

Este artigo explica como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma base de dados do Cassandra no local. Ele se baseia no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo, que apresenta uma visão geral do movimento de dados com a atividade de cópia.

Pode copiar dados de um arquivo de dados do Cassandra no local para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados suportados como sinks a atividade de cópia, consulte a [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabela. O Data factory suporta, atualmente, apenas mover dados de um arquivo de dados do Cassandra para outros arquivos de dados, mas não para mover dados de outros arquivos de dados para um arquivo de dados do Cassandra.

## <a name="supported-versions"></a>Versões suportadas
O conector do Cassandra suporta as seguintes versões do Cassandra: 2.x e 3.x. Para a atividade em execução no Runtime de integração autoalojado, Cassandra 3.x é suportada desde o runtime de integração versão 3.7 e posteriores.

## <a name="prerequisites"></a>Pré-requisitos
Para o serviço do Azure Data Factory conseguir ligar à sua base de dados do Cassandra no local, tem de instalar um Gateway de gestão de dados no mesmo computador que aloja a base de dados ou numa máquina separada para evitar competindo por recursos com a base de dados. O Data Management Gateway é um componente que liga origens de dados no local para serviços cloud de forma segura e gerida. Ver [Data Management Gateway](data-factory-data-management-gateway.md) artigo para obter detalhes sobre o Data Management Gateway. Ver [mover dados do local para a cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo sobre como configurar o gateway de um pipeline de dados mover dados.

Tem de utilizar o gateway para ligar a uma base de dados do Cassandra, mesmo que a base de dados está alojado na cloud, por exemplo, numa VM de IaaS do Azure. Y pode ter o gateway na mesma VM que aloja a base de dados ou numa VM separada, desde que o gateway, pode ligar à base de dados.

Ao instalar o gateway, ele automaticamente instala um driver ODBC do Microsoft Cassandra utilizado para ligar à base de dados Cassandra. Portanto, não precisa de instalar manualmente qualquer driver no computador gateway, quando se copiam dados a partir da base de dados do Cassandra.

> [!NOTE]
> Ver [resolver problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter sugestões sobre resolução de problemas do gateway de ligação/problemas relacionados com.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de um arquivo de dados do Cassandra no local através de APIs/ferramentas diferentes.

- A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados.
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**e  **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia.
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON. Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de um arquivo de dados do Cassandra no local, consulte [exemplo de JSON: Copiar dados do Cassandra para BLOBs do Azure](#json-example-copy-data-from-cassandra-to-azure-blob) seção deste artigo.

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas para um arquivo de dados do Cassandra:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do Cassandra.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida como: **OnPremisesCassandra** |Sim |
| anfitrião |Um ou mais endereços IP ou nomes de anfitrião dos servidores de Cassandra.<br/><br/>Especifica uma lista separada por vírgulas de endereços IP ou nomes de anfitrião para ligar a todos os servidores em simultâneo. |Sim |
| porta |A porta TCP que o servidor Cassandra utiliza para escutar ligações de cliente. |Não, o valor predefinido: 9042 |
| authenticationType |Básico ou anónimo |Sim |
| o nome de utilizador |Especifique o nome de utilizador para a conta de utilizador. |Sim, se authenticationType está definido para básico. |
| palavra-passe |Especifique a palavra-passe da conta de utilizador. |Sim, se authenticationType está definido para básico. |
| gatewayName |O nome do gateway que é utilizado para ligar à base de dados do Cassandra no local. |Sim |
| encryptedCredential |Credencial encriptada pelo gateway. |Não |

>[!NOTE]
>Atualmente a conexão com o Cassandra através de SSL não é suportada.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **CassandraTable** tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| keyspace |Nome do keyspace ou esquema na base de dados do Cassandra. |Sim (se **consulta** para **CassandraSource** não está definido). |
| tableName |Nome da tabela na base de dados do Cassandra. |Sim (se **consulta** para **CassandraSource** não está definido). |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

Ao passo que, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Quando a origem é do tipo **CassandraSource**, as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Consulta de SQL-92 ou consulta CQL. Ver [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique **keyspace name.table nome** para representar a tabela que pretende consultar. |Não (se for tableName e keyspace num conjunto de dados estão definidos). |
| consistencyLevel |O nível de consistência Especifica o número de réplicas devem responder a uma solicitação de leitura antes de retornar dados para a aplicação cliente. Cassandra verifica o número especificado de réplicas de dados satisfazer a solicitação de leitura. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Ver [configurar a consistência dos dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. |Não. Valor predefinido é um. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>Exemplo JSON: Copiar dados do Cassandra para BLOBs do Azure
Este exemplo fornece definições de JSON de exemplo que pode utilizar para criar um pipeline com [portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de uma base de dados do Cassandra no local para um armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um dos sinks indicados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.

> [!IMPORTANT]
> Este exemplo fornece trechos JSON. Não inclui instruções passo a passo para criar a fábrica de dados. Ver [mover dados entre localizações no local e na cloud](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter instruções passo a passo.

O exemplo possui as seguintes entidades do data factory:

* Um serviço ligado do tipo [OnPremisesCassandra](#linked-service-properties).
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [CassandraTable](#dataset-properties).
* Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [CassandraSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Serviço ligado do Cassandra:**

Este exemplo utiliza a **Cassandra** serviço ligado. Ver [serviço ligado do Cassandra](#linked-service-properties) secção para as propriedades suportadas por este serviço ligado.

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

**Serviço ligado do armazenamento do Azure:**

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

**Conjunto de dados entrado de Cassandra:**

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

A definição **externo** ao **verdadeiro** informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

**Conjunto de dados de Blobs do Azure:**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1).

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

**Atividade de cópia num pipeline com a origem de Cassandra e de sink do Blob:**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **CassandraSource** e **sink** tipo está definido como **BlobSink**.

Ver [propriedades do tipo RelationalSource](#copy-activity-properties) para obter a lista das propriedades compatíveis com o RelationalSource.

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

### <a name="type-mapping-for-cassandra"></a>Mapeamento do tipo de Cassandra
| Tipo de Cassandra | .NET com base em tipo |
| --- | --- |
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
>

## <a name="work-with-collections-using-virtual-table"></a>Trabalhar com coleções por meio de tabela virtual
O Azure Data Factory utiliza um controlador ODBC incorporado para ligar e copiar dados de sua base de dados do Cassandra. Para tipos de coleção, incluindo o mapa, o conjunto e lista, o driver renormalizes os dados em tabelas virtuais correspondentes. Especificamente, se uma tabela contém quaisquer colunas de coleção, o driver gera as tabelas virtuais seguintes:

* R **tabela base**, que contém os mesmos dados que a tabela real, exceto as colunas de coleção. A tabela base utiliza o mesmo nome, como a tabela real que representa.
* R **tabela virtual** para cada coluna de coleção, que expande os dados aninhados. As tabelas virtuais que representam as coleções são nomeadas usando o nome da tabela real, um separador de "*vt*" e o nome da coluna.

Tabelas virtuais referem-se aos dados na tabela real, permitindo que o driver acessar os dados desnormalizados. Consulte a secção de exemplo para obter detalhes. Pode acessar o conteúdo das coleções de Cassandra consultando e associar as tabelas virtuais.

Pode utilizar o [Assistente para copiar](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivamente ver a lista de tabelas na base de dados do Cassandra incluindo as tabelas virtuais e pré-visualizar os dados dentro. Também pode construir uma consulta no Assistente de cópia e validar para ver o resultado.

### <a name="example"></a>Exemplo
Por exemplo, "Seguinte ExampleTable" é uma tabela de base de dados do Cassandra que contém a coluna de chave primária de um número inteiro com o nome "pk_int", uma coluna de texto valor com nome, uma coluna de lista, uma coluna de mapa e uma coluna de conjunto (com o nome "StringSet").

| pk_int | Value | Lista | Mapa | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"valor de exemplo 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"valor de exemplo 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

O controlador irá gerar várias tabelas virtuais para representar nesta única tabela. As colunas de chave estrangeiras nas tabelas virtuais referenciam as colunas de chave primárias na tabela real e indicam que linhas da tabela real a linha da tabela virtual corresponde à.

A primeira tabela virtual é a tabela de base com o nome "ExampleTable" é mostrada na seguinte tabela. A tabela base contém os mesmos dados da tabela de base de dados original, exceto para as coleções, o que são omitidos desta tabela e expandido em outras tabelas virtuais.

| pk_int | Value |
| --- | --- |
| 1 |"valor de exemplo 1" |
| 3 |"valor de exemplo 3" |

As tabelas seguintes mostram as tabelas de virtual renormalizar dados das colunas de lista, o mapa e StringSet. As colunas com nomes que terminam com "_index" ou "c_have" indicam a posição dos dados dentro da lista original ou do mapa. As colunas com nomes que terminam com value"contêm os dados expandidos da coleção.

#### <a name="table-exampletablevtlist"></a>Table “ExampleTable_vt_List”:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>Table “ExampleTable_vt_Map”:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>Table “ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Origem do mapa para colunas de sink
Para saber mais sobre as colunas de mapeamento no conjunto de dados de origem para colunas no conjunto de dados de sink, veja [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura passível de repetição de fontes relacionais
Quando armazena a cópia de dados de dados relacionais, tenha a capacidade de repetição em mente para evitar resultados indesejados. No Azure Data Factory, pode voltar a executar um setor manualmente. Também pode configurar a política de repetição para um conjunto de dados para que um setor será novamente executado quando ocorre uma falha. Quando um setor será novamente executado de qualquer forma, terá de certificar-se de que os mesmos dados é de leitura não questão número de vezes que um setor é executado. Ver [Repeatable ler a partir de origens relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
