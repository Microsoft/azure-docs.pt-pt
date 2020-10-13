---
title: Mover dados do PostgreSQL usando a Azure Data Factory
description: Saiba como mover dados da Base de Dados PostgreSQL usando a Azure Data Factory.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87082839"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Mover dados do PostgreSQL usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-onprem-postgresql-connector.md)
> * [Versão 2 (versão atual)](../connector-postgresql.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector PostgreSQL em V2](../connector-postgresql.md).


Este artigo explica como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma base de dados PostgreSQL no local. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de uma loja de dados PostgreSQL no local para qualquer loja de dados de sumidouro suportado. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte [as lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta a transferência de dados de uma base de dados PostgreSQL para outras lojas de dados, mas não para transferir dados de outras lojas de dados para uma base de dados PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

O serviço Data Factory suporta a ligação a fontes postgreSQL no local utilizando o Gateway de Gestão de Dados. Consulte [dados em movimento entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para saber sobre o Gateway de Gestão de Dados e instruções passo a passo sobre a configuração do gateway.

Gateway é necessário mesmo que a base de dados PostgreSQL esteja hospedada num Azure IaaS VM. Pode instalar gateway no mesmo IaaS VM que a loja de dados ou num VM diferente, desde que o gateway possa ligar-se à base de dados.

> [!NOTE]
> Consulte [os problemas de gateway de resolução de problemas](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre questões relacionadas com a ligação de resolução de problemas/gateways.

## <a name="supported-versions-and-installation"></a>Versões e instalação suportadas
Para a Gateway de Gestão de Dados ligar-se à Base de Dados PostgreSQL, instale o [fornecedor de dados Ngpsql para PostgreSQL](https://go.microsoft.com/fwlink/?linkid=282716) com a versão entre 2.0.12 e 3.1.9 no mesmo sistema que o Gateway de Gestão de Dados. A versão 7.4 ou superior da postgreSQL é suportada.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma loja de dados PostgreSQL no local utilizando diferentes ferramentas/APIs.

- A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.
- Também pode utilizar as seguintes ferramentas para criar um oleoduto:
  - Visual Studio
  - Azure PowerShell
  - Modelo Azure Resource Manager
  - API .NET
  - API REST

    Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados PostgreSQL no local, consulte [o exemplo JSON: Copiar dados da secção PostgreSQL para Azure Blob](#json-example-copy-data-from-postgresql-to-azure-blob) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas de uma loja de dados PostgreSQL:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado postgreSQL.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesPostgreSql** |Sim |
| servidor |Nome do servidor PostgreSQL. |Sim |
| base de dados |Nome da base de dados PostgreSQL. |Sim |
| esquema |O nome do esquema na base de dados. O nome do esquema é sensível a maiíssimos. |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à base de dados PostgreSQL. Os valores possíveis são: Anónimo, Básico e Windows. |Sim |
| nome de utilizador |Especifique o nome do utilizador se estiver a utilizar a autenticação Básica ou o Windows. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador especificada para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço data factory deve usar para ligar à base de dados PostgreSQL no local. |Sim |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados.

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção de tipos de favores para conjunto de dados do tipo **RelationalTable** (que inclui conjunto de dados PostgreSQL) tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na página da Base de Dados PostgreSQL a que o serviço ligado se refere. A mesa No nome é sensível a casos. |Não (se a **consulta** de **RelationalSource** for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e política estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na secção de tipos de atividade variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Quando a fonte é do tipo **RelationalSource** (que inclui PostgreSQL), as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `"query": "select * from \"MySchema\".\"MyTable\""`. |Não (se **o nome** de tabela do conjunto de **dados** for especificado) |

> [!NOTE]
> Schema e nomes de mesa são sensíveis a casos. Enr junto-os `""` em (cotações duplas) na consulta.

**Exemplo:**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>Exemplo JSON: Copiar dados de PostgreSQL a Azure Blob
Este exemplo fornece definições JSON de amostra que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados da base de dados PostgreSQL para o Azure Blob Storage. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

> [!IMPORTANT]
> Esta amostra fornece snippets JSON. Não inclui instruções passo a passo para a criação da fábrica de dados. Consulte [os dados em movimento entre as localizações no local e](data-factory-move-data-between-onprem-and-cloud.md) o artigo em nuvem para obter instruções passo a passo.

A amostra tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelacionalTable](data-factory-onprem-postgresql-connector.md#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. O [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma consulta resulta na base de dados PostgreSQL para uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, crie o portal de gestão de dados. As instruções estão nos [dados móveis entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

**Serviço ligado postgreSQL:**

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
**Serviço ligado ao armazenamento Azure Blob:**

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
**Conjunto de dados de entrada pós-SQL:**

A amostra pressupõe que criou uma tabela "MyTable" em PostgreSQL e contém uma coluna chamada "timetamp" para dados da série de tempo.

A Definição `"external": true` informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

**Conjunto de dados de saída Azure Blob:**

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta e o nome do ficheiro para a bolha são avaliados dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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

**Pipeline com atividade de Copy:**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar de hora a hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **RelationalSource** e o tipo **de pia** é definido para **BlobSink**. A consulta SQL especificada para a propriedade **de consulta** seleciona os dados da tabela public.usstates na base de dados PostgreSQL.

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
Como mencionado nas atividades de [movimento de dados](data-factory-data-movement-activities.md) artigo A atividade copy executa conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem de 2 etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados para PostgreSQL, os seguintes mapeamentos são usados do tipo PostgreSQL para .NET type.

| Tipo de base de dados PostgreSQL | Pseudónimos postgresSQL | .NET Tipo de quadro |
| --- | --- | --- |
| hora do abstime | |Datetime |
| bigint |int8 |Int64 |
| bigserial |série8 |Int64 |
| bit [n)] | |Byte[], String |
| pouco variando [ (n) ] |varbit |Byte[], String |
| boolean |bool |Booleano |
| caixa | |Byte[], String |
| bytea | |Byte[], String |
| carácter [n)] |char [n)] |Cadeia |
| caracteres variando [n)] |varchar [n)] |Cadeia |
| cid | |Cadeia |
| cidr | |Cadeia |
| círculo | |Byte[], String |
| date | |Datetime |
| encontro | |Cadeia |
| dupla precisão |flutuar8 |Double (Duplo) |
| inet | |Byte[], String |
| intarry | |Cadeia |
| int4range | |Cadeia |
| int8range | |Cadeia |
| número inteiro |int, int4 |Int32 |
| intervalo [campos] [p)] | |Timespan |
| json | |Cadeia |
| jsonb | |Byte[] |
| linha | |Byte[], String |
| lseg | |Byte[], String |
| macaddr | |Byte[], String |
| dinheiro | |Decimal |
| numérico [p, s)] |decimal [p,s)] |Decimal |
| numrange | |Cadeia |
| oid | |Int32 |
| caminho | |Byte[], String |
| pg_lsn | |Int64 |
| ponto | |Byte[], String |
| polígono | |Byte[], String |
| real |flutuar4 |Único |
| smallint |int2 |Int16 |
| menores |série2 |Int16 |
| série |série4 |Int32 |
| texto | |Cadeia |

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para obter informações sobre as colunas de mapeamento em conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
