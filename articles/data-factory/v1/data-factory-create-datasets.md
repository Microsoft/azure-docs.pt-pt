---
title: Criar conjuntos de os Azure Data Factory | Microsoft Docs
description: Saiba como criar conjuntos de os em Azure Data Factory, com exemplos que usam propriedades como offset e anchorDateTime.
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
ms.openlocfilehash: af5de469b4c4ca57979b80e691e9a5d12b573bec
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140128"
---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de valores no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-create-datasets.md)
> * [Versão 2 (versão atual)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conjuntos de valores na v2](../concepts-datasets-linked-services.md).

Este artigo descreve o que são conjuntos de valores, como eles são definidos no formato JSON e como eles são usados em pipelines de Azure Data Factory. Ele fornece detalhes sobre cada seção (por exemplo, estrutura, disponibilidade e política) na definição de JSON do conjunto de dados. O artigo também fornece exemplos para usar as propriedades **offset**, **anchorDateTime**e **Style** em uma definição de JSON de conjunto de uma.

> [!NOTE]
> Se você for novo no Data Factory, consulte [introdução ao Azure data Factory](data-factory-introduction.md) para obter uma visão geral. Se você não tiver experiência prática com a criação de fábricas de dados, poderá obter uma melhor compreensão lendo o tutorial de [transformação de dados](data-factory-build-your-first-pipeline.md) e o [tutorial de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **pipeline** é um agrupamento lógico de **atividades** que juntos executam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um SQL Server local para o armazenamento de BLOBs do Azure. Em seguida, você pode usar uma atividade do hive que executa um script do hive em um cluster do Azure HDInsight para processar dados do armazenamento de BLOBs para produzir dados de saída. Por fim, você pode usar uma segunda atividade de cópia para copiar os dados de saída para o Azure SQL Data Warehouse, sobre quais soluções de relatório de business intelligence (BI) são criadas. Para obter mais informações sobre pipelines e atividades, consulte [pipelines e atividades em Azure data Factory](data-factory-create-pipelines.md).

Uma atividade pode usar zero ou mais **conjuntos**de dados de entrada e produzir um ou mais conjuntos de resultados de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída para a atividade. Os conjuntos de dados identificam dados dentro de diferentes arquivos de dados, como tabelas, ficheiros, pastas e documentos. Por exemplo, um conjunto de dados de blob do Azure especifica o contêiner de BLOB e a pasta no armazenamento de BLOBs do qual o pipeline deve ler os dados.

Antes de criar um conjunto de dados, crie um **serviço vinculado** para vincular o seu armazenamento de data ao data Factory. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Os conjuntos de dados identificam os dados dentro dos armazenamentos vinculados, como tabelas, arquivos, pastas e documentos do SQL. Por exemplo, um serviço vinculado do armazenamento do Azure vincula uma conta de armazenamento ao data factory. Um conjunto de dados de blob do Azure representa o contêiner de BLOB e a pasta que contém os blobs de entrada a serem processados.

Aqui está um cenário de exemplo. Para copiar dados de um armazenamento de BLOBs para um SQL Database, você cria dois serviços vinculados: Armazenamento do Azure e banco de dados SQL do Azure. Em seguida, crie dois conjuntos de valores: Conjunto de dados de BLOBs do Azure (que se refere ao serviço vinculado do armazenamento do Azure) e ao DataSet da tabela SQL do Azure (que se refere ao serviço vinculado do banco de dados SQL do Azure). Os serviços vinculados de armazenamento do Azure e banco de dados SQL do Azure contêm cadeias de conexão que Data Factory usa em tempo de execução para se conectar ao armazenamento do Azure e ao banco de dados SQL do Azure, respectivamente O conjunto de dados de blob do Azure especifica o contêiner de BLOB e a pasta de BLOB que contém os blobs de entrada no armazenamento de BLOBs. O conjunto de dados da tabela SQL do Azure especifica a tabela SQL em seu banco de dado SQL para a qual os dados serão copiados.

O diagrama a seguir mostra as relações entre pipeline, atividade, conjunto de serviços e serviço vinculado no Data Factory:

![Relação entre pipeline, Activity, DataSet, serviços vinculados](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>DataSet JSON
Um conjunto de Data Factory é definido no formato JSON da seguinte maneira:

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

A tabela a seguir descreve as propriedades no JSON acima:

| Propriedade | Descrição | Requerido | Predefinição |
| --- | --- | --- | --- |
| name |Nome do conjunto de uma. Consulte [regras de nomenclatura de Azure data Factory](data-factory-naming-rules.md) para regras de nomenclatura. |Sim |ND |
| type |Tipo do conjunto de um. Especifique um dos tipos com suporte pelo Data Factory (por exemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obter detalhes, consulte [tipo de conjunto](#Type)de informações. |Sim |ND |
| structure |Esquema do conjunto de um.<br/><br/>Para obter detalhes, consulte [estrutura do conjunto](#Structure)de dados. |Não |ND |
| typeProperties | As propriedades de tipo são diferentes para cada tipo (por exemplo: Blob do Azure, tabela SQL do Azure). Para obter detalhes sobre os tipos com suporte e suas propriedades, consulte [tipo de conjunto](#Type)de informações. |Sim |ND |
| external | Sinalizador booliano para especificar se um conjunto de um DataSet é gerado explicitamente por um pipeline data factory ou não. Se o conjunto de dados de entrada para uma atividade não for produzido pelo pipeline atual, defina esse sinalizador como true. Defina esse sinalizador como true para o conjunto de dados de entrada da primeira atividade no pipeline.  |Não |false |
| availability | Define a janela de processamento (por exemplo, a cada hora ou diariamente) ou o modelo de divisão para a produção do conjunto de uma. Cada unidade de dados consumida e produzida por uma execução de atividade é chamada de fatia de dados. Se a disponibilidade de um conjunto de resultados de saída for definida como diária (frequência-dia, intervalo-1), uma fatia será produzida diariamente. <br/><br/>Para obter detalhes, consulte disponibilidade do conjunto de informações. <br/><br/>Para obter detalhes sobre o modelo de divisão de conjunto de informações, consulte o artigo [agendamento e execução](data-factory-scheduling-and-execution.md) . |Sim |ND |
| policy |Define os critérios ou a condição que as fatias de DataSet devem atender. <br/><br/>Para obter detalhes, consulte a seção [política de conjunto](#Policy) de informações. |Não |ND |

## <a name="dataset-example"></a>Exemplo de DataSet
No exemplo a seguir, o DataSet representa uma tabela chamada **MyTable** em um banco de dados SQL.

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

* **Type** é definido como AzureSqlTable.
* a propriedade do tipo **TableName** (específica ao tipo AzureSqlTable) é definida como MyTable.
* **linkedServiceName** refere-se a um serviço vinculado do tipo AzureSqlDatabase, que é definido no próximo trecho de JSON.
* a **frequência de disponibilidade** é definida como dia e o **intervalo** é definido como 1. Isso significa que a fatia do conjunto de os é produzida diariamente.

**AzureSqlLinkedService** é definido da seguinte maneira:

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

No trecho de JSON anterior:

* **Type** é definido como AzureSqlDatabase.
* a propriedade do tipo **ConnectionString** especifica informações para se conectar a um banco de dados SQL.

Como você pode ver, o serviço vinculado define como se conectar a um banco de dados SQL. O conjunto de dados define qual tabela é usada como entrada e saída para a atividade em um pipeline.

> [!IMPORTANT]
> A menos que um conjunto de um DataSet esteja sendo produzido pelo pipeline, ele deve ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline.

## <a name="Type"></a>Tipo de conjunto de texto
O tipo do conjunto de dados depende do armazenamento de dados que você usa. Consulte a tabela a seguir para obter uma lista de armazenamentos de dados com suporte pelo Data Factory. Clique em um armazenamento de dados para saber como criar um serviço vinculado e um conjunto de dados para esse armazenamento de dado.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Armazenamentos de dados com * podem ser locais ou na infraestrutura como serviço (IaaS) do Azure. Esses armazenamentos de dados exigem que você instale [Gerenciamento de dados gateway](data-factory-data-management-gateway.md).

No exemplo na seção anterior, o tipo do conjunto de os é definido como **AzureSqlTable**. Da mesma forma, para um conjunto de um DataSet do Azure, o tipo do conjunto de um é definido como **AzureBlob**, conforme mostrado no JSON a seguir:

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
A seção de **estrutura** é opcional. Ele define o esquema do conjunto de dados, contendo uma coleção de nomes e tipos de dado de colunas. Use a seção estrutura para fornecer informações de tipo que são usadas para converter tipos e mapear colunas da origem para o destino. No exemplo a seguir, o DataSet tem três colunas: `slicetimestamp`, `projectname`e `pageviews`. Eles são do tipo cadeia de caracteres, Cadeia de caracteres e decimal, respectivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Cada coluna na estrutura contém as seguintes propriedades:

| Propriedade | Descrição | Requerido |
| --- | --- | --- |
| name |Nome da coluna. |Sim |
| type |Tipo de dados da coluna.  |Não |
| culture |. Cultura baseada em rede a ser usada quando o tipo é um tipo .net: `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. |Não |
| format |Cadeia de caracteres de formato a ser usada quando o tipo é um `Datetime` tipo `Datetimeoffset`.net: ou. |Não |

As diretrizes a seguir ajudam a determinar quando incluir informações de estrutura e o que incluir na seção de **estrutura** .

* **Para fontes de dados estruturadas**, especifique a seção de estrutura somente se desejar mapear colunas de origem para colunas do coletor e seus nomes não forem iguais. Esse tipo de fonte de dados estruturado armazena informações de tipo e esquema de dados juntamente com os dados em si. Exemplos de fontes de dados estruturadas incluem SQL Server, Oracle e tabela do Azure.
  
    Como as informações de tipo já estão disponíveis para fontes de dados estruturadas, você não deve incluir informações de tipo ao incluir a seção de estrutura.
* **Para o esquema em fontes de dados de leitura (especificamente o armazenamento de BLOBs)** , você pode optar por armazenar dados sem armazenar nenhuma informação de tipo ou esquema com os dados. Para esses tipos de fontes de dados, inclua a estrutura quando desejar mapear as colunas de origem para as colunas do coletor. Também inclua a estrutura quando o conjunto de dados for uma entrada para uma atividade de cópia, e os tipos de dado do conjunto de dados de origem devem ser convertidos em tipos nativos para o coletor.
    
    Data Factory dá suporte aos seguintes valores para fornecer informações de tipo na estrutura: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], Boolean, String, GUID, DateTime, DateTimeOffset e TimeSpan**. Esses valores são compatíveis com Common Language Specification (CLS),. Valores de tipo baseados em NET.

Data Factory executa automaticamente conversões de tipo ao mover dados de um armazenamento de dados de origem para um armazenamento de dados de coletor.

## <a name="dataset-availability"></a>Disponibilidade do conjunto de
A seção de **disponibilidade** em um conjunto de uma define a janela de processamento (por exemplo, por hora, diariamente ou semanalmente) para o conjunto de um. Para obter mais informações sobre janelas de atividades, consulte [agendamento e execução](data-factory-scheduling-and-execution.md).

A seção de disponibilidade a seguir especifica que o conjunto de dados de saída é produzido por hora, ou o DataSet de entrada está disponível por hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Se o pipeline tiver as seguintes horas de início e término:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

O conjunto de resultados de saída é produzido por hora nas horas de início e término do pipeline. Portanto, há cinco fatias de DataSet produzidas por esse pipeline, uma para cada janela de atividade (12:00-1:00, 1:00-6:00, 1:00-3:00, 8h-6:00, 1:00-17:00).

A tabela a seguir descreve as propriedades que podem ser usadas na seção disponibilidade:

| Propriedade | Descrição | Requerido | Predefinição |
| --- | --- | --- | --- |
| frequency |Especifica a unidade de tempo para produção da fatia do conjunto de um.<br/><br/><b>Frequência com suporte</b>: Minuto, hora, dia, semana, mês |Sim |ND |
| interval |Especifica um multiplicador para frequência.<br/><br/>"Intervalo de frequência x" determina a frequência com que a fatia é produzida. Por exemplo, se você precisar que o conjunto de um seja dividido por hora, defina a <b>frequência</b> como <b>hora</b>e o <b>intervalo</b> como <b>1</b>.<br/><br/>Observe que, se você especificar **frequência** como **minuto**, deverá definir o intervalo como não menor que 15. |Sim |ND |
| style |Especifica se a fatia deve ser produzida no início ou no final do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Se **Frequency** for definido como **month**e **Style** for definido como **EndOfInterval**, a fatia será produzida no último dia do mês. Se **Style** for definido como **StartOfInterval**, a fatia será produzida no primeiro dia do mês.<br/><br/>Se **Frequency** for definido como **Day**e **Style** for definido como **EndOfInterval**, a fatia será produzida na última hora do dia.<br/><br/>Se **Frequency** for definido como **Hour**e **Style** for definido como **EndOfInterval**, a fatia será produzida no final da hora. Por exemplo, para uma fatia do período de 1 PM-2 PM, a fatia é produzida às 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo usada pelo Agendador para computar os limites de fatia do conjunto de cálculo. <br/><br/>Observe que, se essa propriedade tiver partes de data mais granulares do que a frequência especificada, as partes mais granulares serão ignoradas. Por exemplo, se o **intervalo** for por **hora** (frequência: hora e intervalo: 1), e o **anchorDateTime** contém **minutos e segundos**e, em seguida, as partes de minutos e segundos de **anchorDateTime** são ignoradas. |Não |01/01/0001 |
| desvio |Período de tempo pelo qual o início e o término de todas as fatias do conjunto de todos são deslocados. <br/><br/>Observe que, se **anchorDateTime** e **offset** forem especificados, o resultado será o deslocamento combinado. |Não |ND |

### <a name="offset-example"></a>exemplo de deslocamento
Por padrão, as fatias diárias (`"frequency": "Day", "interval": 1`) começam às 12 (meia-noite) tempo universal coordenado (UTC). Se você quiser que a hora de início seja 6, hora UTC, defina o deslocamento conforme mostrado no trecho a seguir:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>exemplo de anchorDateTime
No exemplo a seguir, o DataSet é produzido uma vez a cada 23 horas. A primeira fatia começa no momento especificado por **anchorDateTime**, que é definido como `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>exemplo de deslocamento/estilo
O conjunto de DataSet a seguir é mensal e é produzido no terceiro de cada mês às 8:00 AM`3.08:00:00`():

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Política de conjunto de
A seção de **política** na definição do conjunto de conjuntos define os critérios ou a condição que as fatias do conjunto de os deve atender.

### <a name="validation-policies"></a>Políticas de validação
| Nome da política | Descrição | Aplicado a | Requerido | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que os dados no **armazenamento de BLOBs do Azure** atendem aos requisitos mínimos de tamanho (em megabytes). |Armazenamento de Blobs do Azure |Não |ND |
| minimumRows |Valida que os dados em um banco de dados **SQL do Azure** ou uma **tabela do Azure** contêm o número mínimo de linhas. |<ul><li>Base de dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |ND |

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

### <a name="external-datasets"></a>Conjuntos de valores externos
Os conjuntos de valores externos são aqueles que não são produzidos por um pipeline em execução no data factory. Se o conjunto de um estiver marcado como **externo**, a política **ExternalData** poderá ser definida para influenciar o comportamento da disponibilidade da fatia do conjunto de uma.

A menos que um conjunto de um DataSet esteja sendo produzido por Data Factory, ele deve ser marcado como **externo**. Essa configuração geralmente se aplica às entradas da primeira atividade em um pipeline, a menos que a atividade ou o encadeamento de pipeline esteja sendo usado.

| Name | Descrição | Requerido | Valor predefinido |
| --- | --- | --- | --- |
| dataDelay |O tempo para atrasar a verificação na disponibilidade dos dados externos para a fatia determinada. Por exemplo, você pode atrasar uma verificação por hora usando essa configuração.<br/><br/>A configuração se aplica somente ao momento atual. Por exemplo, se for 1:00 PM no momento e esse valor for de 10 minutos, a validação começará às 1:10 PM.<br/><br/>Observe que essa configuração não afeta as fatias no passado. As fatias com a **hora** + de término da fatia datadelay < **agora** são processadas sem nenhum atraso.<br/><br/>Horas maiores que 23:59 horas devem ser especificadas usando o `day.hours:minutes:seconds` formato. Por exemplo, para especificar 24 horas, não use 24:00:00. Em vez disso, use 1,00:00:00. Se você usar o 24:00:00, ele será tratado como 24 dias (24.00:00:00). Por 1 dia e 4 horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre uma falha e a próxima tentativa. Essa configuração se aplica a hora atual. Se a tentativa anterior falhar, a próxima tentativa será após o período de **retryInterval** . <br/><br/>Se for 1:00 PM no momento, começaremos a primeira tentativa. Se a duração para concluir a primeira verificação de validação for de 1 minuto e a operação falhar, a próxima tentativa será de 1:00 + 1min (duração) + 1min (intervalo de repetição) = 1:02 PM. <br/><br/>Para fatias no passado, não há nenhum atraso. A repetição ocorre imediatamente. |Não |00:01:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se essa propriedade for definida como 10 minutos, a validação deverá ser concluída dentro de 10 minutos. Se demorar mais de 10 minutos para executar a validação, a nova tentativa atingirá o tempo limite.<br/><br/>Se todas as tentativas para a validação atingirem o tempo limite, a fatia será marcada como **TimedOut**. |Não |00:10:00 (10 minutos) |
| maximumRetry |O número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é 10. |Não |3 |


## <a name="create-datasets"></a>Criar conjuntos de dados
Você pode criar conjuntos de valores usando uma destas ferramentas ou SDKs:

- Assistente de Cópia
- Visual Studio
- PowerShell
- Modelo Azure Resource Manager
- API REST
- API .NET

Consulte os tutoriais a seguir para obter instruções passo a passo para criar pipelines e conjuntos de valores usando uma destas ferramentas ou SDKs:

- [Criar um pipeline cum uma atividade de transformação de dados](data-factory-build-your-first-pipeline.md)
- [Criar um pipeline com uma atividade de movimentação de dados](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Depois que um pipeline é criado e implantado, você pode gerenciar e monitorar seus pipelines usando as portal do Azure Blades ou o aplicativo de monitoramento e gerenciamento. Consulte os tópicos a seguir para obter instruções passo a passo:

- [Monitorar e gerenciar pipelines usando as folhas de portal do Azure](data-factory-monitor-manage-pipelines.md)
- [Monitorar e gerenciar pipelines usando o aplicativo de monitoramento e gerenciamento](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Conjuntos de valores com escopo
Você pode criar conjuntos de valores com escopo para um pipeline usando a propriedade **DataSets** . Esses conjuntos de valores só podem ser usados por atividades dentro desse pipeline, não por atividades em outros pipelines. O exemplo a seguir define um pipeline com dois conjuntos de valores (InputDataset-RDC e OutputDataset-RDC) a serem usados no pipeline.

> [!IMPORTANT]
> Só há suporte para conjuntos de data com escopo com pipelines únicos (em que pipelinemode é definido como **OneTime**). Consulte [pipeline de OneTime](data-factory-create-pipelines.md#onetime-pipeline) para obter detalhes.
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
- Para obter mais informações sobre pipelines, consulte [criar pipelines](data-factory-create-pipelines.md).
- Para obter mais informações sobre como os pipelines são agendados e executados, consulte [agendamento e execução em Azure data Factory](data-factory-scheduling-and-execution.md).
