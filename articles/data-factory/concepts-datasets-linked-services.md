---
title: Conjuntos de dados
description: Conheça os conjuntos de dados na Fábrica de Dados. Os conjuntos de dados representam dados de entrada/saída.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/25/2019
ms.openlocfilehash: 878ad98b118fa02a6659584ac60e3343a948cd20
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246269"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados na Fábrica de Dados Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-datasets-linked-services.md)

Este artigo descreve quais são os conjuntos de dados, como são definidos no formato JSON e como são usados em oleodutos Azure Data Factory.

Se é novo na Data Factory, consulte [introdução à Azure Data Factory](introduction.md) para uma visão geral.

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **oleoduto** é um agrupamento lógico de **atividades** que, em conjunto, desempenham uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Agora, um conjunto de **dados** é uma visão nomeada de dados que simplesmente aponta ou refere os dados que pretende utilizar nas suas **atividades** como inputs e saídas. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de Blobs do Azure especifica o contentor de blobs e a pasta no armazenamento de Blobs a partir dos quais a atividade deve ler os dados.

Antes de criar um conjunto de dados, deve criar um [**serviço ligado**](concepts-linked-services.md) para ligar a sua loja de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense desta forma; o conjunto de dados representa a estrutura dos dados dentro das lojas de dados ligadas, e o serviço ligado define a ligação à fonte de dados. Por exemplo, um serviço ligado ao Armazenamento Azure liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados Azure Blob representa o recipiente blob e a pasta dentro dessa conta de armazenamento Azure que contém as bolhas de entrada a serem processadas.

Aqui está um cenário de amostra. Para copiar dados do armazenamento Blob para uma base de dados SQL, cria dois serviços ligados: Azure Storage e Azure SQL Database. Em seguida, crie dois conjuntos de dados: conjunto de dados Azure Blob (que se refere ao serviço ligado ao Armazenamento Azure) e conjunto de dados da tabela Azure SQL (que se refere ao serviço ligado à Base de Dados Azure SQL). Os serviços ligados ao Armazenamento Azure e à Base de Dados Azure SQL contêm cordas de ligação que a Data Factory utiliza no prazo de execução para se ligarem ao seu Armazenamento Azure e à base de dados Azure SQL, respectivamente. O conjunto de dados Azure Blob especifica o recipiente blob e a pasta blob que contém as bolhas de entrada no seu armazenamento Blob. O conjunto de dados da tabela Azure SQL especifica a tabela SQL na sua base de dados SQL para a qual os dados serão copiados.

O diagrama seguinte mostra as relações entre o oleoduto, a atividade, o conjunto de dados e o serviço ligado na Fábrica de Dados:

