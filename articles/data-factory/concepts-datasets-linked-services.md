---
title: Conjuntos de dados no Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre conjuntos de dados no Data Factory. Conjuntos de dados representam os dados de entrada/saída.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: 6b74f217d296b5de8886f608b1bc92e908b5d8b4
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866457"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-datasets-linked-services.md)

Este artigo descreve os conjuntos de dados são, como são definidas no formato JSON, e como são utilizados no Azure Data Factory pipelines.

Se estiver familiarizado com o Data Factory, veja [introdução ao Azure Data Factory](introduction.md) para uma descrição geral.

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. R **pipeline** é um agrupamento lógico de **atividades** que em conjunto, realizam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Agora, um **conjunto de dados** é uma vista com o nome de dados que simplesmente pontos ou faz referência aos dados que pretende utilizar no seu **atividades** como entradas e saídas. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de Blobs do Azure especifica o contentor de blobs e a pasta no armazenamento de Blobs a partir dos quais a atividade deve ler os dados.

Antes de criar um conjunto de dados, tem de criar uma [ **serviço ligado** ](concepts-linked-services.md) para ligar o seu armazenamento de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense nesse assunto desta forma; o conjunto de dados representa a estrutura dos dados dentro dos arquivos de dados ligados e o serviço ligado define a ligação à origem de dados. Por exemplo, um armazenamento do Azure ligado serviço liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados de Blobs do Azure representa o contentor de BLOBs e a pasta dentro dessa conta de armazenamento do Azure que contém os blobs de entrada para serem processados.

Eis um cenário de exemplo. Para copiar dados do armazenamento de BLOBs para base de dados SQL, criar dois serviços ligados: Armazenamento do Azure e Azure base de dados SQL. Em seguida, crie dois conjuntos de dados: Conjunto de dados Blob do Azure (que se refere ao serviço ligado do armazenamento do Azure) e o conjunto de dados de tabela SQL do Azure (o que se refere-se para o serviço de base de dados do SQL do Azure ligada). O armazenamento do Azure e serviços de base de dados do SQL Azure ligado contenham cadeias de ligação que o Data Factory utiliza no tempo de execução para ligar para o armazenamento do Azure e a base de dados SQL do Azure, respectivamente. O conjunto de dados de Blobs do Azure Especifica o contentor de BLOBs e a pasta de BLOBs que contém os blobs de entrada no armazenamento de Blobs. O conjunto de dados de tabela SQL do Azure Especifica a tabela SQL na base de dados SQL para o qual os dados estão a ser copiado.

O diagrama seguinte mostra as relações entre o pipeline, atividade, conjunto de dados e serviço ligado no Data Factory:

