---
title: Criar conjuntos de dados na Azure Data Factory
description: Saiba como criar conjuntos de dados na Azure Data Factory, com exemplos que utilizam propriedades como offset e anchorDateTime.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 45150e00db1885a4ca4d083a8a54cbfd4da0bb10
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456936"
---
# <a name="datasets-in-azure-data-factory-version-1"></a>Conjuntos de dados na Azure Data Factory (versão 1)
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-create-datasets.md)
> * [Versão 2 (versão atual)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [datasets em V2](../concepts-datasets-linked-services.md).

Este artigo descreve quais são os conjuntos de dados, como são definidos no formato JSON e como são usados nos oleodutos Azure Data Factory. Fornece detalhes sobre cada secção (por exemplo, estrutura, disponibilidade e política) na definição de JSON do conjunto de dados. O artigo também fornece exemplos para a utilização do **offset**, **anchorDateTime**, e propriedades **de estilo** numa definição JSON do conjunto de dados.

> [!NOTE]
> Se é novo na Data Factory, consulte [a Introdução à Fábrica de Dados Azure](data-factory-introduction.md) para uma visão geral. Se não tiver experiência prática na criação de fábricas de dados, poderá obter uma melhor compreensão lendo o tutorial de transformação de [dados](data-factory-build-your-first-pipeline.md) e o tutorial de [movimento de dados.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **oleoduto** é um agrupamento lógico de **atividades** que juntos desempenham uma tarefa. As atividades num pipeline definem as ações a executar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de uma base de dados do SQL Server para o armazenamento de Azure Blob. Em seguida, você pode usar uma atividade de Hive que executa um script hive em um cluster Azure HDInsight para processar dados do armazenamento Blob para produzir dados de saída. Finalmente, poderá utilizar uma segunda atividade de cópia para copiar os dados de saída para a Azure Synapse Analytics, além das quais são construídas soluções de reporte de informação de business intelligence (BI). Para obter mais informações sobre os oleodutos e atividades, consulte [Pipelines e atividades na Azure Data Factory.](data-factory-create-pipelines.md)

Uma atividade pode tomar zero ou mais **conjuntos de dados de** entrada , e produzir um ou mais conjuntos de dados de saída. Um conjunto de dados de entrada representa a entrada para uma atividade no pipeline, e um conjunto de dados de saída representa a saída para a atividade. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados Azure Blob especifica o recipiente blob e a pasta no armazenamento blob a partir do qual o pipeline deve ler os dados.

Antes de criar um conjunto de dados, crie um **serviço ligado** para ligar a sua loja de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Os conjuntos de dados identificam dados dentro das lojas de dados ligadas, tais como tabelas SQL, ficheiros, pastas e documentos. Por exemplo, um serviço ligado ao Azure Storage liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados Azure Blob representa o recipiente blob e a pasta que contém as bolhas de entrada a serem processadas.

Aqui está um cenário de amostra. Para copiar dados do armazenamento blob para a Base de Dados SQL, cria dois serviços ligados: Azure Storage e Azure SQL Database. Em seguida, crie dois conjuntos de dados: conjunto de dados Azure Blob (que se refere ao serviço ligado ao Armazenamento Azure) e conjunto de dados de tabela Azure SQL (que se refere ao serviço ligado à Base de Dados Azure SQL). Os serviços ligados ao Azure Storage e Azure SQL Database contêm cadeias de ligação que a Data Factory utiliza em tempo de execução para ligar à sua Base de Dados Azure Storage e Azure SQL, respectivamente. O conjunto de dados Azure Blob especifica o recipiente blob e a pasta blob que contém as bolhas de entrada no seu armazenamento Blob. O conjunto de dados da tabela Azure SQL especifica a tabela SQL na sua base de dados SQL para a qual os dados devem ser copiados.

O diagrama a seguir mostra as relações entre o pipeline, a atividade, o conjunto de dados e o serviço ligado na Data Factory:

![Relação entre pipeline, atividade, conjunto de dados, serviços ligados](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Conjunto de dados JSON
Um conjunto de dados na Data Factory é definido no formato JSON da seguinte forma:

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

A tabela a seguir descreve propriedades no JSON acima:

| Propriedade | Descrição | Obrigatório | Predefinição |
| --- | --- | --- | --- |
| name |Nome do conjunto de dados. Ver [Azure Data Factory - Regras de nomeação](data-factory-naming-rules.md) para regras de nomeação. |Sim |ND |
| tipo |Tipo do conjunto de dados. Especifique um dos tipos suportados pela Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para mais informações, consulte o [tipo dataset](#Type). |Sim |ND |
| estrutura |Esquema do conjunto de dados.<br/><br/>Para mais detalhes, consulte a [estrutura do Conjunto de Dados.](#Structure) |Não |ND |
| typeProperties | As propriedades do tipo são diferentes para cada tipo (por exemplo: Azure Blob, tabela Azure SQL). Para obter mais informações sobre os tipos suportados e as suas propriedades, consulte [o tipo Dataset](#Type). |Sim |ND |
| externo | Bandeira booleana para especificar se um conjunto de dados é explicitamente produzido por um oleoduto de fábrica de dados ou não. Se o conjunto de dados de entrada para uma atividade não for produzido pelo gasoduto atual, esta bandeira seja verdadeira. Defina esta bandeira de forma verdadeira para o conjunto de dados de entrada da primeira atividade no oleoduto.  |Não |false |
| disponibilidade | Define a janela de processamento (por exemplo, de hora a hora ou diariamente) ou o modelo de corte para a produção do conjunto de dados. Cada unidade de dados consumidos e produzidos por uma atividade executada é chamada de fatia de dados. Se a disponibilidade de um conjunto de dados de saída for definida diariamente (frequência - dia, intervalo - 1), uma fatia é produzida diariamente. <br/><br/>Para mais detalhes, consulte a disponibilidade do Dataset. <br/><br/>Para obter mais informações sobre o modelo de corte de conjuntos de dados, consulte o artigo [de Agendamento e execução.](data-factory-scheduling-and-execution.md) |Sim |ND |
| política |Define os critérios ou a condição que as fatias de conjunto de dados devem cumprir. <br/><br/>Para mais detalhes, consulte a secção [de política do Dataset.](#Policy) |Não |ND |

## <a name="dataset-example"></a>Exemplo do conjunto de dados
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

* **tipo** é definido para AzureSqlTable.
* **tableName** tipo propriedade (específica do tipo AzureSqlTable) é definida para MyTable.
* **linkedServiceName** refere-se a um serviço ligado do tipo AzureSqlDatabase, que é definido no próximo snippet JSON.
* **a frequência de disponibilidade** é definida para o dia, e **o intervalo** é definido para 1. Isto significa que a fatia de conjunto de dados é produzida diariamente.

**O AzureSqlLinkedService** é definido da seguinte forma:

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

No corte JSON anterior:

* **o tipo** é definido para AzureSqlDatabase.
* **ligação A** propriedade tipo de ligação especifica informações para ligar a uma base de dados SQL.

Como pode ver, o serviço ligado define como ligar-se a uma base de dados SQL. O conjunto de dados define qual a tabela utilizada como entrada e saída para a atividade num oleoduto.

> [!IMPORTANT]
> A menos que um conjunto de dados seja produzido pelo gasoduto, este deve ser marcado como **externo**. Esta definição aplica-se geralmente às entradas da primeira atividade num gasoduto.

## <a name="dataset-type"></a><a name="Type"></a> Tipo de conjunto de dados
O tipo de conjunto de dados depende da loja de dados que utiliza. Consulte a tabela seguinte para obter uma lista de lojas de dados suportadas pela Data Factory. Clique numa loja de dados para saber como criar um serviço ligado e um conjunto de dados para essa loja de dados.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> As lojas de dados com * podem estar no local ou na infraestrutura Azure como um serviço (IaaS). Estas lojas de dados exigem que instale [o Data Management Gateway.](data-factory-data-management-gateway.md)

No exemplo da secção anterior, o tipo de conjunto de dados é definido para **AzureSqlTable**. Da mesma forma, para um conjunto de dados Azure Blob, o tipo de conjunto de dados é definido para **AzureBlob,** como mostrado no seguinte JSON:

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

## <a name="dataset-structure"></a><a name="Structure"></a>Estrutura de conjunto de dados
A secção **de estrutura** é opcional. Define o esquema do conjunto de dados contendo uma recolha de nomes e tipos de dados de colunas. Utiliza a secção de estrutura para fornecer informações de tipo que são usadas para converter tipos e colunas de mapas da fonte para o destino. No exemplo seguinte, o conjunto de dados tem três colunas: `slicetimestamp` `projectname` , e `pageviews` . São do tipo String, String e Decimal, respectivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Cada coluna da estrutura contém as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| name |O nome da coluna. |Sim |
| tipo |Tipo de dados da coluna.  |Não |
| cultura |.Cultura baseada em NET a ser utilizada quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset` . A predefinição é `en-us`. |Não |
| formato |Cadeia de formato a utilizar quando o tipo é um tipo .NET: `Datetime` ou `Datetimeoffset` . |Não |

As seguintes diretrizes ajudam-no a determinar quando incluir informações de estrutura e o que incluir na secção de **estrutura.**

* **Para fontes de dados estruturadas, especifique** a secção de estrutura apenas se quiser que as colunas de origem do mapa afundem colunas, e os seus nomes não são os mesmos. Este tipo de fonte de dados estruturada armazena esquema de dados e informação de tipo, juntamente com os próprios dados. Exemplos de fontes de dados estruturadas incluem sql server, Oracle e tabela Azure.
  
    Uma vez que a informação do tipo já está disponível para fontes de dados estruturadas, não deve incluir informações do tipo quando incluir a secção de estrutura.
* **Para esquemas sobre fontes de dados de leitura (especificamente armazenamento blob),** pode optar por armazenar dados sem armazenar qualquer esquema ou tipo de informação com os dados. Para este tipo de fontes de dados, inclua a estrutura quando pretende mapear colunas de origem para afundar colunas. Também inclua a estrutura quando o conjunto de dados é uma entrada para uma atividade de cópia, e os tipos de dados de dados de origem devem ser convertidos para tipos nativos para o lavatório.
    
    A Data Factory suporta os seguintes valores para fornecer informações de tipo em estrutura: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetimeset, e Timespan**. Estes valores são compatíveis com especificações linguísticas comuns (CLS), . Valores de tipo net.000.

A Data Factory realiza automaticamente conversões de tipo ao mover dados de uma loja de dados de origem para uma loja de dados de lavatórios.

## <a name="dataset-availability"></a>Disponibilidade do conjunto de dados
A secção **de disponibilidade** num conjunto de dados define a janela de processamento (por exemplo, hora, dia ou semana) para o conjunto de dados. Para obter mais informações sobre janelas de atividades, consulte [Agendamento e execução.](data-factory-scheduling-and-execution.md)

A seguinte secção de disponibilidade especifica que o conjunto de dados de saída é produzido de hora a hora, ou o conjunto de dados de entrada está disponível a cada hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Se o gasoduto tiver os seguintes tempos de início e fim:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

O conjunto de dados de saída é produzido de hora em hora dentro dos tempos de início e fim do gasoduto. Portanto, existem cinco fatias de conjunto de dados produzidas por este oleoduto, uma para cada janela de atividade (12:00 - 1:00- 1:00 - 2:00 - 3:00, 3:00 - 4:00, 4:00 - 5:00).

A tabela a seguir descreve propriedades que pode utilizar na secção de disponibilidade:

| Propriedade | Descrição | Obrigatório | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para a produção de fatias de conjunto de dados.<br/><br/><b>Frequência suportada</b>: Minuto, Hora, Dia, Semana, Mês |Sim |ND |
| interval |Especifica um multiplicador para a frequência.<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida. Por exemplo, se necessitar que o conjunto de dados seja cortado de hora a hora, define a <b>frequência</b> para <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/>Tenha em atenção que, se especificar **a frequência** como **Minuto,** deverá definir o intervalo para nada menos do que 15. |Sim |ND |
| estilo |Especifica se a fatia deve ser produzida no início ou no fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Se **a frequência** estiver definida para **mês**, e **o estilo** está definido para **EndOfInterval**, a fatia é produzida no último dia do mês. Se **o estilo** estiver definido para **StartOfInterval,** a fatia é produzida no primeiro dia do mês.<br/><br/>Se **a frequência** estiver definida para o **Dia**, e o **estilo** estiver definido para **EndOfInterval,** a fatia é produzida na última hora do dia.<br/><br/>Se **a frequência** estiver definida para **Hora**, e o **estilo** estiver definido para **EndOfInterval,** a fatia é produzida no final da hora. Por exemplo, para uma fatia para o período das 13:00 às 14:00, a fatia é produzida às 14:00. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo utilizado pelo programador para calcular os limites da fatia de conjunto de dados. <br/><br/>Note que se esta propriedade tem peças de data mais granulares do que a frequência especificada, as peças mais granulares são ignoradas. Por exemplo, se o **intervalo** for **de hora** a hora (frequência: hora e intervalo: 1) e a **âncoraDateTime** contiver **minutos e segundos**, então as partes de minutos e segundos do **anchorDateTime** são ignoradas. |Não |01/01/0001 |
| offset |Timepan pelo qual o início e o fim de todas as fatias de conjunto de dados são deslocados. <br/><br/>Note que se ambos **anchorDatime** e **offset** forem especificados, o resultado é a mudança combinada. |Não |ND |

### <a name="offset-example"></a>exemplo offset
Por padrão, `"frequency": "Day", "interval": 1` as fatias diárias () começam às 12:00 (meia-noite) Tempo Universal Coordenado (UTC). Se pretender que a hora de início seja 6 HORAS UTC, desapeje a compensação como mostrado no seguinte corte:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime exemplo
No exemplo seguinte, o conjunto de dados é produzido uma vez a cada 23 horas. A primeira fatia começa no momento especificado pelo **anchorDateTime**, que está definido para `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>offset/exemplo de estilo
O conjunto de dados seguinte é mensal, e é produzido no dia 3 de cada mês às 8:00 am `3.08:00:00` ( ):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Política de dataset
A secção **de política** na definição de conjunto de dados define os critérios ou a condição que as fatias de conjunto de dados devem cumprir.

### <a name="validation-policies"></a>Políticas de validação
| Nome da política | Descrição | Aplicado a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| tamanho mínimoS |Valida que os dados no **armazenamento Azure Blob** satisfazem os requisitos mínimos de tamanho (em megabytes). |Armazenamento de Blobs do Azure |Não |ND |
| sobrancelhas mínimas |Valida que os dados numa **base de dados Azure SQL** ou numa **tabela Azure** contém o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

#### <a name="examples"></a>Exemplos
**tamanho mínimoS:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**sobrancelhas mínimas:**

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
Conjuntos de dados externos são os que não são produzidos por um gasoduto em funcionamento na fábrica de dados. Se o conjunto de dados for marcado como **externo,** a política **ExternalData** pode ser definida para influenciar o comportamento da disponibilidade da fatia de conjunto de dados.

A menos que um conjunto de dados seja produzido pela Data Factory, deve ser marcado como **externo**. Esta definição aplica-se geralmente às entradas da primeira atividade num gasoduto, a menos que esteja a ser utilizada atividade ou acorrentamento do gasoduto.

| Nome | Descrição | Obrigatório | Valor predefinido |
| --- | --- | --- | --- |
| dataDelaia |É o momento de atrasar a verificação da disponibilidade dos dados externos para a fatia dada. Por exemplo, pode atrasar uma verificação de hora a hora utilizando esta definição.<br/><br/>A regulação aplica-se apenas ao presente momento. Por exemplo, se forem 13:00 pm agora e este valor é de 10 minutos, a validação começa às 13:10.<br/><br/>Note que esta definição não afeta fatias no passado. Fatias com **dados de fim de tempo de**  +  **fatiaDela agora** são  <  **Now** processadas sem demora.<br/><br/>Os tempos superiores às 23:59 horas devem ser especificados utilizando o `day.hours:minutes:seconds` formato. Por exemplo, para especificar 24 horas, não use 24:00:00. Em vez disso, use 1:00:00:00. Se utilizar 24:00:00, é tratado como 24 dias (24.00:00:00). Por 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre um fracasso e a próxima tentativa. Esta definição aplica-se ao tempo presente. Se a tentativa anterior falhar, a próxima tentativa é após o período **de retryInterval.** <br/><br/>Se são 13:00 agora, começamos a primeira tentativa. Se a duração para completar a primeira verificação de validação for de 1 minuto e a operação falhar, a próxima repetição é de 1:00 + 1min (duração) + 1min (intervalo de repetição) = 1:02 PM. <br/><br/>Para fatias no passado, não há atraso. A repetição acontece imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se esta propriedade estiver definida para 10 minutos, a validação deve ser concluída dentro de 10 minutos. Se demorar mais de 10 minutos a efetuar a validação, o tempo de retítu disso.<br/><br/>Se todas as tentativas para o tempo de validação, a fatia é marcada como **TimedOut**. |Não |00:10:00 (10 minutos) |
| máximaSRetry |O número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é de 10. |Não |3 |


## <a name="create-datasets"></a>Criar conjuntos de dados
Pode criar conjuntos de dados utilizando uma destas ferramentas ou SDKs:

- Assistente de Cópia
- Visual Studio
- PowerShell
- Modelo Azure Resource Manager
- API REST
- API .NET

Consulte os seguintes tutoriais para instruções passo a passo para a criação de oleodutos e conjuntos de dados utilizando uma destas ferramentas ou SDKs:

- [Criar um pipeline cum uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Construir um oleoduto com uma atividade de movimento de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Depois de um gasoduto ser criado e implantado, pode gerir e monitorizar os seus oleodutos utilizando as lâminas do portal Azure ou a aplicação de Monitorização e Gestão. Consulte os seguintes tópicos para instruções passo a passo:

- [Monitorize e gere os oleodutos utilizando lâminas do portal Azure](data-factory-monitor-manage-pipelines.md)
- [Monitorize e gere os oleodutos utilizando a app de monitorização e gestão](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Conjuntos de dados alargados
Pode criar conjuntos de dados que são traçados para um pipeline utilizando a propriedade **de conjuntos de dados.** Estes conjuntos de dados só podem ser utilizados por atividades dentro deste oleoduto, e não por atividades noutros oleodutos. O exemplo a seguir define um pipeline com dois conjuntos de dados (InputDataset-rdc e OutputDataset-rdc) a utilizar dentro do pipeline.

> [!IMPORTANT]
> Os conjuntos de dados com âmbito são suportados apenas com gasodutos únicos (onde **o pipelineMode** está definido para **OneTime).** Consulte [o oleoduto Onetime](data-factory-create-pipelines.md#onetime-pipeline) para mais detalhes.
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

## <a name="next-steps"></a>Passos seguintes
- Para obter mais informações sobre os gasodutos, consulte [criar oleodutos.](data-factory-create-pipelines.md)
- Para obter mais informações sobre como os oleodutos são programados e executados, consulte [Agendamento e execução na Azure Data Factory](data-factory-scheduling-and-execution.md).
