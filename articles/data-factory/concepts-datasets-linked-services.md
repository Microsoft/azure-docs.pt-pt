---
title: Conjuntos de Dados
description: Saiba mais sobre conjuntos de dados na Data Factory. Os conjuntos de dados representam dados de entrada/saída.
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
ms.openlocfilehash: 122725bff616a49d27981b88f465e04418db9526
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83826117"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Este artigo descreve quais são os conjuntos de dados, como são definidos no formato JSON e como são usados nos oleodutos Azure Data Factory.

Se é novo na Data Factory, consulte [a Introdução à Fábrica de Dados Azure](introduction.md) para uma visão geral.

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **oleoduto** é um agrupamento lógico de **atividades** que juntos desempenham uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Agora, um conjunto de **dados** é uma visão nomeada de dados que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas **atividades** como entradas e saídas. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de Blobs do Azure especifica o contentor de blobs e a pasta no armazenamento de Blobs a partir dos quais a atividade deve ler os dados.

Antes de criar um conjunto de dados, tem de criar um [**serviço ligado**](concepts-linked-services.md) para ligar a sua loja de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense desta forma; o conjunto de dados representa a estrutura dos dados dentro das lojas de dados ligadas, e o serviço ligado define a ligação à fonte de dados. Por exemplo, um serviço ligado ao Azure Storage liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados Azure Blob representa o recipiente blob e a pasta dentro dessa conta de Armazenamento Azure que contém as bolhas de entrada a serem processadas.

Aqui está um cenário de amostra. Para copiar dados do armazenamento blob para uma Base de Dados SQL, cria dois serviços ligados: Azure Storage e Azure SQL Database. Em seguida, crie dois conjuntos de dados: conjunto de dados Azure Blob (que se refere ao serviço ligado ao Armazenamento Azure) e conjunto de dados de tabela Azure SQL (que se refere ao serviço ligado à Base de Dados Azure SQL). Os serviços ligados ao Azure Storage e Azure SQL Database contêm cadeias de ligação que a Data Factory utiliza em tempo de execução para ligar à sua Base de Dados Azure Storage e Azure SQL, respectivamente. O conjunto de dados Azure Blob especifica o recipiente blob e a pasta blob que contém as bolhas de entrada no seu armazenamento Blob. O conjunto de dados da tabela Azure SQL especifica a tabela SQL na sua Base de Dados SQL para a qual os dados devem ser copiados.

O diagrama a seguir mostra as relações entre o pipeline, a atividade, o conjunto de dados e o serviço ligado na Data Factory:

![Relação entre pipeline, atividade, conjunto de dados, serviços ligados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Conjunto de dados JSON
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
A tabela a seguir descreve propriedades no JSON acima:

Propriedade | Descrição | Necessário |
-------- | ----------- | -------- |
name | Nome do conjunto de dados. Ver [Azure Data Factory - Regras de nomeação](naming-rules.md). |  Sim |
tipo | Tipo do conjunto de dados. Especifique um dos tipos suportados pela Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para mais informações, consulte os [tipos de Dataset](#dataset-type). | Sim |
estrutura | Esquema do conjunto de dados. Para mais informações, consulte [o esquema do Dataset](#dataset-structure-or-schema). | Não |
typeProperties | As propriedades do tipo são diferentes para cada tipo (por exemplo: Azure Blob, tabela Azure SQL). Para obter mais informações sobre os tipos suportados e as suas propriedades, consulte [o tipo Dataset](#dataset-type). | Sim |

### <a name="data-flow-compatible-dataset"></a>Conjunto de dados compatíveis com fluxo de dados



Consulte [os tipos de conjuntos de dados suportados](#dataset-type) para uma lista de tipos de conjuntos de dados compatíveis com o Fluxo de [Dados.](concepts-data-flow-overview.md) Conjuntos de dados compatíveis com o Data Flow requerem definições de conjunto de dados de grãos finos para transformações. Assim, a definição JSON é ligeiramente diferente. Em vez de uma propriedade _de estrutura,_ os conjuntos de dados que são compatíveis com o Fluxo de Dados têm uma propriedade _de esquema._

No Data Flow, os conjuntos de dados são utilizados nas transformações de origem e afundanço. Os conjuntos de dados definem os esquemas básicos de dados. Se os seus dados não ílmulas, pode utilizar o esquema para a sua fonte e afundar. O esquema no conjunto de dados representa o tipo e a forma de dados físicos.

Ao definir o esquema a partir do conjunto de dados, obterá os tipos de dados relacionados, formatos de dados, localização de ficheiros e informações de conexão a partir do serviço Linked associado. Os metadados dos conjuntos de dados aparecem na sua transformação de origem como projeção de *origem*. A projeção na transformação da fonte representa os dados do Fluxo de Dados com nomes e tipos definidos.

Quando importar o esquema de um conjunto de dados do Data Flow, selecione o botão **Descoduma** de Importação e opte por importar da fonte ou de um ficheiro local. Na maioria dos casos, importará o esquema diretamente da fonte. Mas se já tiver um ficheiro de esquema local (um ficheiro Parquet ou CSV com cabeçalhos), pode direcionar a Data Factory para basear o esquema nesse ficheiro.


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

A tabela a seguir descreve propriedades no JSON acima:

Propriedade | Descrição | Necessário |
-------- | ----------- | -------- |
name | Nome do conjunto de dados. Ver [Azure Data Factory - Regras de nomeação](naming-rules.md). |  Sim |
tipo | Tipo do conjunto de dados. Especifique um dos tipos suportados pela Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para mais informações, consulte os [tipos de Dataset](#dataset-type). | Sim |
esquema | Esquema do conjunto de dados. Para mais informações, consulte [conjuntos de dados compatíveis com o Fluxo de Dados](#dataset-type). | Não |
typeProperties | As propriedades do tipo são diferentes para cada tipo (por exemplo: Azure Blob, tabela Azure SQL). Para obter mais informações sobre os tipos suportados e as suas propriedades, consulte [o tipo Dataset](#dataset-type). | Sim |


## <a name="dataset-example"></a>Exemplo do conjunto de dados
No exemplo seguinte, o conjunto de dados representa uma tabela chamada MyTable numa Base de Dados SQL.

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

- tipo é definido para AzureSqlTable.
- tableName tipo propriedade (específica do tipo AzureSqlTable) é definida para MyTable.
- linkedServiceName refere-se a um serviço ligado do tipo AzureSqlDatabase, que é definido no próximo snippet JSON.

## <a name="dataset-type"></a>Tipo de conjunto de dados
Existem muitos tipos diferentes de conjuntos de dados, dependendo da loja de dados que utiliza. Pode encontrar a lista de dados armazenados suportados pela Data Factory a partir do artigo de visão geral do [Connector.](connector-overview.md) Clique numa loja de dados para saber como criar um serviço ligado e um conjunto de dados para essa loja de dados.

No exemplo da secção anterior, o tipo de conjunto de dados é definido para **AzureSqlTable**. Da mesma forma, para um conjunto de dados Azure Blob, o tipo de conjunto de dados é definido para **AzureBlob,** como mostrado no seguinte JSON:

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
A secção de **estrutura** ou **conjuntos de** dados de secção (compatível com fluxo de dados) é opcional. Define o esquema do conjunto de dados contendo uma recolha de nomes e tipos de dados de colunas. Utiliza a secção de estrutura para fornecer informações de tipo que são usadas para converter tipos e colunas de mapas da fonte para o destino.

Cada coluna da estrutura contém as seguintes propriedades:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
name | O nome da coluna. | Sim
tipo | Tipo de dados da coluna. Data Factory suporta os seguintes tipos de dados provisórios como valores permitidos: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetimeset, e Timespan** | Não
cultura | . Cultura baseada na NET a ser utilizada quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset` . A predefinição é `en-us`. | Não
formato | Cadeia de formato a utilizar quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset` . Consulte as [cordas de data e formato de hora personalizadas](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar a data. | Não

### <a name="example"></a>Exemplo
No exemplo seguinte, suponha que os dados blob de origem estão no formato CSV e contém três colunas: userid, name e lastlogindate. São do tipo Int64, String e Datetime com um formato de data-hora personalizado usando nomes franceses abreviados para o dia da semana.

Defina a estrutura do conjunto de dados Blob da seguinte forma, juntamente com definições de tipo para as colunas:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Orientação

As seguintes diretrizes ajudam-no a compreender quando deve incluir informações de estrutura e o que incluir na secção de **estrutura.** Saiba mais sobre como a fábrica de dados mapeia dados de origem para afundar e quando especificar informações de estrutura a partir de [Schema e mapeamento de tipo](copy-activity-schema-and-type-mapping.md).

- **Para fontes de dados de esquemas fortes**, especifique a secção de estrutura apenas se quiser que as colunas de origem do mapa afundem colunas, e os seus nomes não são os mesmos. Este tipo de fonte de dados estruturada armazena esquema de dados e informação de tipo, juntamente com os próprios dados. Exemplos de fontes de dados estruturadas incluem SQL Server, Oracle e Azure SQL Database.<br/><br/>Uma vez que a informação do tipo já está disponível para fontes de dados estruturadas, não deve incluir informações do tipo quando incluir a secção de estrutura.
- **Para fontes de dados de esquemas não/fracos, por exemplo, o ficheiro de texto no armazenamento de bolhas,** inclua a estrutura quando o conjunto de dados é uma entrada para uma atividade de cópia, e os tipos de dados de dados de origem devem ser convertidos para tipos nativos para o lavatório. E inclua estrutura quando quiser mapear colunas de origem para afundar colunas

## <a name="create-datasets"></a>Criar conjuntos de dados
Pode criar conjuntos de dados utilizando uma destas ferramentas ou SDKs: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Modelo de Gestor de Recursos Azure e Portal Azure

## <a name="current-version-vs-version-1-datasets"></a>Versão atual vs. conjuntos de dados da versão 1

Aqui ficam algumas diferenças entre data factory e data factory versão 1 conjuntos de dados:

- A propriedade externa não é suportada na versão atual. É substituído por um [gatilho.](concepts-pipeline-execution-triggers.md)
- As propriedades de política e disponibilidade não são suportadas na versão atual. A hora de início de um gasoduto depende dos [gatilhos](concepts-pipeline-execution-triggers.md).
- Os conjuntos de dados (conjuntos de dados definidos num pipeline) não são suportados na versão atual.

## <a name="next-steps"></a>Próximos passos
Consulte o seguinte tutorial para obter instruções passo a passo para a criação de oleodutos e conjuntos de dados utilizando uma destas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Quickstart: criar uma fábrica de dados utilizando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Quickstart: criar uma fábrica de dados utilizando a REST API](quickstart-create-data-factory-rest-api.md)
- [Quickstart: criar uma fábrica de dados utilizando o portal Azure](quickstart-create-data-factory-portal.md)