![Relação entre o pipeline, atividade, conjunto de dados, serviços ligados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Conjunto de dados JSON
Um conjunto de dados no Data Factory é definido no seguinte formato JSON:

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
A tabela seguinte descreve as propriedades no JSON acima:

Propriedade | Descrição | Necessário |
-------- | ----------- | -------- |
nome | Nome do conjunto de dados. Ver [do Azure Data Factory - regras de nomenclatura](naming-rules.md). |  Sim |
tipo | Tipo de conjunto de dados. Especifique um dos tipos suportados pela fábrica de dados (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter detalhes, consulte [tipos de conjunto de dados](#dataset-type). | Sim |
estrutura | Esquema do conjunto de dados. Para obter detalhes, consulte [esquema de conjunto de dados](#dataset-structure-or-schema). | Não |
typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: O Azure Blob, tabela SQL do Azure). Para obter detalhes sobre os tipos suportados e as respetivas propriedades, consulte [tipo de conjunto de dados](#dataset-type). | Sim |

### <a name="data-flow-compatible-dataset"></a>Conjunto de dados compatível do fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Ver [tipos de conjunto de dados suportados](#dataset-type) para obter uma lista de tipos de conjunto de dados que são [fluxo de dados](concepts-data-flow-overview.md) compatível. Conjuntos de dados que são compatíveis com o fluxo de dados exigem definições de conjunto de dados refinado para transformações. Portanto, a definição de JSON é um pouco diferente. Em vez de um _estrutura_ propriedade, conjuntos de dados que são compatíveis de fluxo de dados têm um _esquema_ propriedade.

No fluxo de dados, conjuntos de dados são utilizados nas transformações de origem e sink. Os conjuntos de dados definem os esquemas de dados básicos. Se os dados não tiverem nenhum esquema, pode utilizar os descompassos de esquema para a sua origem e sink. O esquema do conjunto de dados representa o tipo de dados físico e de forma.

Ao definir o esquema do conjunto de dados, obterá os tipos de dados relacionados, formatos de dados, localização de ficheiros e informações de ligação do serviço ligado associado. Metadados de conjuntos de dados é apresentado na sua transformação de origem como a fonte *projeção*. A projeção na transformação de origem representa os dados de fluxo de dados de mensagens em fila com nomes predefinidos e de tipos.

Quando importar o esquema de um conjunto de dados de fluxo de dados, selecione o **importar esquema** botão e optar por importar a partir da origem ou a partir de um ficheiro local. Na maioria dos casos, irá importar o esquema diretamente da fonte. Mas, se já tiver um ficheiro de esquema local (um ficheiro Parquet ou CSV com cabeçalhos), pode direcionar o Data Factory para o esquema nesse ficheiro de base.


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

A tabela seguinte descreve as propriedades no JSON acima:

Propriedade | Descrição | Necessário |
-------- | ----------- | -------- |
nome | Nome do conjunto de dados. Ver [do Azure Data Factory - regras de nomenclatura](naming-rules.md). |  Sim |
tipo | Tipo de conjunto de dados. Especifique um dos tipos suportados pela fábrica de dados (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter detalhes, consulte [tipos de conjunto de dados](#dataset-type). | Sim |
schema | Esquema do conjunto de dados. Para obter detalhes, consulte [fluxo de dados compatíveis com conjuntos de dados](#dataset-type). | Não |
typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: O Azure Blob, tabela SQL do Azure). Para obter detalhes sobre os tipos suportados e as respetivas propriedades, consulte [tipo de conjunto de dados](#dataset-type). | Sim |


## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo a seguir, o conjunto de dados representa uma tabela chamada MyTable numa base de dados SQL.

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

- tipo está definido como AzureSqlTable.
- (específica para o tipo de AzureSqlTable) de propriedade de tipo tableName é definida como MyTable.
- linkedServiceName refere-se para um serviço ligado do tipo AzureSqlDatabase, que é definido no fragmento JSON seguinte.

## <a name="dataset-type"></a>Tipo de conjunto de dados
Existem muitos tipos diferentes de conjuntos de dados, consoante o arquivo de dados que utiliza. Consulte a tabela seguinte para obter uma lista dos arquivos de dados suportado pelo Data Factory. Clique num arquivo de dados para saber como criar um serviço ligado e um conjunto de dados para esse arquivo de dados.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-dataflow.md)]

No exemplo na secção anterior, o tipo do conjunto de dados está definido como **AzureSqlTable**. Da mesma forma, para um conjunto de dados de Blobs do Azure, o tipo do conjunto de dados está definido como **AzureBlob**, conforme mostrado no seguinte JSON:

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

## <a name="dataset-structure-or-schema"></a>Estrutura do conjunto de dados ou esquema
O **estrutura** secção ou **esquema** (fluxo de dados compatível) conjuntos de dados de secção é opcional. Define o esquema do conjunto de dados, que contém uma coleção de nomes e tipos de dados das colunas. Utilize a secção de estrutura para fornecer informações de tipo, que são utilizadas para converter tipos e mapear colunas da origem para o destino.

Cada coluna na estrutura contém as seguintes propriedades:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
nome | Nome da coluna. | Sim
tipo | Tipo de dados da coluna. Data Factory suporta os seguintes tipos de dados intermediárias, como os valores permitidos: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], booleano, cadeia de caracteres, Guid, Datetime, Datetimeoffset e período de tempo** | Não
cultura | . Com base em NET cultura a ser utilizado quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. | Não
Formato | Formatar a cadeia de caracteres a ser utilizado quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. Consulte a [data de personalizado e cadeias de caracteres de formato de tempo](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar datetime. | Não

### <a name="example"></a>Exemplo
No exemplo a seguir, vamos supor que a origem de dados de Blob está num formato CSV e contém três colunas: ID de utilizador, nome e lastlogindate. Eles são do tipo Int64, cadeia e Datetime com um formato de datetime personalizadas com nomes abreviados de francês para o dia da semana.

Defina a estrutura do conjunto de dados de Blob da seguinte forma, juntamente com definições de tipo para as colunas:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Orientação

As seguintes diretrizes ajudá-lo a compreender quando deve incluir informações sobre a estrutura e o que pretende incluir no **estrutura** secção. Saiba mais sobre como a fábrica de dados mapeia os dados de origem para o sink e quando especificar informações sobre a estrutura da [esquema e o mapeamento do tipo](copy-activity-schema-and-type-mapping.md).

- **Para origens de dados de esquema forte**, especifique a seção de estrutura, se pretender mapear colunas de origem para colunas de sink e seus nomes não são iguais. Esse tipo de origem de dados estruturados armazena informações de esquema e o tipo de dados, juntamente com os dados propriamente ditos. Exemplos de origens de dados estruturados incluem o SQL Server, Oracle e SQL Database do Azure.<br/><br/>Como as informações de tipo já estão disponíveis para origens de dados estruturados, não deve incluir informações sobre o tipo ao incluir a secção de estrutura.
- **Esquema não/weak por exemplo, ficheiro de texto no armazenamento de BLOBs de origens de dados**, incluir estrutura quando o conjunto de dados é uma entrada para uma atividade de cópia e tipos de dados do conjunto de dados de origem devem ser convertidos em tipos nativos para o sink. E incluir estrutura quando pretende mapear colunas de origem para o sink de colunas....

## <a name="create-datasets"></a>Criar conjuntos de dados
Pode criar conjuntos de dados ao utilizar uma destas ferramentas ou SDKs: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), modelo do Azure Resource Manager e o portal do Azure

## <a name="current-version-vs-version-1-datasets"></a>Versão atual do vs. conjuntos de dados de versão 1

Aqui estão algumas diferenças entre a fábrica de dados e conjuntos de dados do Data Factory versão 1:

- A propriedade externa não é suportada na versão atual. Ele é substituído por um [acionador](concepts-pipeline-execution-triggers.md).
- As propriedades de política e a disponibilidade não são suportadas na versão atual. A hora de início para um pipeline depende [acionadores](concepts-pipeline-execution-triggers.md).
- Conjuntos de dados de âmbito (conjuntos de dados definidos num pipeline) não são suportados na versão atual.

## <a name="next-steps"></a>Passos Seguintes
Veja o tutorial seguinte para obter instruções passo a passo para a criação de pipelines e conjuntos de dados ao utilizar uma destas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Início rápido: criar uma fábrica de dados com o PowerShell](quickstart-create-data-factory-powershell.md)
- [Início rápido: criar uma fábrica de dados com a REST API](quickstart-create-data-factory-rest-api.md)
- [Início rápido: criar uma fábrica de dados com o portal do Azure](quickstart-create-data-factory-portal.md)
