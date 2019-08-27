---
title: Conjuntos de valores em Azure Data Factory | Microsoft Docs
description: Saiba mais sobre conjuntos de os Data Factory. DataSets representam dados de entrada/saída.
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
ms.openlocfilehash: bbf7159abf88ce70cc62d202a8375aad302a0552
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019963"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de valores no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-datasets-linked-services.md)

Este artigo descreve o que são conjuntos de valores, como eles são definidos no formato JSON e como eles são usados em pipelines de Azure Data Factory.

Se você for novo no Data Factory, consulte [introdução ao Azure data Factory](introduction.md) para obter uma visão geral.

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **pipeline** é um agrupamento lógico de **atividades** que juntos executam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Agora, um **DataSet** é uma exibição nomeada de dados que simplesmente aponta ou faz referência aos dados que você deseja usar em suas **atividades** como entradas e saídas. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de Blobs do Azure especifica o contentor de blobs e a pasta no armazenamento de Blobs a partir dos quais a atividade deve ler os dados.

Antes de criar um conjunto de dados, você deve criar um [**serviço vinculado**](concepts-linked-services.md) para vincular seu armazenamento de data ao data Factory. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Imagine dessa forma; o DataSet representa a estrutura dos dados dentro dos armazenamentos de dados vinculados e o serviço vinculado define a conexão com a fonte de dados. Por exemplo, um serviço vinculado do armazenamento do Azure vincula uma conta de armazenamento ao data factory. Um conjunto de dados de blob do Azure representa o contêiner de BLOB e a pasta dentro dessa conta de armazenamento do Azure que contém os blobs de entrada a serem processados.

Aqui está um cenário de exemplo. Para copiar dados de um armazenamento de BLOBs para um SQL Database, você cria dois serviços vinculados: Armazenamento do Azure e banco de dados SQL do Azure. Em seguida, crie dois conjuntos de valores: Conjunto de dados de BLOBs do Azure (que se refere ao serviço vinculado do armazenamento do Azure) e ao DataSet da tabela SQL do Azure (que se refere ao serviço vinculado do banco de dados SQL do Azure). Os serviços vinculados de armazenamento do Azure e banco de dados SQL do Azure contêm cadeias de conexão que Data Factory usa em tempo de execução para se conectar ao armazenamento do Azure e ao banco de dados SQL do Azure, respectivamente O conjunto de dados de blob do Azure especifica o contêiner de BLOB e a pasta de BLOB que contém os blobs de entrada no armazenamento de BLOBs. O conjunto de dados da tabela SQL do Azure especifica a tabela SQL em seu banco de dado SQL para a qual os dados serão copiados.

O diagrama a seguir mostra as relações entre pipeline, atividade, conjunto de serviços e serviço vinculado no Data Factory:

