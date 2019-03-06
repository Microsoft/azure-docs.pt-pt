---
title: Criar conjuntos de dados no Azure Data Factory | Documentos da Microsoft
description: Saiba como criar conjuntos de dados no Azure Data Factory, com exemplos que utilizam propriedades como offset e anchorDateTime.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 6b16b6c4de8c8d2d7a821dd476f07c8ab1135408
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433438"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de dados no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-create-datasets.md)
> * [Versão 2 (versão atual)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conjuntos de dados no V2](../concepts-datasets-linked-services.md).

Este artigo descreve os conjuntos de dados são, como são definidas no formato JSON, e como são utilizados no Azure Data Factory pipelines. Fornece detalhes sobre cada seção (por exemplo, estrutura, disponibilidade e a política) na definição de JSON do conjunto de dados. O artigo também fornece exemplos de utilização a **desvio**, **anchorDateTime**, e **estilo** propriedades numa definição de JSON do conjunto de dados.

> [!NOTE]
> Se estiver familiarizado com o Data Factory, veja [introdução ao Azure Data Factory](data-factory-introduction.md) para uma descrição geral. Se não tiver experiência prática com a criação de fábricas de dados, pode obter uma melhor compreensão, lendo o [tutorial de transformação de dados](data-factory-build-your-first-pipeline.md) e o [tutorial de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. R **pipeline** é um agrupamento lógico de **atividades** que em conjunto, realizam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um servidor de SQL no local para o armazenamento de Blobs do Azure. Em seguida, poderá utilizar uma atividade do Hive que executa um script do Hive num cluster do Azure HDInsight para processar dados de armazenamento de BLOBs para produzir os dados de saída. Por fim, pode usar uma segunda atividade de cópia para copiar os dados de saída para o Azure SQL Data Warehouse, sobre o relatórios de business intelligence (BI) soluções foram concebidas. Para obter mais informações sobre os pipelines e atividades, consulte [Pipelines e atividades no Azure Data Factory](data-factory-create-pipelines.md).

Uma atividade pode ter zero ou mais entradas **conjuntos de dados**e produzem um ou mais conjuntos de dados de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída para a atividade. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de Blobs do Azure Especifica o contentor de BLOBs e a pasta no armazenamento de BLOBs a partir do qual o pipeline deve ler os dados.

Antes de criar um conjunto de dados, criar um **serviço ligado** para ligar o seu armazenamento de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Conjuntos de dados identificam dados dentro dos arquivos de dados ligados, como tabelas SQL, arquivos, pastas e documentos. Por exemplo, um armazenamento do Azure ligado serviço liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados de Blobs do Azure representa o contentor de BLOBs e a pasta que contém os blobs de entrada para serem processados.

Eis um cenário de exemplo. Para copiar dados do armazenamento de BLOBs para base de dados SQL, criar dois serviços ligados: Armazenamento do Azure e Azure base de dados SQL. Em seguida, crie dois conjuntos de dados: Conjunto de dados Blob do Azure (que se refere ao serviço ligado do armazenamento do Azure) e o conjunto de dados de tabela SQL do Azure (o que se refere-se para o serviço de base de dados do SQL do Azure ligada). O armazenamento do Azure e serviços de base de dados do SQL Azure ligado contenham cadeias de ligação que o Data Factory utiliza no tempo de execução para ligar para o armazenamento do Azure e a base de dados SQL do Azure, respectivamente. O conjunto de dados de Blobs do Azure Especifica o contentor de BLOBs e a pasta de BLOBs que contém os blobs de entrada no armazenamento de Blobs. O conjunto de dados de tabela SQL do Azure Especifica a tabela SQL na base de dados SQL para o qual os dados estão a ser copiado.

O diagrama seguinte mostra as relações entre o pipeline, atividade, conjunto de dados e serviço ligado no Data Factory:

![Relação entre o pipeline, atividade, conjunto de dados, serviços ligados](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Conjunto de dados JSON
Um conjunto de dados no Data Factory é definido no formato JSON, da seguinte forma:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

A tabela seguinte descreve as propriedades no JSON acima:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| nome |Nome do conjunto de dados. Ver [do Azure Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura. |Sim |ND |
| tipo |Tipo de conjunto de dados. Especifique um dos tipos suportados pela fábrica de dados (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter detalhes, consulte [tipo de conjunto de dados](#Type). |Sim |ND |
| estrutura |Esquema do conjunto de dados.<br/><br/>Para obter detalhes, consulte [estrutura do conjunto de dados](#Structure). |Não |ND |
| typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: O Azure Blob, tabela SQL do Azure). Para obter detalhes sobre os tipos suportados e as respetivas propriedades, consulte [tipo de conjunto de dados](#Type). |Sim |ND |
| externo | Sinalizador booleano para especificar se um conjunto de dados é produzido explicitamente por um pipeline de fábrica de dados ou não. Se o conjunto de dados de entrada para uma atividade não é produzido pelo pipeline atual, defina este sinalizador como true. Defina este sinalizador como true para o conjunto de dados de entrada da primeira atividade no pipeline.  |Não |false |
| disponibilidade | Define o período de processamento (por exemplo, hora ou diária) ou o modelo slicing para o conjunto de dados de produção. Cada unidade de dados consumidos e produzidos por uma execução de atividade é chamada de um setor de dados. Se a disponibilidade de um conjunto de dados de saída está definida como diariamente (frequência - dia, o intervalo de-1), diariamente é produzido um setor. <br/><br/>Para obter detalhes, consulte o conjunto de dados disponibilidade. <br/><br/>Para obter detalhes sobre o modelo de fragmentação do conjunto de dados, consulte a [agendamento e execução](data-factory-scheduling-and-execution.md) artigo. |Sim |ND |
| política |Define os critérios ou a condição que tem de preencher os setores do conjunto de dados. <br/><br/>Para obter detalhes, consulte a [política de conjunto de dados](#Policy) secção. |Não |ND |

## <a name="dataset-example"></a>Exemplo de conjunto de dados
No exemplo seguinte, o conjunto de dados representa uma tabela chamada **MyTable** numa base de dados SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Tenha em atenção os seguintes pontos:

* **tipo de** está definido como AzureSqlTable.
* **tableName** propriedade de tipo (específicas ao tipo de AzureSqlTable) está definida como MyTable.
* **linkedServiceName** refere-se a um serviço ligado do tipo AzureSqlDatabase, que é definido no fragmento JSON seguinte.
* **frequência de disponibilidade** está definido para o dia, e **intervalo** está definido como 1. Isso significa que o setor de conjunto de dados é produzido diariamente.

**AzureSqlLinkedService** é definida da seguinte forma:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

No fragmento JSON anterior:

* **tipo de** está definido como AzureSqlDatabase.
* **connectionString** propriedade de tipo Especifica informações para ligar a uma base de dados SQL.

Como pode ver, o serviço ligado define como ligar a uma base de dados SQL. O conjunto de dados define quais tabela é usada como entrada e saída para a atividade num pipeline.

> [!IMPORTANT]
> A menos que um conjunto de dados está a ser produzido pelo pipeline, deverá ser marcado como **externo**. Esta definição aplica-se geralmente a entradas da primeira atividade num pipeline.

## <a name="Type"></a> Tipo de conjunto de dados
O tipo do conjunto de dados depende de usar o arquivo de dados. Consulte a tabela seguinte para obter uma lista dos arquivos de dados suportado pelo Data Factory. Clique num arquivo de dados para saber como criar um serviço ligado e um conjunto de dados para esse arquivo de dados.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Arquivos de dados com * podem estar no local ou na infraestrutura do Azure como um serviço (IaaS). Esses arquivos de dados exigem a instalação [Data Management Gateway](data-factory-data-management-gateway.md).

No exemplo na secção anterior, o tipo do conjunto de dados está definido como **AzureSqlTable**. Da mesma forma, para um conjunto de dados de Blobs do Azure, o tipo do conjunto de dados está definido como **AzureBlob**, conforme mostrado no seguinte JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>Estrutura do conjunto de dados
O **estrutura** secção é opcional. Define o esquema do conjunto de dados, que contém uma coleção de nomes e tipos de dados das colunas. Utilize a secção de estrutura para fornecer informações de tipo, que são utilizadas para converter tipos e mapear colunas da origem para o destino. No exemplo seguinte, o conjunto de dados tem três colunas: `slicetimestamp`, `projectname`, e `pageviews`. Eles são do tipo cadeia, cadeia de caracteres e Decimal, respectivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Cada coluna na estrutura contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna.  |Não |
| cultura |. Com base em NET cultura a ser utilizado quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. |Não |
| Formato |Formatar a cadeia de caracteres a ser utilizado quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset`. |Não |

As seguintes diretrizes ajudá-lo a determinar quando deve incluir informações sobre a estrutura e o que pretende incluir no **estrutura** secção.

* **Para origens de dados estruturados**, especifique a seção de estrutura, se pretender mapear colunas de origem para colunas de sink e seus nomes não são iguais. Esse tipo de origem de dados estruturados armazena informações de esquema e o tipo de dados, juntamente com os dados propriamente ditos. Exemplos de origens de dados estruturados incluem o SQL Server, Oracle e tabelas do Azure.
  
    Como as informações de tipo já estão disponíveis para origens de dados estruturados, não deve incluir informações sobre o tipo ao incluir a secção de estrutura.
* **Para o esquema em origens de dados de leitura (especificamente o armazenamento de BLOBs)**, pode optar por armazenar os dados sem armazenar as informações de esquema ou tipo com os dados. Para estes tipos de origens de dados, inclua estrutura quando pretende mapear colunas de origem para colunas de sink. Também deve inclua estrutura quando o conjunto de dados é uma entrada para uma atividade de cópia e tipos de dados do conjunto de dados de origem devem ser convertidos em tipos nativos para o sink.
    
    Data Factory suporta os seguintes valores para fornecer informações sobre tipos na estrutura: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], booleano, cadeia de caracteres, Guid, Datetime, Datetimeoffset e Timespan**. Estes valores são a especificação de linguagem comum (CLS)-em conformidade,. Valores de tipo com base em NET.

Fábrica de dados executa automaticamente as conversões de tipo ao mover dados de um arquivo de dados de origem para um arquivo de dados de sink.

## <a name="dataset-availability"></a>Disponibilidade do conjunto de dados
O **disponibilidade** secção num conjunto de dados define a janela de processamento (por exemplo, hora a hora, diariamente, ou semanal) para o conjunto de dados. Para obter mais informações sobre janelas de atividade, consulte [agendamento e execução](data-factory-scheduling-and-execution.md).

A secção de disponibilidade seguinte especifica que o conjunto de dados de saída também é produzido de hora a hora ou o conjunto de dados de entrada está disponível uma vez por hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Se o pipeline tem o seguinte de início e de horas de fim:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

O conjunto de dados de saída é produzido de hora a hora no pipeline começar e terminar vezes. Portanto, existem cinco setores do conjunto de dados produzidos por este pipeline, um para cada janela de atividade (12AM - 1 AM, 1AM - 2 AM, 2AM - 3 AM, AM de 3 - 4 AM, 4 AM - 5 AM).

A tabela seguinte descreve as propriedades que pode utilizar a secção de disponibilidade:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para produção do setor de conjunto de dados.<br/><br/><b>Suportado frequência</b>: Minuto, hora, dia, semana, mês |Sim |ND |
| intervalo |Especifica um multiplicador para a frequência.<br/><br/>"Intervalo de frequência x" determina a frequência com que o setor é produzido. Por exemplo, se precisar do conjunto de dados para ser segmentadas numa base horária, defina <b>frequência</b> ao <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/>Tenha em atenção que se especificar **frequência** como **minuto**, deve definir o intervalo não menos do que 15. |Sim |ND |
| Estilo |Especifica se o setor de deve ser produzido no início ou no final do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Se **frequência** está definida como **mês**, e **estilo** está definido como **EndOfInterval**, o setor é produzido no último dia do mês. Se **estilo** está definida como **StartOfInterval**, o setor é produzido no primeiro dia do mês.<br/><br/>Se **frequência** está definida como **dia**, e **estilo** está definido como **EndOfInterval**, o setor é produzido na última hora do dia.<br/><br/>Se **frequência** está definida como **hora**, e **estilo** está definido como **EndOfInterval**, o setor é produzido no fim da hora. Por exemplo, para um setor para o período de 1 PM - 2 PM, o setor é produzido em 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo utilizado pelo scheduler para computar os limites de setor de conjunto de dados. <br/><br/>Tenha em atenção que, se esta propriedade tem partes de data que são mais granulares do que a frequência especificada, as partes mais granulares serão ignoradas. Por exemplo, se o **intervalo** é **por hora** (frequência: hora e intervalo: 1) e o **anchorDateTime** contém **minutos e segundos**, em seguida, as partes de minutos e segundos da **anchorDateTime** são ignorados. |Não |01/01/0001 |
| offset |O período de tempo através do qual o início e de fim de todos os setores do conjunto de dados são mudou. <br/><br/>Observe que, se os dois **anchorDateTime** e **deslocamento** forem especificados, o resultado é a mudança combinada. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por predefinição, diariamente (`"frequency": "Day", "interval": 1`) setores iniciar às 12 AM (meia-noite) Hora Universal Coordenada (UTC). Se pretender que a hora de início para ser o fuso horário UTC 6 da Manhã em vez disso, defina o deslocamento, conforme mostrado no seguinte fragmento:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>exemplo de anchorDateTime
No exemplo a seguir, o conjunto de dados é produzido uma vez a cada 23 horas. O primeiro setor é iniciado no momento especificado por **anchorDateTime**, que é definida como `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>exemplo de desvio/estilo
O seguinte conjunto de dados é mensal e é produzido 3rd de cada mês às 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Política de conjunto de dados
O **política** secção na definição do conjunto de dados define os critérios ou a condição que tem de preencher os setores do conjunto de dados.

### <a name="validation-policies"></a>Políticas de validação
| Nome da política | Descrição | Aplicado a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que os dados no **armazenamento de Blobs do Azure** cumpre os requisitos de tamanho mínimo (em megabytes). |Armazenamento de Blobs do Azure |Não |ND |
| minimumRows |Valida que os dados num **base de dados SQL do Azure** ou uma **tabelas do Azure** contém o número mínimo de linhas. |<ul><li>Base de dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

#### <a name="examples"></a>Exemplos
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Conjuntos de dados externos
Conjuntos de dados externos são aqueles que não são produzidos por uma execução de pipeline na fábrica de dados. Se o conjunto de dados estiver marcado como **externo**, o **ExternalData** política pode ser definida para influenciar o comportamento da disponibilidade do setor de conjunto de dados.

A menos que um conjunto de dados está a ser produzido pela fábrica de dados, deverá ser marcado como **externo**. Esta definição geralmente aplica-se para as entradas da primeira atividade num pipeline, a menos que a atividade ou o encadeamento de pipeline está a ser utilizado.

| Name | Descrição | Necessário | Valor predefinido |
| --- | --- | --- | --- |
| dataDelay |O tempo para atrasar a verificação da disponibilidade dos dados externos para o determinado setor. Por exemplo, pode atrasar uma verificação de hora a hora, utilize esta definição.<br/><br/>A definição só se aplica a hora presente. Por exemplo, caso seja 1 que, neste momento, e este valor é 10 minutos, a validação começa em 1:10 PM.<br/><br/>Tenha em atenção que esta definição não afeta setores no passado. Reparte com **hora de fim do setor** + **dataDelay** < **agora** são processadas sem demora.<br/><br/>Vezes maior do que 23:59 horas devem ser especificadas utilizando o `day.hours:minutes:seconds` formato. Por exemplo, para especificar a 24 horas, a não utilize 24: 00:00. Em alternativa, utilize 1.00:00:00. Se usar 24: 00:00, ela é tratada como 24 dias (24.00:00:00). Para 1 dia e quatro horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre uma falha e da próxima tentativa. Esta definição aplica-se a hora atual. Se o anterior tentar com falha, repita seguinte é depois do **retryInterval** período. <br/><br/>Se for 1 que, neste momento, começamos a primeira tentativa. Se a duração para concluir a primeira verificação de validação é de 1 minuto e a operação falhou, a próxima repetição é em 1:00 + 1 min (duração) + 1min (intervalo de repetições) = 1:02 PM. <br/><br/>Para setores no passado, não existe nenhum atraso. A nova tentativa ocorre imediatamente. |Não |01: 00:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se esta propriedade é definida como 10 minutos, a validação deve ser concluída em 10 minutos. Se precisar de mais de 10 minutos para efetuar a validação, a repetição exceder o tempo limite.<br/><br/>Se todas as tentativas para o tempo limite de validação, o setor está marcado como **excedido**. |Não |10: 00:00 (10 minutos) |
| maximumRetry |O número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é de 10. |Não |3 |


## <a name="create-datasets"></a>Criar conjuntos de dados
Pode criar conjuntos de dados ao utilizar uma destas ferramentas ou SDKs:

- Assistente de Cópia
- Portal do Azure
- Visual Studio
- PowerShell
- Modelo Azure Resource Manager
- API REST
- API .NET

Veja os tutoriais seguintes para obter instruções passo a passo para a criação de pipelines e conjuntos de dados ao utilizar uma destas ferramentas ou SDKs:

- [Criar um pipeline cum uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Criar um pipeline com uma atividade de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Depois de um pipeline é criado e implementado, pode gerir e monitorizar os seus pipelines com os painéis do portal do Azure ou a aplicação de monitorização e gestão. Consulte os seguintes tópicos para obter instruções passo a passo:

- [Monitorizar e gerir pipelines com os painéis do portal do Azure](data-factory-monitor-manage-pipelines.md)
- [Monitorizar e gerir pipelines com a aplicação de monitorização e gestão](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Âmbito de conjuntos de dados
Pode criar conjuntos de dados que estão no âmbito de um pipeline, utilizando o **conjuntos de dados** propriedade. Estes conjuntos de dados só podem ser utilizados por atividades dentro deste pipeline, e não por atividades noutros pipelines. O exemplo seguinte define um pipeline com dois conjuntos de dados (InputDataset rdc e OutputDataset rdc) a ser utilizado no pipeline.

> [!IMPORTANT]
> Âmbito de conjuntos de dados só são suportados com pipelines de uso individual (em que **pipelineMode** está definida como **OneTime**). Ver [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) para obter detalhes.
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Passos Seguintes
- Para obter mais informações sobre pipelines, consulte [Criar pipelines de](data-factory-create-pipelines.md).
- Para obter mais informações sobre como os pipelines são agendados e executados, consulte [agendamento e execução no Azure Data Factory](data-factory-scheduling-and-execution.md).
