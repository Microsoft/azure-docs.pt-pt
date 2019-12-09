---
title: Mover dados do PostgreSQL usando Azure Data Factory
description: Saiba mais sobre como mover dados do banco de dado PostgreSQL usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 888d9ebc-2500-4071-b6d1-0f6bd1b5997c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 37c83e77cadae002ff701a08c4b36a86f7cab9a0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929074"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Mover dados do PostgreSQL usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-onprem-postgresql-connector.md)
> * [Versão 2 (versão atual)](../connector-postgresql.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector do PostgreSQL na v2](../connector-postgresql.md).


Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um banco de dado PostgreSQL local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados PostgreSQL local para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, o data Factory dá suporte à movimentação de dados de um banco de dados PostgreSQL para outros repositórios de armazenamento, mas não para a movimentação de dados de outros armazenamentos de dados para o PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Data Factory serviço dá suporte à conexão a fontes PostgreSQL locais usando o gateway de Gerenciamento de Dados. Confira o artigo [movendo dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre gerenciamento de dados gateway e instruções passo a passo sobre como configurar o gateway.

O gateway é necessário mesmo se o banco de dados PostgreSQL estiver hospedado em uma VM IaaS do Azure. Você pode instalar o gateway na mesma VM IaaS que o armazenamento de dados ou em uma VM diferente, contanto que o gateway possa se conectar ao banco de dado.

> [!NOTE]
> Consulte [solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre como solucionar problemas relacionados à conexão/gateway.

## <a name="supported-versions-and-installation"></a>Versões e instalação com suporte
Para Gerenciamento de Dados gateway para conectar-se ao banco de dados PostgreSQL, instale o [Ngpsql Data Provider para PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) com a versão entre 2.0.12 e 3.1.9 no mesmo sistema que o Gateway de gerenciamento de dados. Há suporte para o PostgreSQL versão 7,4 e superior.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de um armazenamento de dados PostgreSQL local usando diferentes ferramentas/APIs.

- A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.
- Você também pode usar as seguintes ferramentas para criar um pipeline:
  - Visual Studio
  - Azure PowerShell
  - Modelo Azure Resource Manager
  - API .NET
  - API REST

    Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um armazenamento de dados PostgreSQL local, confira a seção [exemplo de JSON: copiar dados do PostgreSQL para o blob do Azure](#json-example-copy-data-from-postgresql-to-azure-blob) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para um armazenamento de dados PostgreSQL:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado do PostgreSQL.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OnPremisesPostgreSql** |Sim |
| servidor |Nome do servidor PostgreSQL. |Sim |
| base de dados |Nome do banco de dados PostgreSQL. |Sim |
| schema |Nome do esquema no banco de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao banco de dados PostgreSQL. Os valores possíveis são: Anonymous, Basic e Windows. |Sim |
| o nome de utilizador |Especifique o nome de usuário se você estiver usando a autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a senha para a conta de usuário especificada para o nome do usuário. |Não |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao banco de dados PostgreSQL local. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A seção typeproperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties para o conjunto de um do tipo **RelationalTable** (que inclui o conjunto de texto PostgreSQL) tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados PostgreSQL à qual o serviço vinculado se refere. O TableName diferencia maiúsculas de minúsculas. |Não (se a **consulta** de **RelationalSource** for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na seção typeproperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Quando a fonte é do tipo **RelationalSource** (que inclui PostgreSQL), as seguintes propriedades estão disponíveis na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: `"query": "select * from \"MySchema\".\"MyTable\""`. |Não (se **TableName** de **DataSet** for especificado) |

> [!NOTE]
> Os nomes de esquema e tabela diferenciam maiúsculas de minúsculas. Coloque-os em `""` (aspas duplas) na consulta.

**Exemplo:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Exemplo de JSON: copiar dados do PostgreSQL para o blob do Azure
Este exemplo fornece definições de JSON de exemplo que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados do banco de dado PostgreSQL para o armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

> [!IMPORTANT]
> Este exemplo fornece trechos de código JSON. Ele não inclui instruções passo a passo para criar o data factory. Confira o artigo [movendo dados entre os locais e a nuvem no local](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que usa [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta no banco de dado PostgreSQL para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [movendo dados entre os locais e a nuvem do local](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado do PostgreSQL:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Serviço vinculado do armazenamento de BLOBs do Azure:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**Conjunto de dados de entrada PostgreSQL:**

O exemplo supõe que você criou uma tabela "MyTable" no PostgreSQL e que ela contém uma coluna chamada "timestamp" para dados de série temporal.

A configuração `"external": true` informa ao serviço Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
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

**Conjunto de resultados de saída de blob do Azure:**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline com atividade de cópia:**

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada por hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **RelationalSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **Query** seleciona os dados da tabela Public. usstates no banco de dados PostgreSQL.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Mapeamento de tipo para PostgreSQL
Conforme mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para o PostgreSQL, os seguintes mapeamentos são usados do tipo PostgreSQL para o tipo .NET.

| Tipo de banco de dados PostgreSQL | Aliases de PostgresSQL | Tipo de .NET Framework |
| --- | --- | --- |
| abstime | |DateTime |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Byte [], Cadeia de caracteres |
| variação de bit [(n)] |varbit |Byte [], Cadeia de caracteres |
| boolean |booleano |Booleano |
| quadro | |Byte [], Cadeia de caracteres |
| bytea | |Byte [], Cadeia de caracteres |
| caractere [(n)] |Char [(n)] |String |
| variação de caractere [(n)] |varchar [(n)] |String |
| CID | |String |
| CIDR | |String |
| círculo | |Byte [], Cadeia de caracteres |
| date | |DateTime |
| daterange | |String |
| precisão dupla |float8 |Double |
| inet | |Byte [], Cadeia de caracteres |
| intarry | |String |
| int4range | |String |
| int8range | |String |
| número inteiro |int, int4 |Int32 |
| Interval [campos] [(p)] | |Timespan |
| json | |String |
| jsonb | |Byte[] |
| linha | |Byte [], Cadeia de caracteres |
| lseg | |Byte [], Cadeia de caracteres |
| macaddr | |Byte [], Cadeia de caracteres |
| money | |Decimal |
| numeric [(p, s)] |Decimal [(p, s)] |Decimal |
| numrange | |String |
| oid | |Int32 |
| Caminho | |Byte [], Cadeia de caracteres |
| pg_lsn | |Int64 |
| registo | |Byte [], Cadeia de caracteres |
| Polygon | |Byte [], Cadeia de caracteres |
| real |float4 |Único |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| série |serial4 |Int32 |
| texto | |String |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, confira [mapeando colunas Azure data Factory do conjunto de](data-factory-map-columns.md)informações

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