![Relação entre pipeline, Activity, DataSet, serviços vinculados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>DataSet JSON
Um conjunto de Data Factory é definido no seguinte formato JSON:

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
A tabela a seguir descreve as propriedades no JSON acima:

Propriedade | Descrição | Requerido |
-------- | ----------- | -------- |
name | Nome do conjunto de uma. Consulte [regras de nomenclatura de Azure data Factory](naming-rules.md). |  Sim |
type | Tipo do conjunto de um. Especifique um dos tipos com suporte pelo Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter detalhes, consulte [tipos de conjunto](#dataset-type)de informações. | Sim |
structure | Esquema do conjunto de um. Para obter detalhes, consulte [esquema de conjunto](#dataset-structure-or-schema)de informações. | Não |
typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: Blob do Azure, tabela SQL do Azure). Para obter detalhes sobre os tipos com suporte e suas propriedades, consulte [tipo de conjunto](#dataset-type)de informações. | Sim |

### <a name="data-flow-compatible-dataset"></a>DataSet compatível com fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Consulte [tipos de conjunto de dados com suporte](#dataset-type) para obter uma lista de tipos de conjuntos de [dados](concepts-data-flow-overview.md) compatíveis. Os conjuntos de dados que são compatíveis com o fluxo de data exigem definições de conjunto de dado refinadas para transformações. Portanto, a definição de JSON é ligeiramente diferente. Em vez de uma propriedade de _estrutura_ , os conjuntos de dados que são compatíveis com o data Flow têm uma propriedade de _esquema_ .

No fluxo de dados, DataSets são usados em transformações de origem e de coletor. Os conjuntos de dados definem os esquemas básicos. Se os dados não tiverem um esquema, você poderá usar descompasso de esquema para a origem e o coletor. O esquema no DataSet representa o tipo de dados físico e a forma.

Ao definir o esquema a partir do conjunto de dados, você obterá os tipos, os formatos de dados, o local do arquivo e as informações de conexão relacionados do serviço vinculado associado. Os metadados dos conjuntos de valores são exibidos na transformação de origem comoa projeção de origem. A projeção na transformação origem representa os dados de fluxo de dados com nomes e tipos definidos.

Quando você importa o esquema de um conjunto de dados de fluxo, seleciona o botão **importar esquema** e opta por importar da origem ou de um arquivo local. Na maioria dos casos, você importará o esquema diretamente da origem. Mas se você já tiver um arquivo de esquema local (um arquivo parquet ou CSV com cabeçalhos), poderá direcionar Data Factory para basear o esquema nesse arquivo.


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

A tabela a seguir descreve as propriedades no JSON acima:

Propriedade | Descrição | Requerido |
-------- | ----------- | -------- |
name | Nome do conjunto de uma. Consulte [regras de nomenclatura de Azure data Factory](naming-rules.md). |  Sim |
type | Tipo do conjunto de um. Especifique um dos tipos com suporte pelo Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter detalhes, consulte [tipos de conjunto](#dataset-type)de informações. | Sim |
esquema | Esquema do conjunto de um. Para obter detalhes, consulte [conjuntos de dados compatíveis com o data Flow](#dataset-type). | Não |
typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: Blob do Azure, tabela SQL do Azure). Para obter detalhes sobre os tipos com suporte e suas propriedades, consulte [tipo de conjunto](#dataset-type)de informações. | Sim |


## <a name="dataset-example"></a>Exemplo de DataSet
No exemplo a seguir, o DataSet representa uma tabela chamada MyTable em um banco de dados SQL.

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

- Type é definido como AzureSqlTable.
- a propriedade do tipo TableName (específica ao tipo AzureSqlTable) é definida como MyTable.
- linkedServiceName refere-se a um serviço vinculado do tipo AzureSqlDatabase, que é definido no próximo trecho de JSON.

## <a name="dataset-type"></a>Tipo de conjunto de texto
Há muitos tipos diferentes de conjuntos de dados, dependendo do armazenamento de dados que você usa. Consulte a tabela a seguir para obter uma lista de armazenamentos de dados com suporte pelo Data Factory. Clique em um armazenamento de dados para saber como criar um serviço vinculado e um conjunto de dados para esse armazenamento de dado.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/connector-activity-support-matrix.md)]

No exemplo na seção anterior, o tipo do conjunto de os é definido como **AzureSqlTable**. Da mesma forma, para um conjunto de um DataSet do Azure, o tipo do conjunto de um é definido como **AzureBlob**, conforme mostrado no JSON a seguir:

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
A seção de **estrutura** ou os conjuntos de dados da seção de **esquema** (compatível com o fluxo) são opcionais. Ele define o esquema do conjunto de dados, contendo uma coleção de nomes e tipos de dado de colunas. Use a seção estrutura para fornecer informações de tipo que são usadas para converter tipos e mapear colunas da origem para o destino.

Cada coluna na estrutura contém as seguintes propriedades:

Propriedade | Descrição | Requerido
-------- | ----------- | --------
name | Nome da coluna. | Sim
type | Tipo de dados da coluna. Data Factory dá suporte aos seguintes tipos de dados provisórios como valores permitidos: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], Boolean, String, GUID, DateTime, DateTimeOffset e TimeSpan** | Não
culture | . Cultura baseada em rede a ser usada quando o tipo é um tipo .net: `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. | Não
format | Cadeia de caracteres de formato a ser usada quando o tipo é um `Datetime` tipo `Datetimeoffset`.net: ou. Consulte cadeias de [caracteres de formato personalizado de data e hora](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar DateTime. | Não

### <a name="example"></a>Exemplo
No exemplo a seguir, suponha que os dados de blob de origem estejam no formato CSV e contenham três colunas: userid, Name e LastLoginDate. Eles são do tipo Int64, String e DateTime com um formato de data e hora personalizado usando nomes abreviados em francês para o dia da semana.

Defina a estrutura do conjunto de dados de BLOB da seguinte maneira com definições de tipo para as colunas:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Orientação

As diretrizes a seguir ajudam você a entender quando incluir informações de estrutura e o que incluir na seção de **estrutura** . Saiba mais sobre como o data factory mapeia os dados de origem para o coletor e quando especificar informações de estrutura do [esquema e do mapeamento de tipo](copy-activity-schema-and-type-mapping.md).

- **Para fontes de dados de esquema fortes**, especifique a seção de estrutura somente se desejar mapear colunas de origem para colunas do coletor e seus nomes não forem iguais. Esse tipo de fonte de dados estruturado armazena informações de tipo e esquema de dados juntamente com os dados em si. Exemplos de fontes de dados estruturadas incluem SQL Server, Oracle e banco de dado SQL do Azure.<br/><br/>Como as informações de tipo já estão disponíveis para fontes de dados estruturadas, você não deve incluir informações de tipo ao incluir a seção de estrutura.
- **Para fontes de dados de esquema não fracas, por exemplo, arquivo de texto no armazenamento**de BLOBs, inclua a estrutura quando o conjunto de dados for uma entrada para uma atividade de cópia, e os tipos de dado do conjunto de dados de origem devem ser convertidos em tipos nativos para o coletor. E inclua a estrutura quando desejar mapear colunas de origem para colunas do coletor.

## <a name="create-datasets"></a>Criar conjuntos de dados
Você pode criar conjuntos de valores usando uma destas ferramentas ou SDKs: [API .net](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), Azure Resource Manager modelo e portal do Azure

## <a name="current-version-vs-version-1-datasets"></a>Versão atual versus conjuntos de valores da versão 1

Aqui estão algumas diferenças entre Data Factory e os conjuntos de Data Factory da versão 1:

- A propriedade externa não tem suporte na versão atual. Ele é substituído por um [gatilho](concepts-pipeline-execution-triggers.md).
- A política e as propriedades de disponibilidade não têm suporte na versão atual. A hora de início de um pipeline depende [](concepts-pipeline-execution-triggers.md)dos gatilhos.
- Os conjuntos de valores com escopo (conjuntos de valores definidos em um pipeline) não têm suporte na versão atual.

## <a name="next-steps"></a>Passos seguintes
Consulte o tutorial a seguir para obter instruções passo a passo para criar pipelines e conjuntos de valores usando uma dessas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Início rápido: criar um data factory usando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Início rápido: criar um data factory usando a API REST](quickstart-create-data-factory-rest-api.md)
- [Início rápido: criar um data factory usando portal do Azure](quickstart-create-data-factory-portal.md)
