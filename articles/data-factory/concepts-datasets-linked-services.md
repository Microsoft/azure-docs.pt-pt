---
title: Conjuntos de dados
description: Saiba mais sobre conjuntos de dados na Data Factory. Os conjuntos de dados representam dados de entrada/saída.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 4dafb738fd7d44893705994baa962831c101804b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390033"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Este artigo descreve quais são os conjuntos de dados, como são definidos no formato JSON e como são usados nos oleodutos Azure Data Factory.

Se é novo na Data Factory, consulte [a Introdução à Fábrica de Dados Azure](introduction.md) para uma visão geral.

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **oleoduto** é um agrupamento lógico de **atividades** que juntos desempenham uma tarefa. As atividades num pipeline definem as ações a executar nos seus dados. Agora, um conjunto de **dados** é uma visão nomeada de dados que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas **atividades** como entradas e saídas. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de Blobs do Azure especifica o contentor de blobs e a pasta no armazenamento de Blobs a partir dos quais a atividade deve ler os dados.

Antes de criar um conjunto de dados, tem de criar um [**serviço ligado**](concepts-linked-services.md) para ligar a sua loja de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense desta forma; o conjunto de dados representa a estrutura dos dados dentro das lojas de dados ligadas, e o serviço ligado define a ligação à fonte de dados. Por exemplo, um serviço ligado ao Azure Storage liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados Azure Blob representa o recipiente blob e a pasta dentro dessa conta de Armazenamento Azure que contém as bolhas de entrada a serem processadas.

Aqui está um cenário de amostra. Para copiar dados do armazenamento blob para uma Base de Dados SQL, cria dois serviços ligados: Azure Blob Storage e Azure SQL Database. Em seguida, crie dois conjuntos de dados: Conjunto de dados de textolimtado (que se refere ao serviço ligado ao armazenamento Azure Blob, assumindo que tem ficheiros de texto como fonte) e conjunto de dados de tabelaS Azure SQL (que se refere ao serviço ligado à Base de Dados Azure SQL). Os serviços ligados ao Azure Blob Storage e Azure SQL Database contêm cadeias de ligação que a Data Factory utiliza em tempo de execução para se ligarem ao seu Azure Storage e À Azure SQL Database, respectivamente. O conjunto de dados de texto delimitado especifica o recipiente blob e a pasta blob que contém as bolhas de entrada no seu armazenamento Blob, juntamente com as definições relacionadas com o formato. O conjunto de dados da tabela Azure SQL especifica a tabela SQL na sua Base de Dados SQL para a qual os dados devem ser copiados.

O diagrama a seguir mostra as relações entre o pipeline, a atividade, o conjunto de dados e o serviço ligado na Data Factory:

![Relação entre pipeline, atividade, conjunto de dados, serviços ligados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Conjunto de dados JSON
Um conjunto de dados na Data Factory é definido no seguinte formato JSON:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

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
name | Nome do conjunto de dados. Ver [Azure Data Factory - Regras de nomeação](naming-rules.md). |  Yes |
tipo | Tipo do conjunto de dados. Especifique um dos tipos suportados pela Data Factory (por exemplo: DelimitedText, AzureSqlTable). <br/><br/>Para mais informações, consulte os [tipos de Dataset](#dataset-type). | Yes |
esquema | O esquema do conjunto de dados representa o tipo e a forma de dados físicos. | No |
typeProperties | As propriedades do tipo são diferentes para cada tipo. Para obter mais informações sobre os tipos suportados e as suas propriedades, consulte [o tipo Dataset](#dataset-type). | Yes |

Quando importar o esquema do conjunto de dados, selecione o botão **Descoduma** de Importação e opte por importar da fonte ou de um ficheiro local. Na maioria dos casos, importará o esquema diretamente da fonte. Mas se já tiver um ficheiro de esquema local (um ficheiro Parquet ou CSV com cabeçalhos), pode direcionar a Data Factory para basear o esquema nesse ficheiro.

Na atividade de cópias, os conjuntos de dados são utilizados na fonte e na pia. O esquema definido no conjunto de dados é opcional como referência. Se pretender aplicar mapeamento de coluna/campo entre a fonte e a pia, consulte [o Schema e o mapeamento do tipo](copy-activity-schema-and-type-mapping.md).

No Data Flow, os conjuntos de dados são utilizados nas transformações de origem e afundanço. Os conjuntos de dados definem os esquemas básicos de dados. Se os seus dados não ílmulas, pode utilizar o esquema para a sua fonte e afundar. Os metadados dos conjuntos de dados aparecem na sua transformação de origem como projeção de origem. A projeção na transformação da fonte representa os dados do Fluxo de Dados com nomes e tipos definidos.

## <a name="dataset-type"></a>Tipo de conjunto de dados

A Azure Data Factory suporta vários tipos de conjuntos de dados, dependendo das lojas de dados que utiliza. Pode encontrar a lista de lojas de dados suportadas pela Data Factory a partir do artigo de visão geral do [Connector.](connector-overview.md) Clique numa loja de dados para saber como criar um serviço ligado e um conjunto de dados para o mesmo.

Por exemplo, para um conjunto de dados de texto delimitado, o tipo de conjunto **de** dados é definido como mostrado na amostra JSON seguinte:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>Criar conjuntos de dados
Pode criar conjuntos de dados utilizando uma destas ferramentas ou SDKs: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Modelo de Gestor de Recursos Azure e Portal Azure

## <a name="current-version-vs-version-1-datasets"></a>Versão atual vs. conjuntos de dados da versão 1

Aqui ficam algumas diferenças entre data factory e data factory versão 1 conjuntos de dados:

- A propriedade externa não é suportada na versão atual. É substituído por um [gatilho.](concepts-pipeline-execution-triggers.md)
- As propriedades de política e disponibilidade não são suportadas na versão atual. A hora de início de um gasoduto depende dos [gatilhos](concepts-pipeline-execution-triggers.md).
- Os conjuntos de dados (conjuntos de dados definidos num pipeline) não são suportados na versão atual.

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte tutorial para obter instruções passo a passo para a criação de oleodutos e conjuntos de dados utilizando uma destas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Quickstart: criar uma fábrica de dados utilizando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Quickstart: criar uma fábrica de dados utilizando a REST API](quickstart-create-data-factory-rest-api.md)
- [Quickstart: criar uma fábrica de dados utilizando o portal Azure](quickstart-create-data-factory-portal.md)