![Relação entre o gasoduto, a atividade, o conjunto de dados, os serviços ligados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Dataset JSON
Um conjunto de dados na Data Factory é definido no seguinte formato JSON:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
A tabela seguinte descreve propriedades no JSON acima:

Propriedade | Descrição | Required |
-------- | ----------- | -------- |
nome | Nome do conjunto de dados. Ver [Azure Data Factory - Regras de nomeação.](naming-rules.md) |  Sim |
tipo | tipo de conjunto de dados. Especifique um dos tipos suportados pela Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para mais detalhes, consulte [os tipos de Conjunto de Dados](#dataset-type). | Sim |
estrutura | Esquema do conjunto de dados. Para mais detalhes, consulte [dataset schema](#dataset-structure-or-schema). | Não |
typeProperties | As propriedades do tipo são diferentes para cada tipo (por exemplo: Azure Blob, mesa Azure SQL). Para mais detalhes sobre os tipos suportados e suas propriedades, consulte o [tipo dataset](#dataset-type). | Sim |

### <a name="data-flow-compatible-dataset"></a>Conjunto de dados compatível com fluxo de dados



Consulte [os tipos de conjuntos](#dataset-type) de dados suportados para obter uma lista de tipos de conjuntos de dados compatíveis com o Fluxo de [Dados.](concepts-data-flow-overview.md) Os conjuntos de dados compatíveis com o Fluxo de Dados requerem definições de conjunto de dados de grãos finos para transformações. Assim, a definição JSON é ligeiramente diferente. Em vez de uma _propriedade_ estrutura, conjuntos de dados compatíveis com data Flow têm uma propriedade _de esquema._

No Data Flow, os conjuntos de dados são utilizados em transformações de origem e afunda-se. Os conjuntos de dados definem os dados básicos. Se os seus dados não tiver esquema, pode usar a deriva de esquemas para a sua fonte e afundar. O esquema no conjunto de dados representa o tipo e a forma dos dados físicos.

Ao definir o esquema a partir do conjunto de dados, obterá os tipos de dados relacionados, formatos de dados, localização de ficheiros e informações de ligação do serviço Linked associado. Os metadados dos conjuntos de dados aparecem na sua transformação de origem como *a projeção*de origem . A projeção na transformação de origem representa os dados do Fluxo de Dados com nomes e tipos definidos.

Quando importar o esquema de um conjunto de dados do Fluxo de Dados, selecione o botão **Import Schema** e opte por importar a partir da fonte ou de um ficheiro local. Na maioria dos casos, importará o esquema diretamente da fonte. Mas se já tiver um ficheiro de esquema local (um ficheiro Parquet ou CSV com cabeçalhos), pode direcionar a Data Factory para basear o esquema nesse ficheiro.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

A tabela seguinte descreve propriedades no JSON acima:

Propriedade | Descrição | Required |
-------- | ----------- | -------- |
nome | Nome do conjunto de dados. Ver [Azure Data Factory - Regras de nomeação.](naming-rules.md) |  Sim |
tipo | tipo de conjunto de dados. Especifique um dos tipos suportados pela Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para mais detalhes, consulte [os tipos de Conjunto de Dados](#dataset-type). | Sim |
schema | Esquema do conjunto de dados. Para mais detalhes, consulte conjuntos de [dados compatíveis](#dataset-type)com o Data Flow . | Não |
typeProperties | As propriedades do tipo são diferentes para cada tipo (por exemplo: Azure Blob, mesa Azure SQL). Para mais detalhes sobre os tipos suportados e suas propriedades, consulte o [tipo dataset](#dataset-type). | Sim |


## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo seguinte, o conjunto de dados representa uma tabela chamada MyTable numa base de dados SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Tenha em atenção os seguintes pontos:

- o tipo é definido para AzureSqlTable.
- a propriedade do tipo tableName (específica do tipo AzureSqlTable) está definida para MyTable.
- linkedServiceName refere-se a um serviço ligado do tipo AzureSqlDatabase, que é definido no próximo snippet JSON.

## <a name="dataset-type"></a>Tipo de conjunto de dados
Existem muitos tipos diferentes de conjuntos de dados, dependendo da loja de dados que utiliza. Pode encontrar a lista de dados armazenados suportados pela Data Factory a partir do artigo de visão geral do [Connector.](connector-overview.md) Clique numa loja de dados para aprender a criar um serviço ligado e um conjunto de dados para essa loja de dados.

No exemplo na secção anterior, o tipo do conjunto de dados é definido para **AzureSqlTable**. Da mesma forma, para um conjunto de dados Azure Blob, o tipo de conjunto de dados é definido para **AzureBlob,** como mostra o seguinte JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Estrutura de conjunto de dados ou esquema
Os conjuntos de dados da secção **de estrutura** ou **schema** (data flow compatível) são opcionais. Define o esquema do conjunto de dados contendo uma recolha de nomes e tipos de dados de colunas. Utiliza a secção da estrutura para fornecer informações de tipo que são usadas para converter tipos e colunas de mapas da fonte para o destino.

Cada coluna na estrutura contém as seguintes propriedades:

Propriedade | Descrição | Required
-------- | ----------- | --------
nome | Nome da coluna. | Sim
tipo | Tipo de dados da coluna. Data Factory suporta os seguintes tipos de dados provisórios como valores permitidos: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset e Timespan** | Não
culture | . Cultura baseada em NET a utilizar quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. | Não
format | Cadeia de formato a utilizar quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. Consulte as [cordas de data e formato](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) de tempo personalizados sobre como formatar a data. | Não

### <a name="example"></a>Exemplo
No exemplo seguinte, suponha que os dados blob de origem estão no formato CSV e contém três colunas: userid, name e lastlogindate. São do tipo Int64, String e Datetime com um formato de data personalizada usando nomes franceses abreviados para o dia da semana.

Defina a estrutura do conjunto de dados Blob da seguinte forma, juntamente com as definições de tipo para as colunas:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Orientação

As seguintes diretrizes ajudam-no a entender quando incluir informações estruturais e o que incluir na secção de **estrutura.** Saiba mais sobre como os dados da fábrica de dados mapeiam dados de origem para afundar e quando especificar informações estruturais de [Schema e mapeamento](copy-activity-schema-and-type-mapping.md)de tipo .

- **Para obter fontes de dados de esquemas fortes,** especifique a secção da estrutura apenas se quiser que as colunas de origem do mapa afundem colunas, e os seus nomes não são os mesmos. Este tipo de fonte de dados estruturada armazena esquema de dados e tipo informação juntamente com os próprios dados. Exemplos de fontes de dados estruturadas incluem SQL Server, Oracle e Azure SQL Database.<br/><br/>Como a informação do tipo já está disponível para fontes de dados estruturadas, não deve incluir informações de tipo quando incluir a secção da estrutura.
- **Para fontes de dados de esquemas não/fracas, por exemplo, ficheiros de texto no armazenamento de blob,** inclua a estrutura quando o conjunto de dados é uma entrada para uma atividade de cópia, e os tipos de dados de dados de origem devem ser convertidos para tipos nativos para o lavatório. E inclua estrutura quando quiser mapear colunas de origem para afundar colunas.

## <a name="create-datasets"></a>Criar conjuntos de dados
Pode criar conjuntos de dados utilizando uma destas ferramentas ou SDKs: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Modelo de Gestor de Recursos Azure e portal Azure

## <a name="current-version-vs-version-1-datasets"></a>Versão atual vs. versão 1 conjuntos de dados

Aqui ficam algumas diferenças entre data factory e data factory versão 1 conjuntos de dados:

- A propriedade externa não é suportada na versão atual. É substituído por um [gatilho.](concepts-pipeline-execution-triggers.md)
- As propriedades políticas e de disponibilidade não são suportadas na versão atual. A hora de início de um gasoduto depende dos [gatilhos.](concepts-pipeline-execution-triggers.md)
- Conjuntos de dados com âmbito (conjuntos de dados definidos num pipeline) não são suportados na versão atual.

## <a name="next-steps"></a>Passos Seguintes
Consulte o seguinte tutorial para obter instruções passo a passo para a criação de oleodutos e conjuntos de dados utilizando uma destas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Quickstart: criar uma fábrica de dados usando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Quickstart: criar uma fábrica de dados utilizando a Rest API](quickstart-create-data-factory-rest-api.md)
- [Quickstart: criar uma fábrica de dados utilizando o portal Azure](quickstart-create-data-factory-portal.md)
