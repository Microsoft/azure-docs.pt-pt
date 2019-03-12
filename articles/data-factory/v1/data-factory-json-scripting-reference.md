---
title: Fábrica de dados do Azure - referência de scripts JSON | Documentos da Microsoft
description: Fornece os esquemas JSON para entidades do Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ee09f8defc7a10b153e910cb7208b0ddb21120b2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543957"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Fábrica de dados do Azure - referência de scripts JSON
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory.


Este artigo fornece os esquemas JSON e exemplos para definir entidades do Azure Data Factory (pipeline, atividade, conjunto de dados e serviço ligado).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pipeline
Segue-se a estrutura de alto nível para uma definição de pipeline:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

A tabela seguinte descreve as propriedades no pipeline definição JSON:

| Propriedade | Descrição | Necessário
-------- | ----------- | --------
| nome | Nome do pipeline. Especifique um nome que represente a ação que a atividade ou o pipeline está configurado para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com um letra, um número ou um caráter de sublinhado (\_)</li><li>Seguintes carateres não são permitidos: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Sim |
| descrição |Texto que descreve o que a atividade ou o pipeline é utilizado para | Não |
| atividades | Contém uma lista de atividades. | Sim |
| início |Data-hora de início para o pipeline. Tem de estar no [formato ISO](https://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41. <br/><br/>É possível especificar uma hora local, por exemplo, um período de tempo EST. Eis um exemplo: `2016-02-27T06:00:00**-05:00`, que é 6 AM estimativa<br/><br/>As propriedades de início e de fim especificam em conjunto o período ativo do pipeline. Apenas os setores de saída são produzidos neste período de Active Directory. |Não<br/><br/>Se especificar um valor para a propriedade final, tem de especificar o valor da propriedade de início.<br/><br/>As horas de início e de fim podem de estar vazias para criar um pipeline. Tem de especificar ambos os valores para definir um período de Active Directory para o execução do pipeline. Se não especificar horários de início e fim quando criar um pipeline, pode configurá-los usando o cmdlet Set-AzDataFactoryPipelineActivePeriod mais tarde. |
| end |Data / hora de fim do pipeline. Se for especificado tem de estar no formato ISO. Por exemplo: 2014-10-14T17:32:41 <br/><br/>É possível especificar uma hora local, por exemplo, um período de tempo EST. Eis um exemplo: `2016-02-27T06:00:00**-05:00`, que é 6 AM estimativa<br/><br/>Para executar o pipeline de forma indefinida, especifique a 9999-09-09 como o valor para a propriedade final. |Não <br/><br/>Se especificar um valor para a propriedade de início, tem de especificar o valor da propriedade end.<br/><br/>Consulte as notas para o **iniciar** propriedade. |
| isPaused |Se definido como true, o pipeline não é executado. Valor predefinido = false. Pode utilizar esta propriedade para ativar ou desativar. |Não |
| pipelineMode |O método para agendar execuções do pipeline. Valores permitidos são: agendada (predefinição), onetime.<br/><br/>"Agendada" indica que o pipeline é executado num intervalo de tempo especificado, de acordo com o período ativo (hora de início e de fim). "Única" indica que o pipeline é executado apenas uma vez. Pipelines onetime, uma vez criadas não podem ser modificado/atualizado atualmente. Ver [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) para obter detalhes sobre a definição de onetime. |Não |
| expirationTime |Duração de tempo após a criação para os quais o pipeline é válido e deve permanecer aprovisionado. Se não tem qualquer ativo, falha, ou pendente é executado, o pipeline é eliminado automaticamente assim que atingir o tempo de expiração. |Não |


## <a name="activity"></a>Atividade
A estrutura de alto nível para uma atividade dentro de uma definição de pipeline (elemento atividades) é o seguinte:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

Seguinte tabela descreve as propriedades na definição JSON da atividade:

| Etiqueta | Descrição | Necessário |
| --- | --- | --- |
| name |Nome da atividade. Especifique um nome que represente a ação que a atividade está configurada para fazer<br/><ul><li>Número máximo de carateres: 260</li><li>Tem de começar com um letra, um número ou um caráter de sublinhado (\_)</li><li>Seguintes carateres não são permitidos: ".", "+","?", "/", "<",">", "*", "%", "&", ":","\\"</li></ul> |Sim |
| descrição |Texto que descreve a atividade é usada para quê. |Não |
| tipo |Especifica o tipo da atividade. Consulte a [ARQUIVOS de dados](#data-stores) e [atividades de transformação de dados](#data-transformation-activities) secções para diferentes tipos de atividades. |Sim |
| entradas |Tabelas de entrada utilizadas pela atividade<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Não para atividades HDInsightStreaming e SqlServerStoredProcedure <br/> <br/> Sim para todos os outros |
| saídas |Tabelas de saída utilizadas pela atividade.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Sim |
| linkedServiceName |Nome do serviço ligado utilizado pela atividade. <br/><br/>Uma atividade pode exigir que especifique o serviço ligado que liga ao ambiente de computação necessário. |Sim para atividades do HDInsight, atividades do Azure Machine Learning e atividade de procedimento armazenado. <br/><br/>Não para todas as outras. |
| typeProperties |Propriedades na secção typeProperties dependem do tipo da atividade. |Não |
| política |Políticas que afetam o comportamento de runtime da atividade. Se não for especificado, são utilizadas políticas predefinidas. |Não |
| Scheduler |propriedade de "scheduler" é utilizada para definir um agendamento pretendida para a atividade. Seu subproperties são as mesmas que na [propriedade de disponibilidade num conjunto de dados](data-factory-create-datasets.md#dataset-availability). |Não |

### <a name="policies"></a>Políticas
As políticas afetam o comportamento de tempo de execução de uma atividade, especificamente quando o setor de uma tabela é processado. A tabela seguinte fornece os detalhes.

| Propriedade | Valores permitidos | Valor Predefinido | Descrição |
| --- | --- | --- | --- |
| simultaneidade |Número Inteiro <br/><br/>Valor máximo: 10 |1 |Número de execuções simultâneas da atividade.<br/><br/>Determina o número de execuções de atividade parallel, que podem ser efetuadas em setores diferentes. Por exemplo, se precisar de uma atividade de passar por um grande conjunto de dados disponíveis, ter um valor maior da simultaneidade acelera o processamento de dados. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina a ordenação de setores de dados que estão a ser processados.<br/><br/>Por exemplo, se tiver 2 reparte (uma aconteça às 16:00 e outro às 17:00), e ambos são execução pendente. Se definir o executionPriorityOrder ser NewestFirst, o setor às 17:00 é processado pela primeira vez. Da mesma forma se definir o executionPriorityORder ser OldestFIrst, em seguida, o setor às 16:00 é processado. |
| repetir |Número Inteiro<br/><br/>Valor máximo pode ser 10 |0 |Número de tentativas antes do processamento de dados para o setor está marcado como falha. Execução da atividade de um setor de dados é repetida até o número especificado de tentativas. A nova tentativa é feita logo que possível após a falha. |
| tempo limite |TimeSpan |00:00:00 |Tempo limite para a atividade. Exemplo: 10: 00:00 (implica o tempo limite de 10 minutos)<br/><br/>Se um valor não for especificado ou for 0, o tempo limite é infinito.<br/><br/>Se o tempo de processamento de dados num setor excede o valor de tempo limite, ser cancelada e o sistema tenta repetir o processamento. O número de repetições depende da propriedade de repetição. Quando ocorre o tempo limite, o estado é definido como excedido. |
| atraso |TimeSpan |00:00:00 |Especifica o atraso antes de processamento de dados do setor a ser iniciada.<br/><br/>A execução da atividade de um setor de dados é iniciada depois do atraso é posterior à hora de execução esperada.<br/><br/>Exemplo: 10: 00:00 (implica o atraso de 10 minutos) |
| longRetry |Número Inteiro<br/><br/>Valor máximo: 10 |1 |O número de tentativas de repetição longa antes da execução de setor falhou.<br/><br/>estão espaçadas a tentativas de longRetry, longRetryInterval. Por isso, se tiver de especificar uma hora entre tentativas de repetição, utilize o longRetry. Se forem especificados repetição e longRetry, cada tentativa de longRetry inclui tentativas de repetição e o número máximo de tentativas é tentar * longRetry.<br/><br/>Por exemplo, se tivermos as seguintes definições na política de atividade:<br/>Repetir: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Suponha que existe apenas um setor para executar (estado está a aguardar) e a execução da atividade falha sempre. Inicialmente, deveria haver 3 tentativas de execução consecutivos. Após cada tentativa, o estado do setor seria repetição. Depois de primeiros 3 tentativas ao longo, o estado do setor seria LongRetry.<br/><br/>Depois de uma hora (ou seja, o valor do longRetryInteval), deveria haver outro conjunto de 3 tentativas de execução consecutivos. Depois disso, seria possível executar o estado do setor e não existem mais tentativas devem ser tentadas. Assim geral 6 foram feitas tentativas.<br/><br/>Se qualquer execução for bem-sucedida, o estado do setor seria prontos e não existem mais tentativas são tentadas.<br/><br/>longRetry pode ser usado em situações em que a chegada de dados dependentes em momentos não determinística ou o ambiente geral é instável em que o processamento de dados ocorre. Nesses casos, as repetições não um após o outro podem ajudar ao fazê-lo e fazê-lo após um intervalo de tempo resulta na saída desejada.<br/><br/>Nota de advertência: não definir valores altos para longRetry ou longRetryInterval. Normalmente, os valores mais altos implicam outros problemas sistêmicos. |
| longRetryInterval |TimeSpan |00:00:00 |O atraso entre tentativas de repetição longa |

### <a name="typeproperties-section"></a>secção typeProperties
A secção typeProperties é diferente para cada atividade. Atividades de transformação têm apenas as propriedades de tipo. Ver [atividades de transformação de dados](#data-transformation-activities) secção deste artigo para exemplos JSON que definem as atividades de transformação num pipeline.

**Atividade de cópia** tem duas subseções na secção typeProperties: **origem** e **sink**. Ver [ARQUIVOS de dados](#data-stores) secção neste artigo para exemplos JSON que mostram como usar um dados armazenam como uma origem de e/ou o sink.

### <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo seguinte, existe uma atividade do tipo **Cópia** na secção **activities**. Neste exemplo, o [atividade de cópia](data-factory-data-movement-activities.md) copia dados de um armazenamento de Blobs do Azure para uma base de dados SQL do Azure.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Tenha em atenção os seguintes pontos:

* Na secção atividades, existe apenas uma atividade cujo **type** está definido como **Copy**.
* A entrada da atividade está definida como **InputDataset** e a saída como **OutputDataset**.
* Na secção **typeProperties**, **BlobSource** está especificado como o tipo de origem e **SqlSink** como o tipo de sink.

Ver [ARQUIVOS de dados](#data-stores) secção neste artigo para exemplos JSON que mostram como usar um dados armazenam como uma origem de e/ou o sink.

Para obter instruções completas de criar este pipeline, veja [Tutorial: Copiar dados de armazenamento de BLOBs para base de dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplos
No pipeline de exemplo seguinte, existe uma atividade do tipo **HDInsightHive** na secção **activities**. Neste exemplo, a [atividade Hive do HDInsight](data-factory-hive-activity.md) transforma dados de um armazenamento de Blobs do Azure mediante a execução de um ficheiro de script de Hive num cluster do Hadoop para o Azure HDInsight.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Tenha em atenção os seguintes pontos:

* Na secção “activities”, existe apenas uma atividade cujo **type** está definido como **HDInsightHive**.
* O ficheiro do Script de ramo de registo **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.
* O **define** seção é usada para especificar as definições de tempo de execução que são transmitidas ao script do hive como valores de configuração do Hive (por exemplo, de `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Ver [atividades de transformação de dados](#data-transformation-activities) secção deste artigo para exemplos JSON que definem as atividades de transformação num pipeline.

Para obter instruções completas de criar este pipeline, veja [Tutorial: Crie seu primeiro pipeline para processar dados com o cluster do Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Serviço ligado
A estrutura de alto nível para uma definição de serviço ligado é o seguinte:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Seguinte tabela descreve as propriedades na definição JSON da atividade:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| name | Nome do serviço ligado. | Sim |
| Propriedades - tipo | Tipo de serviço ligado. Por exemplo: O armazenamento do Azure, base de dados SQL do Azure. |
| typeProperties | A secção typeProperties tem elementos que são diferentes para cada arquivo de dados ou ambiente de computação. Consulte a secção de armazenamentos de dados para todos os dados de serviços ligados de arquivo e [ambientes de computação](#compute-environments) para todos os a computação de serviços ligados |

## <a name="dataset"></a>Conjunto de dados
Um conjunto de dados no Azure Data Factory é definido da seguinte forma:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
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
| name | Nome do conjunto de dados. Ver [do Azure Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para regras de nomenclatura. |Sim |N/D |
| tipo | Tipo de conjunto de dados. Especifique um dos tipos suportados pelo Azure Data Factory (por exemplo: AzureBlob, AzureSqlTable). Ver [ARQUIVOS de dados](#data-stores) secção para todos os arquivos de dados e os tipos de conjunto de dados suportados pelo Data Factory. |
| estrutura | Esquema do conjunto de dados. Ela contém colunas, seus tipos, etc. | Não |N/D |
| typeProperties | Propriedades correspondentes ao tipo selecionado. Ver [ARQUIVOS de dados](#data-stores) na secção tipos suportados e as respetivas propriedades. |Sim |N/D |
| externo | Sinalizador booleano para especificar se um conjunto de dados é produzido explicitamente por um pipeline de fábrica de dados ou não. |Não |falso |
| disponibilidade | Define o período de processamento ou o modelo slicing para o conjunto de dados de produção. Para obter detalhes sobre o modelo de fragmentação do conjunto de dados, consulte [agendamento e execução](data-factory-scheduling-and-execution.md) artigo. |Sim |N/D |
| política |Define os critérios ou a condição que tem de preencher os setores do conjunto de dados. <br/><br/>Para obter detalhes, consulte a política de conjunto de dados de secção. |Não |N/D |

Cada coluna no **estrutura** seção contém as seguintes propriedades:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| name |Nome da coluna. |Sim |
| tipo |Tipo de dados da coluna.  |Não |
| cultura |.NET com base em cultura a ser utilizado quando o tipo especificado e é o tipo de .NET `Datetime` ou `Datetimeoffset`. A predefinição é `en-us`. |Não |
| Formato |Formatar a cadeia de caracteres a ser utilizado quando o tipo especificado e é o tipo de .NET `Datetime` ou `Datetimeoffset`. |Não |

No exemplo seguinte, o conjunto de dados tem três colunas `slicetimestamp`, `projectname`, e `pageviews` e são do tipo: Cadeia, cadeia de caracteres e Decimal, respetivamente.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A tabela seguinte descreve as propriedades que pode utilizar o **disponibilidade** secção:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| frequência |Especifica a unidade de tempo para produção do setor de conjunto de dados.<br/><br/><b>Suportado frequência</b>: Minuto, hora, dia, semana, mês |Sim |N/D |
| intervalo |Especifica um multiplicador para a frequência<br/><br/>"Intervalo de frequência x" determina a frequência com que o setor é produzido.<br/><br/>Se precisar do conjunto de dados para ser segmentadas numa base horária, defina <b>frequência</b> ao <b>hora</b>, e <b>intervalo</b> para <b>1</b>.<br/><br/><b>Nota</b>: Se especificar a frequência como minuto, recomendamos que defina o intervalo para não menos do que 15 |Sim |N/D |
| Estilo |Especifica se o setor deve ser gerado no início/fim do intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se a frequência está definida para o mês e estilo está definido como EndOfInterval, o setor é produzido no último dia do mês. Se o estilo é definido como StartOfInterval, o setor é produzido no primeiro dia do mês.<br/><br/>Se a frequência está definida como o dia e estilo está definido como EndOfInterval, o setor é produzido na última hora do dia.<br/><br/>Se a frequência está definida como hora e estilo está definido como EndOfInterval, o setor é produzido no fim da hora. Por exemplo, para um setor durante o período de 1 PM – 2 PM, o setor é produzido em 2 PM. |Não |EndOfInterval |
| anchorDateTime |Define a posição absoluta no tempo utilizado pelo agendador para computar os limites de setor de conjunto de dados. <br/><br/><b>Nota</b>: Se o AnchorDateTime tem partes de data que são mais granulares do que a frequência, em seguida, as partes mais granulares são ignoradas. <br/><br/>Por exemplo, se o <b>intervalo</b> é <b>por hora</b> (frequência: hora e intervalo: 1) e o <b>AnchorDateTime</b> contém <b>minutos e segundos</b> , em seguida, o <b>minutos e segundos</b> partes do AnchorDateTime são ignorados. |Não |01/01/0001 |
| offset |O período de tempo através do qual o início e de fim de todos os setores do conjunto de dados são mudou. <br/><br/><b>Nota</b>: Se forem especificados anchorDateTime e desvio, o resultado é a mudança combinada. |Não |N/D |

A secção de disponibilidade seguinte especifica que o conjunto de dados de saída é produzidos por hora (ou) entrada do conjunto de dados está disponível uma vez por hora:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

O **política** secção na definição do conjunto de dados define os critérios ou a condição que tem de preencher os setores do conjunto de dados.

| Nome da Política | Descrição | Aplicado a | Necessário | Predefinição |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que os dados num **BLOBs do Azure** cumpre os requisitos de tamanho mínimo (em megabytes). |Blob do Azure |Não |N/D |
| minimumRows |Valida que os dados num **base de dados SQL do Azure** ou uma **tabelas do Azure** contém o número mínimo de linhas. |<ul><li>Base de Dados SQL do Azure</li><li>Tabela do Azure</li></ul> |Não |N/D |

**Exemplo:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

A menos que um conjunto de dados está a ser produzido pelo Azure Data Factory, deverá ser marcado como **externo**. Esta definição geralmente aplica-se para as entradas da primeira atividade num pipeline, a menos que a atividade ou o encadeamento de pipeline está a ser utilizado.

| Name | Descrição | Necessário | Valor Predefinido |
| --- | --- | --- | --- |
| dataDelay |Tempo para atrasar a verificação da disponibilidade dos dados externos para o determinado setor. Por exemplo, se os dados estão disponíveis por hora, a verificação para ver os dados externos estão disponíveis e o setor correspondente está preparado para pode ser atrasada utilizando dataDelay.<br/><br/>Só se aplica a hora presente.  Por exemplo, caso seja 1 que, neste momento, e este valor é 10 minutos, a validação começa em 1:10 PM.<br/><br/>Esta definição não afeta os setores no passado (setores com a hora de fim do setor + dataDelay < agora) são processados sem demora.<br/><br/>Tempo superior a 23:59 tem de horas especificado usando a `day.hours:minutes:seconds` formato. Por exemplo, para especificar a 24 horas, não utilize 24:00:00; em alternativa, utilize 1.00:00:00. Se usar 24: 00:00, ela é tratada como 24 dias (24.00:00:00). Para 1 dia e quatro horas, especifique 1:04:00:00. |Não |0 |
| retryInterval |O tempo de espera entre uma falha e a próxima tentativa de repetição. Se uma tentativa falhar, repita seguinte é após retryInterval. <br/><br/>Se for 1 que, neste momento, começamos a primeira tentativa. Se a duração para concluir a primeira verificação de validação é de 1 minuto e a operação falhou, a próxima repetição é em 1:00 + 1 min (duração) + 1 min (intervalo de repetições) = 1:02 PM. <br/><br/>Para setores no passado, não existe nenhum atraso. A nova tentativa ocorre imediatamente. |Não |01: 00:00 (1 minuto) |
| retryTimeout |O tempo limite para cada tentativa de repetição.<br/><br/>Se esta propriedade é definida como 10 minutos, a validação precisa ser concluída em 10 minutos. Se precisar de mais de 10 minutos para efetuar a validação, a repetição exceder o tempo limite.<br/><br/>Se todas as tentativas para a validação exceder o tempo limite, o setor é marcado como excedido. |Não |10: 00:00 (10 minutos) |
| maximumRetry |Número de vezes para verificar a disponibilidade dos dados externos. O valor máximo permitido é de 10. |Não |3 |


## <a name="data-stores"></a>ARQUIVOS DE DADOS
O [serviço ligado](#linked-service) descrições de seção fornecida para elementos JSON que são comuns a todos os tipos de serviços ligados. Esta secção fornece detalhes sobre os elementos JSON que são específicos para cada arquivo de dados.

O [conjunto de dados](#dataset) descrições de seção fornecida para elementos JSON que são comuns a todos os tipos de conjuntos de dados. Esta secção fornece detalhes sobre os elementos JSON que são específicos para cada arquivo de dados.

O [atividade](#activity) descrições de seção fornecida para elementos JSON que são comuns a todos os tipos de atividades. Esta secção fornece detalhes sobre os elementos JSON que são específicos para cada arquivo de dados quando é utilizado como origem/sink na atividade de cópia.

Clique na ligação para o arquivo que estiver interessado em ver os esquemas JSON para o serviço ligado, o conjunto de dados e origem/sink para a atividade de cópia.

| Categoria | Arquivo de dados
|:--- |:--- |
| **Azure** |[Armazenamento de Blobs do Azure](#azure-blob-storage) |
| &nbsp; |Arquivo do Azure Data Lake |
| &nbsp; |[BD do Cosmos para o Azure](#azure-cosmos-db) |
| &nbsp; |[Base de Dados SQL do Azure](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Search](#azure-search) |
| &nbsp; |[Armazenamento de tabelas do Azure](#azure-table-storage) |
| **Bases de dados** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Ficheiro** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Sistema de Ficheiros](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Outros** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Tabela Web |

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

### <a name="linked-service"></a>Serviço ligado
Existem dois tipos de serviços ligados: O serviço ligado do armazenamento do Azure e serviço de SAS de armazenamento do Azure ligado.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para ligar a sua conta de armazenamento do Azure para uma fábrica de dados utilizando o **chave de conta**, criar um serviço ligado do armazenamento do Azure. Para definir um armazenamento do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureStorage**. Em seguida, pode especificar seguindo as propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| connectionString |Especifica as informações necessárias para ligar ao armazenamento do Azure para a propriedade connectionString. |Sim |

##### <a name="example"></a>Exemplo

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Serviço ligado do armazenamento Azure SAS
O serviço de SAS de armazenamento do Azure ligada permite-lhe ligar uma conta de armazenamento do Azure a uma fábrica de dados do Azure através de uma assinatura de acesso partilhado (SAS). Ele fornece a fábrica de dados com acesso restrito/com limite de tempo específico/todos os recursos (blob/contentor) no armazenamento. Para ligar a sua conta de armazenamento do Azure para uma fábrica de dados utilizando a assinatura de acesso partilhado, criar uma SAS de armazenamento do Azure serviço ligado. Para definir uma SAS de armazenamento do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureStorageSas**. Em seguida, pode especificar seguindo as propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| sasUri |Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento do Azure, como BLOBs, contentores ou tabela. |Sim |

##### <a name="example"></a>Exemplo

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Para obter mais informações sobre estes serviços ligados, consulte [conector de armazenamento de Blobs do Azure](data-factory-azure-blob-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de Blobs do Azure, defina o **tipo** do conjunto de dados para **AzureBlob**. Em seguida, especifique as propriedades específicas de Blobs do Azure seguintes na **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para o contentor e a pasta no armazenamento de Blobs. Exemplo: myblobcontainer\myblobfolder\ |Sim |
| fileName |Nome do blob. nome de ficheiro é opcional e diferencia maiúsculas de minúsculas.<br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no Blob específico.<br/><br/>Quando o nome de ficheiro não for especificada, a cópia inclui todos os Blobs no folderPath para conjunto de dados de entrada.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy propriedade é opcional. Pode usá-lo para especificar um folderPath dinâmico e o nome do ficheiro de dados de séries de tempo. Por exemplo, pode ser parametrizado folderPath por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example"></a>Exemplo

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


Para obter mais informações, consulte [conector de Blobs do Azure](data-factory-azure-blob-connector.md#dataset-properties) artigo.

### <a name="blobsource-in-copy-activity"></a>BlobSource na atividade de cópia
Se estiver a copiar dados de um armazenamento de Blobs do Azure, definir o **tipo de origem** da atividade de cópia para **BlobSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |False a TRUE (valor predefinido), |Não |

#### <a name="example-blobsource"></a>Exemplo: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink na atividade de cópia
Se estiver a copiar dados para um armazenamento de Blobs do Azure, definir o **tipo de sink** da atividade copy na atividade **BlobSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou sistema de ficheiros. |<b>PreserveHierarchy</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem estão no primeiro nível de pasta de destino. Os ficheiros de destino têm o nome gerado automaticamente. <br/><br/><b>MergeFiles (predefinição):</b> une todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro/Blob for especificado, o nome de ficheiro intercalada seria o nome especificado; caso contrário, seria o nome de ficheiro gerado automaticamente. |Não |

#### <a name="example-blobsink"></a>Exemplo: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector de Blobs do Azure](data-factory-azure-blob-connector.md#copy-activity-properties) artigo.

## <a name="azure-data-lake-store"></a>Arquivo do Azure Data Lake

### <a name="linked-service"></a>Serviço ligado
Para definir um Store do Azure Data Lake serviço ligado, defina o tipo do serviço ligado ao **AzureDataLakeStore**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida como: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifique informações sobre a conta do Azure Data Lake Store. É no seguinte formato: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | Id de subscrição do Azure à qual pertence a Data Lake Store. | Necessário para o sink |
| resourceGroupName | Nome do grupo de recursos do Azure à qual pertence a Data Lake Store. | Necessário para o sink |
| servicePrincipalId | Especifique o ID de cliente. da aplicação | Sim (para autenticação do principal de serviço) |
| servicePrincipalKey | Especifique a chave da aplicação. | Sim (para autenticação do principal de serviço) |
| inquilino | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim (para autenticação do principal de serviço) |
| Autorização | Clique em **autorizar** botão no **Editor do Data Factory** e introduza a sua credencial que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim (para autenticação de credenciais de utilizador)|
| sessionId | Id de sessão OAuth a partir da sessão de autorização de OAuth. Cada id de sessão é exclusivo e só pode ser utilizada uma vez. Esta definição é gerada automaticamente ao utilizar o Editor do Data Factory. | Sim (para autenticação de credenciais de utilizador) |

#### <a name="example-using-service-principal-authentication"></a>Exemplo: utilizar a autenticação principal de serviço
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Exemplo: utilizar a autenticação de credenciais de utilizador
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Para obter mais informações, consulte [conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure Data Lake Store, defina o **tipo** do conjunto de dados para **AzureDataLakeStore**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| folderPath |Caminho para o contentor e a pasta no Azure Data Lake armazenar. |Sim |
| fileName |Nome do ficheiro no armazenamento do Azure Data Lake. nome de ficheiro é opcional e diferencia maiúsculas de minúsculas. <br/><br/>Se especificar um nome de ficheiro, a atividade (incluindo cópia) funciona no ficheiro específico.<br/><br/>Quando o nome de ficheiro não for especificada, cópia inclui todos os ficheiros em folderPath para conjunto de dados de entrada.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy propriedade é opcional. Pode usá-lo para especificar um folderPath dinâmico e o nome do ficheiro de dados de séries de tempo. Por exemplo, pode ser parametrizado folderPath por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
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

Para obter mais informações, consulte [conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties) artigo.

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Origem de Azure Data Lake Store na atividade de cópia
Se está a copiar dados de um Store do Azure Data Lake, definir o **tipo de origem** da atividade copy na atividade **AzureDataLakeStoreSource**e especifique os seguintes propriedades no **origem**secção:

**AzureDataLakeStoreSource** suporta as seguintes propriedades **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |False a TRUE (valor predefinido), |Não |

#### <a name="example-azuredatalakestoresource"></a>Exemplo: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) artigo.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store Sink na atividade de cópia
Se estiver a copiar dados para um Store do Azure Data Lake, definir o **tipo de sink** da atividade copy na atividade **um AzureDataLakeStoreSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Especifica o comportamento de cópia. |<b>PreserveHierarchy</b>: preserva a hierarquia de ficheiros na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico para o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/><b>FlattenHierarchy</b>: todos os ficheiros da pasta de origem são criados no primeiro nível de pasta de destino. Os ficheiros de destino são criados com o nome gerado automaticamente.<br/><br/><b>MergeFiles</b>: une todos os ficheiros da pasta de origem para um ficheiro. Se o nome de ficheiro/Blob for especificado, o nome de ficheiro intercalada seria o nome especificado; caso contrário, seria o nome de ficheiro gerado automaticamente. |Não |

#### <a name="example-azuredatalakestoresink"></a>Exemplo: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector do Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties) artigo.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Serviço ligado
Para definir um Azure Cosmos DB de serviço ligado, defina o **tipo** do serviço ligado para **DocumentDb**e especifique os seguintes propriedades no **typeProperties** secção:

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| connectionString |Especifica as informações necessárias para ligar à base de dados do Azure Cosmos DB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Para obter mais informações, consulte [conector do Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure Cosmos DB, defina o **tipo** do conjunto de dados para **DocumentDbCollection**e especifique as seguintes propriedades no **typeProperties** secção:

| **Propriedade** | **Descrição** | **Necessário** |
| --- | --- | --- |
| CollectionName |Nome da coleção do Azure Cosmos DB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Para obter mais informações, consulte [conector do Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties) artigo.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Origem da coleção do Azure Cosmos DB na atividade de cópia
Se estiver a copiar dados do Azure Cosmos DB, definir o **tipo de origem** da atividade de cópia para **DocumentDbCollectionSource**e especifique os seguintes propriedades no **origem**secção:


| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| consulta |Especifique a consulta para ler os dados. |Suportado pelo Azure Cosmos DB de cadeia de consulta. <br/><br/>Exemplo: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Não <br/><br/>Se não for especificado, a instrução SQL que é executada: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Caráter especial para indicar que o documento está aninhado |Qualquer caráter. <br/><br/>O Azure Cosmos DB é um arquivo de NoSQL para documentos JSON, onde as estruturas aninhadas são permitidas. O Azure Data Factory permite que o utilizador denotar hierarquia via nestingSeparator, que é "." nos exemplos acima. Com o separador, a atividade de cópia irá gerar o objeto "Nome" com elementos de três filhos em primeiro lugar, média e por último, a fim de acordo com "Name.First", "Name.Middle" e "Name.Last" na definição da tabela. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB coleção Sink na atividade de cópia
Se estiver a copiar dados para o Azure Cosmos DB, definir o **tipo de sink** da atividade copy na atividade **DocumentDbCollectionSink**e especifique os seguintes propriedades no **sink** secção :

| **Propriedade** | **Descrição** | **Valores permitidos** | **Necessário** |
| --- | --- | --- | --- |
| nestingSeparator |É necessário um caráter especial no nome da coluna de origem para indicar esse documento aninhado. <br/><br/>Por exemplo acima: `Name.First` na saída da tabela produz a seguinte estrutura JSON no documento do Cosmos DB:<br/><br/>"Name": {<br/>    "First": "João"<br/>}, |Caráter utilizado para separar níveis de aninhamento.<br/><br/>Valor predefinido é `.` (ponto). |Caráter utilizado para separar níveis de aninhamento. <br/><br/>Valor predefinido é `.` (ponto). |
| writeBatchSize |Número de pedidos paralelos para o serviço do Azure Cosmos DB para criar documentos.<br/><br/>Pode ajustar o desempenho ao copiar dados de/para Azure Cosmos DB ao utilizar esta propriedade. Pode esperar um melhor desempenho quando aumenta writeBatchSize porque são enviados os pedidos paralelos mais ao Azure Cosmos DB. No entanto, terá de evitar a limitação que pode gerar a mensagem de erro: "Taxa de pedidos é grande".<br/><br/>A limitação é decidida por um número de fatores, incluindo o tamanho de documentos, o número de termos em documentos, a indexação de política de coleção de destino, etc. Para operações de cópia, pode utilizar uma coleção de melhor (por exemplo, S3) que tenha o máximo débito disponível (pedido de 2500 unidades por segundo). |Número Inteiro |Não (predefinição: 5) |
| writeBatchTimeout |Tempo para a operação seja concluída antes de atingir o tempo limite de espera. |Período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Para obter mais informações, consulte [conector do Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties) artigo.

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

### <a name="linked-service"></a>Serviço ligado
Para definir uma base de dados do SQL do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureSqlDatabase**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para ligar à instância do SQL Database do Azure para a propriedade connectionString. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte [conector do Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SQL Database do Azure, defina o **tipo** do conjunto de dados para **AzureSqlTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância da base de dados do Azure SQL pelo serviço ligado refere-se. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
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
Para obter mais informações, consulte [conector do Azure SQL](data-factory-azure-sql-connector.md#dataset-properties) artigo.

### <a name="sql-source-in-copy-activity"></a>Origem SQL na atividade de cópia
Se está a copiar dados de uma base de dados do SQL do Azure, definir o **tipo de origem** da atividade copy na atividade **SqlSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte [conector do Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) artigo.

### <a name="sql-sink-in-copy-activity"></a>SQL Sink na atividade de cópia
Se estiver a copiar dados para a base de dados do Azure SQL, definir o **tipo de sink** da atividade copy na atividade **SqlSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera. |Período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| sqlWriterCleanupScript |Especifica uma consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpo. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique um nome de coluna para a atividade de cópia preencher com o identificador de setor gerado automaticamente, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que upserts (inserções/atualizações) os dados para a tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Especifique um nome de tipo de tabela a ser utilizado no procedimento armazenado. Atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados a ser copiados com os dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector do Azure SQL](data-factory-azure-sql-connector.md#copy-activity-properties) artigo.

## <a name="azure-sql-data-warehouse"></a>Armazém de Dados SQL do Azure

### <a name="linked-service"></a>Serviço ligado
Para definir um Azure SQL Data Warehouse de serviço ligado, defina o **tipo** do serviço ligado para **AzureSqlDW**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para ligar à instância do armazém de dados SQL do Azure para a propriedade connectionString. |Sim |



#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte [conector do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure SQL Data Warehouse, defina o **tipo** do conjunto de dados para **AzureSqlDWTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista no banco de dados Azure SQL Data Warehouse que o serviço ligado refere-se a. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
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

Para obter mais informações, consulte [conector do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) artigo.

### <a name="sql-dw-source-in-copy-activity"></a>Origem do armazém de dados SQL na atividade de cópia
Se estiver a copiar dados do Azure SQL Data Warehouse, definir o **tipo de origem** da atividade copy na atividade **SqlDWSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artigo.

### <a name="sql-dw-sink-in-copy-activity"></a>Armazém de dados de SQL Sink na atividade de cópia
Se estiver a copiar dados para o Azure SQL Data Warehouse, definir o **tipo de sink** da atividade copy na atividade **SqlDWSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Especifica uma consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpo. |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se deve utilizar o PolyBase (quando aplicável), em vez de mecanismo BULKINSERT. <br/><br/> **Com o PolyBase é a forma recomendada para carregar dados para o SQL Data Warehouse.** |Verdadeiro <br/>FALSE (predefinição) |Não |
| polyBaseSettings |Um grupo de propriedades que pode ser especificada quando o **allowPolybase** estiver definida como **verdadeiro**. |&nbsp; |Não |
| rejectValue |Especifica o número ou porcentagem das linhas que pode ser rejeitada antes da consulta falha. <br/><br/>Saiba mais sobre as opções de rejeição do PolyBase no **argumentos** secção [criar tabela externa (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) tópico. |0 (predefinição), 1, 2,... |Não |
| rejectType |Especifica se a opção de rejectValue é especificada como um valor literal ou uma percentagem. |Valor de percentagem (predefinição), |Não |
| rejectSampleValue |Determina o número de linhas para obter antes do PolyBase recalcula a porcentagem das linhas rejeitadas. |1, 2, … |Sim, se **rejectType** é **percentagem** |
| useTypeDefault |Especifica como lidar com valores em falta nos ficheiros de texto delimitado quando PolyBase obtém dados a partir do ficheiro de texto.<br/><br/>Saiba mais sobre esta propriedade da secção argumentos na [criar ficheiro de formato externo (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |TRUE, False (predefinição) |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera. |Período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artigo.

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado, defina o **tipo** do serviço ligado para **AzureSearch**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| url | URL para o serviço Azure Search. | Sim |
| chave | Chave de administrador para o serviço Azure Search. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Para obter mais informações, consulte [conector do Azure Search](data-factory-azure-search-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Azure Search, defina o **tipo** do conjunto de dados para **AzureSearchIndex**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| tipo | A propriedade de tipo deve ser definida como **AzureSearchIndex**.| Sim |
| indexName | Nome do índice da Azure Search. Fábrica de dados não cria o índice. O índice tem de existir no Azure Search. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Para obter mais informações, consulte [conector do Azure Search](data-factory-azure-search-connector.md#dataset-properties) artigo.

### <a name="azure-search-index-sink-in-copy-activity"></a>O Azure Search Index Sink na atividade de cópia
Se estiver a copiar dados para um índice da Azure Search, definir o **tipo de sink** da atividade copy na atividade **AzureSearchIndexSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Especifica se deve intercalar ou substituir quando um documento já existe no índice. | Intercalar (predefinição)<br/>Carregar| Não |
| WriteBatchSize | Carrega dados para o índice da Azure Search, quando o tamanho do buffer atinge writeBatchSize. | 1 a 1000. Valor predefinido é 1000. | Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector do Azure Search](data-factory-azure-search-connector.md#copy-activity-properties) artigo.

## <a name="azure-table-storage"></a>Armazenamento de Tabelas do Azure

### <a name="linked-service"></a>Serviço ligado
Existem dois tipos de serviços ligados: O serviço ligado do armazenamento do Azure e serviço de SAS de armazenamento do Azure ligado.

#### <a name="azure-storage-linked-service"></a>Serviço Ligado do Storage do Azure
Para ligar a sua conta de armazenamento do Azure para uma fábrica de dados utilizando o **chave de conta**, criar um serviço ligado do armazenamento do Azure. Para definir um armazenamento do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureStorage**. Em seguida, pode especificar seguindo as propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida como: **AzureStorage** |Sim |
| connectionString |Especifica as informações necessárias para ligar ao armazenamento do Azure para a propriedade connectionString. |Sim |

**Exemplo:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Serviço ligado do armazenamento Azure SAS
O serviço de SAS de armazenamento do Azure ligada permite-lhe ligar uma conta de armazenamento do Azure a uma fábrica de dados do Azure através de uma assinatura de acesso partilhado (SAS). Ele fornece a fábrica de dados com acesso restrito/com limite de tempo específico/todos os recursos (blob/contentor) no armazenamento. Para ligar a sua conta de armazenamento do Azure para uma fábrica de dados utilizando a assinatura de acesso partilhado, criar uma SAS de armazenamento do Azure serviço ligado. Para definir uma SAS de armazenamento do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureStorageSas**. Em seguida, pode especificar seguindo as propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |A propriedade de tipo tem de ser definida como: **AzureStorageSas** |Sim |
| sasUri |Especifique o URI de assinatura de acesso partilhado para os recursos de armazenamento do Azure, como BLOBs, contentores ou tabela. |Sim |

**Exemplo:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Para obter mais informações sobre estes serviços ligados, consulte [conector de armazenamento de tabelas do Azure](data-factory-azure-table-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados de tabelas do Azure, defina o **tipo** do conjunto de dados para **Azuretable{0}name**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados do Azure tabela pelo serviço ligado refere-se. |Sim. Quando um tableName é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se um azureTableSourceQuery também for especificado, os registos da tabela que satisfaz a consulta são copiados para o destino. |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
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

Para obter mais informações sobre estes serviços ligados, consulte [conector de armazenamento de tabelas do Azure](data-factory-azure-table-connector.md#dataset-properties) artigo.

### <a name="azure-table-source-in-copy-activity"></a>Origem de tabela do Azure na atividade de cópia
Se está a copiar dados de armazenamento de tabelas do Azure, definir o **tipo de origem** da atividade copy na atividade **AzureTableSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableSourceQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta de tabela do Azure. Veja exemplos na próxima seção. |Não. Quando um tableName é especificado sem um azureTableSourceQuery, todos os registos da tabela são copiados para o destino. Se um azureTableSourceQuery também for especificado, os registos da tabela que satisfaz a consulta são copiados para o destino. |
| azureTableSourceIgnoreTableNotFound |Indica se assimilar a exceção da tabela não existe. |VERDADEIRO<br/>FALSO |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações sobre estes serviços ligados, consulte [conector de armazenamento de tabelas do Azure](data-factory-azure-table-connector.md#copy-activity-properties) artigo.

### <a name="azure-table-sink-in-copy-activity"></a>Tabela do Azure de Sink na atividade de cópia
Se estiver a copiar dados para armazenamento de tabelas do Azure, definir o **tipo de sink** da atividade copy na atividade **AzureTableSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Predefinição partição valor da chave que pode ser utilizado pelo sink. |Um valor de cadeia de caracteres. |Não |
| azureTablePartitionKeyName |Especifique o nome da coluna cujos valores são utilizados como as chaves de partição. Se não for especificado, AzureTableDefaultPartitionKeyValue é utilizado como a chave de partição. |Um nome de coluna. |Não |
| azureTableRowKeyName |Especifique o nome da coluna cujos valores de coluna são utilizados como chave de linha. Se não for especificado, utilize um GUID para cada linha. |Um nome de coluna. |Não |
| azureTableInsertType |O modo de inserir dados na tabela do Azure.<br/><br/>Esta propriedade controla se as linhas existentes na tabela de saída com correspondentes chaves de partição e de linha têm seus valores substituído ou intercalado. <br/><br/>Para saber mais sobre o funcionamento destas definições (intercalação e substituir), veja [Insert ou entidade de intercalação](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Insert ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) tópicos. <br/><br> Esta definição aplica-se ao nível da linha, não é o nível de tabela, e nenhuma dessas opções elimina linhas da tabela de saída que não existem na entrada. |Intercalar (predefinição)<br/>substituir |Não |
| writeBatchSize |Insere dados na tabela do Azure quando for atingido o writeBatchSize ou writeBatchTimeout. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| writeBatchTimeout |Insere dados na tabela do Azure quando for atingido o writeBatchSize ou writeBatchTimeout |Período de tempo<br/><br/>Exemplo: "00: 20:00" (20 minutos) |Não (seg 90 de valor predefinido para o tempo limite de padrão de cliente de armazenamento) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações sobre estes serviços ligados, consulte [conector de armazenamento de tabelas do Azure](data-factory-azure-table-connector.md#copy-activity-properties) artigo.

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Serviço ligado
Para definir um Amazon Redshift serviço ligado, defina o **tipo** do serviço ligado para **AmazonRedshift**e especifique os seguintes propriedades no **typeProperties** secção :

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome anfitrião ou endereço IP do servidor do Amazon Redshift. |Sim |
| porta |O número da porta TCP que o servidor do Amazon Redshift utiliza para escutar ligações de cliente. |Não, o valor predefinido: 5439 |
| base de dados |Nome da base de dados do Amazon Redshift. |Sim |
| nome do utilizador |Nome de utilizador que tenha acesso à base de dados. |Sim |
| palavra-passe |Palavra-passe da conta de utilizador. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Para obter mais informações, consulte o artigo de conector Amazon Redshift.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Amazon Redshift, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na base de dados do Amazon Redshift pelo serviço ligado refere-se. |Não (se **consulta** dos **RelationalSource** for especificado) |


#### <a name="example"></a>Exemplo

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte o artigo de conector Amazon Redshift.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados do Amazon Redshift, definir o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** dos **conjunto de dados** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Para obter mais informações, consulte o artigo de conector Amazon Redshift.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Serviço ligado
Para definir o IBM DB2 serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesDB2**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor DB2. |Sim |
| base de dados |Nome da base de dados DB2. |Sim |
| schema |Nome do esquema na base de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados DB2. Os valores possíveis são: Anónimo, básico e Windows. |Sim |
| nome do utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados de DB2 no local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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
Para obter mais informações, consulte o artigo de conector de DB2 da IBM.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados DB2, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados DB2 pelo serviço ligado refere-se. O tableName diferencia maiúsculas de minúsculas. |Não (se **consulta** dos **RelationalSource** for especificado)

#### <a name="example"></a>Exemplo
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

Para obter mais informações, consulte o artigo de conector de DB2 da IBM.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados do IBM DB2, definir o **tipo de origem** da atividade de cópia para **RelationalSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. |Não (se **tableName** dos **conjunto de dados** for especificado) |

#### <a name="example"></a>Exemplo
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Para obter mais informações, consulte o artigo de conector de DB2 da IBM.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Serviço ligado
Para definir um MySQL de serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesMySql**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor MySQL. |Sim |
| base de dados |Nome da base de dados MySQL. |Sim |
| schema |Nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados MySQL. Os valores possíveis são: `Basic`. |Sim |
| nome do utilizador |Especifique o nome de utilizador para ligar à base de dados MySQL. |Sim |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou. |Sim |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do MySQL no local. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Para obter mais informações, consulte [conector de MySQL](data-factory-onprem-mysql-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do MySQL, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados MySQL pelo serviço ligado refere-se. |Não (se **consulta** dos **RelationalSource** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
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
Para obter mais informações, consulte [conector de MySQL](data-factory-onprem-mysql-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se está a copiar dados de uma base de dados do MySQL, defina o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** dos **conjunto de dados** for especificado) |


#### <a name="example"></a>Exemplo
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector de MySQL](data-factory-onprem-mysql-connector.md#copy-activity-properties) artigo.

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Serviço ligado
Para definir uma Oracle serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesOracle**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| driverType | Especifique qual driver para utilizar para copiar dados de/para a base de dados Oracle. Valores permitidos são **Microsoft** ou **ODP** (predefinição). Consulte a secção de instalação e a versão de suportados em detalhes do controlador. | Não |
| connectionString | Especifica as informações necessárias para ligar à instância de base de dados Oracle para a propriedade connectionString. | Sim |
| gatewayName | Nome do gateway que é utilizado para ligar ao servidor Oracle no local |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte [conector Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Oracle, defina o **tipo** do conjunto de dados para **OracleTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na base de dados Oracle que o serviço ligado refere-se a. |Não (se **oracleReaderQuery** dos **OracleSource** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
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
Para obter mais informações, consulte [conector Oracle](data-factory-onprem-oracle-connector.md#dataset-properties) artigo.

### <a name="oracle-source-in-copy-activity"></a>Oracle origem na atividade de cópia
Se está a copiar dados de uma base de dados do Oracle, defina o **tipo de origem** da atividade copy na atividade **OracleSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| oracleReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable` <br/><br/>Se não for especificado, a instrução SQL que é executada: `select * from MyTable` |Não (se **tableName** dos **conjunto de dados** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) artigo.

### <a name="oracle-sink-in-copy-activity"></a>Oracle Sink na atividade de cópia
Se estiver a copiar dados para am base de dados Oracle, definir o **tipo de sink** da atividade copy na atividade **OracleSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera. |Período de tempo<br/><br/> Exemplo: 30: 00:00 (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 100) |
| sqlWriterCleanupScript |Especifica uma consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpo. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia preencher com o identificador de setor gerado automaticamente, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |

#### <a name="example"></a>Exemplo
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte [conector Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties) artigo.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Serviço ligado
Para definir um PostgreSQL serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesPostgreSql**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor PostgreSQL. |Sim |
| base de dados |Nome da base de dados PostgreSQL. |Sim |
| schema |Nome do esquema na base de dados. O nome do esquema diferencia maiúsculas de minúsculas. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados PostgreSQL. Os valores possíveis são: Anónimo, básico e Windows. |Sim |
| nome do utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do PostgreSQL no local. |Sim |

#### <a name="example"></a>Exemplo

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
Para obter mais informações, consulte [conector de PostgreSQL](data-factory-onprem-postgresql-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do PostgreSQL, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados PostgreSQL pelo serviço ligado refere-se. O tableName diferencia maiúsculas de minúsculas. |Não (se **consulta** dos **RelationalSource** for especificado) |

#### <a name="example"></a>Exemplo
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
Para obter mais informações, consulte [conector de PostgreSQL](data-factory-onprem-postgresql-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se está a copiar dados de uma base de dados do PostgreSQL, defina o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: "consulta": "selecionar * da \"MySchema\".\" MyTable\"". |Não (se **tableName** dos **conjunto de dados** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
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
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector de PostgreSQL](data-factory-onprem-postgresql-connector.md#copy-activity-properties) artigo.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Serviço ligado
Para definir um SAP Business Warehouse (BW) serviço de ligado, defina o **tipo** do serviço ligado para **SapBw**e especifique os seguintes propriedades no **typeProperties** secção :

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância do SAP BW. | string | Sim
systemNumber | Número de sistema do sistema SAP BW. | Número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim
clientId | ID de cliente do cliente no sistema SAP W. | Número decimal de três dígitos representado como uma cadeia de caracteres. | Sim
nome do utilizador | Nome de utilizador que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
gatewayName | Nome do gateway que o serviço Data Factory deve utilizar para ligar à instância de SAP BW no local. | string | Sim
encryptedCredential | A cadeia de credencial encriptada. | string | Não

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte [conector SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SAP BW, defina o **tipo** do conjunto de dados para **RelationalTable**. Não há específicos do tipo propriedades suportados para o conjunto de dados do SAP BW typu **RelationalTable**.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte [conector SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados do SAP Business Warehouse, defina o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta MDX para ler dados a partir da instância do SAP BW. | Consulta MDX. | Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) artigo.

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Serviço ligado
Para definir um SAP HANA de serviço ligado, defina o **tipo** do serviço ligado para **SapHana**e especifique os seguintes propriedades no **typeProperties** secção:

Propriedade | Descrição | Valores permitidos | Necessário
-------- | ----------- | -------------- | --------
servidor | Nome do servidor no qual reside a instância do SAP HANA. Se o servidor estiver a utilizar uma porta personalizada, especifique `server:port`. | string | Sim
authenticationType | Tipo de autenticação. | cadeia de caracteres. "Básico" ou "Windows" | Sim
nome do utilizador | Nome de utilizador que tem acesso ao servidor SAP | string | Sim
palavra-passe | A palavra-passe do utilizador. | string | Sim
gatewayName | Nome do gateway que o serviço Data Factory deve utilizar para ligar à instância de SAP HANA no local. | string | Sim
encryptedCredential | A cadeia de credencial encriptada. | string | Não

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Para obter mais informações, consulte [conector do SAP HANA](data-factory-sap-hana-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SAP HANA, defina o **tipo** do conjunto de dados para **RelationalTable**. Não há específicos do tipo propriedades suportados para o conjunto de dados do SAP HANA typu **RelationalTable**.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Para obter mais informações, consulte [conector do SAP HANA](data-factory-sap-hana-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de um arquivo de dados do SAP HANA, defina o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta | Especifica a consulta SQL para ler dados a partir da instância do SAP HANA. | Consulta SQL. | Sim |


#### <a name="example"></a>Exemplo


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector do SAP HANA](data-factory-sap-hana-connector.md#copy-activity-properties) artigo.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Serviço ligado
Vai criar um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server no local a uma fábrica de dados. A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço de ligado do SQL Server no local.

A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do SQL Server.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para se ligar à base de dados de SQL Server no local, utilizando a autenticação SQL ou autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| nome do utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação do Windows. Exemplo: **domainname\\nome de utilizador**. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |

Pode criptografar as credenciais com o **New-AzDataFactoryEncryptValue** cmdlet e utilizá-los na cadeia de ligação, conforme mostrado no exemplo a seguir (**EncryptedCredential** propriedade):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para utilizar a autenticação de SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para utilizar a autenticação do Windows

Se o nome de utilizador e palavra-passe forem especificados, o gateway utiliza-os para representar a conta de utilizador especificado para ligar à base de dados do SQL Server no local. Caso contrário, o gateway liga para o SQL Server diretamente com o contexto de segurança do Gateway (sua conta de arranque).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SQL Server, defina o **tipo** do conjunto de dados para **SqlServerTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela ou vista na instância da base de dados do SQL Server pelo serviço ligado refere-se. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
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

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#dataset-properties) artigo.

### <a name="sql-source-in-copy-activity"></a>Origem de SQL na atividade de cópia
Se está a copiar dados de uma base de dados do SQL Server, defina o **tipo de origem** da atividade copy na atividade **SqlSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| sqlReaderQuery |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. Pode fazer referência a várias tabelas da base de dados referenciado pelo conjunto de dados de entrada. Se não for especificado, a instrução SQL que é executada: selecione a partir dos MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |

Se o **sqlReaderQuery** é especificado para o SqlSource, a atividade de cópia executa esta consulta em relação à origem de base de dados do SQL Server para obter os dados.

Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura são utilizadas para criar uma consulta select para executar na base de dados do SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

> [!NOTE]
> Quando utiliza **sqlReaderStoredProcedureName**, terá de especificar um valor para o **tableName** propriedade no conjunto de dados JSON. Não há nenhuma validação executada nessa tabela, no entanto.


#### <a name="example"></a>Exemplo
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Neste exemplo, **sqlReaderQuery** especificado para o SqlSource. A atividade de cópia executa esta consulta em relação à origem de base de dados do SQL Server para obter os dados. Em alternativa, pode especificar um procedimento armazenado, especificando o **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado recebe parâmetros). O sqlReaderQuery pode fazer referência a várias tabelas na base de dados referenciados pelo conjunto de dados de entrada. Não se limita a apenas a tabela definir como typeProperty de tableName o conjunto de dados.

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na secção de estrutura são utilizadas para criar uma consulta select para executar na base de dados do SQL Server. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela.

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) artigo.

### <a name="sql-sink-in-copy-activity"></a>SQL Sink na atividade de cópia
Se estiver a copiar dados para uma base de dados do SQL Server, defina o **tipo de sink** da atividade copy na atividade **SqlSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo para a operação de inserção de lote seja concluída antes de atingir o tempo limite de espera. |Período de tempo<br/><br/> Exemplo: "00: 30:00" (30 minutos). |Não |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize. |Número inteiro (número de linhas) |Não (predefinição: 10000) |
| sqlWriterCleanupScript |Especifique a consulta para a atividade de cópia executar de forma a que os dados de um setor específico é limpo. Para obter mais informações, consulte a secção de capacidade de repetição. |Uma instrução de consulta. |Não |
| sliceIdentifierColumnName |Especifique o nome da coluna para a atividade de cópia preencher com o identificador de setor gerado automaticamente, o que é utilizado para limpar os dados de um setor específico quando voltar a executar. Para obter mais informações, consulte a secção de capacidade de repetição. |Nome da coluna de uma coluna com o tipo de dados de binary(32). |Não |
| sqlWriterStoredProcedureName |Nome do procedimento armazenado que upserts (inserções/atualizações) os dados para a tabela de destino. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros do procedimento armazenado. |Pares de nome/valor. Os nomes e tem maiúsculas e minúsculas de parâmetros têm de corresponder os nomes e os parâmetros do procedimento armazenado letras maiúsculas e minúsculas. |Não |
| sqlWriterTableType |Especifique o nome de tipo de tabela para ser utilizado no procedimento armazenado. Atividade de cópia torna os dados que está a ser movidos disponíveis numa tabela temporária com este tipo de tabela. Código do procedimento armazenado pode então mesclar os dados a ser copiados com os dados existentes. |Um nome de tipo de tabela. |Não |

#### <a name="example"></a>Exemplo
O pipeline contém uma atividade de cópia que está configurado para utilizar estes conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **BlobSource** e **sink** tipo está definido como **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties) artigo.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Serviço ligado
Para definir um Sybase serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesSybase**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor Sybase. |Sim |
| base de dados |Nome da base de dados Sybase. |Sim |
| schema |Nome do esquema na base de dados. |Não |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados Sybase. Os valores possíveis são: Anónimo, básico e Windows. |Sim |
| nome do utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados de Sybase no local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
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

Para obter mais informações, consulte [conector de Sybase](data-factory-onprem-sybase-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados Sybase, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela na instância da base de dados Sybase pelo serviço ligado refere-se. |Não (se **consulta** dos **RelationalSource** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
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

Para obter mais informações, consulte [conector de Sybase](data-factory-onprem-sybase-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se está a copiar dados de uma base de dados Sybase, defina o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção :


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Não (se **tableName** dos **conjunto de dados** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector de Sybase](data-factory-onprem-sybase-connector.md#copy-activity-properties) artigo.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Serviço ligado
Para definir um Teradata serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesTeradata**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome do servidor de Teradata. |Sim |
| authenticationType |Tipo de autenticação utilizado para ligar à base de dados Teradata. Os valores possíveis são: Anónimo, básico e Windows. |Sim |
| nome do utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica ou do Windows. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do Teradata no local. |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Para obter mais informações, consulte [conector de Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Blob de Teradata, defina o **tipo** do conjunto de dados para **RelationalTable**. Atualmente, não há nenhuma propriedade de tipo suportada para o conjunto de dados Teradata.

#### <a name="example"></a>Exemplo
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
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

Para obter mais informações, consulte [conector de Teradata](data-factory-onprem-teradata-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se está a copiar dados de uma base de dados Teradata, defina o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte [conector de Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties) artigo.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Serviço ligado
Para definir um serviço ligado do Cassandra, defina o **tipo** do serviço ligado para **OnPremisesCassandra**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| anfitrião |Um ou mais endereços IP ou nomes de anfitrião dos servidores de Cassandra.<br/><br/>Especifica uma lista separada por vírgulas de endereços IP ou nomes de anfitrião para ligar a todos os servidores em simultâneo. |Sim |
| porta |A porta TCP que o servidor Cassandra utiliza para escutar ligações de cliente. |Não, o valor predefinido: 9042 |
| authenticationType |Básico ou anónimo |Sim |
| nome do utilizador |Especifique o nome de utilizador para a conta de utilizador. |Sim, se authenticationType está definido para básico. |
| palavra-passe |Especifique a palavra-passe da conta de utilizador. |Sim, se authenticationType está definido para básico. |
| gatewayName |O nome do gateway que é utilizado para ligar à base de dados do Cassandra no local. |Sim |
| encryptedCredential |Credencial encriptada pelo gateway. |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [conector de Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Cassandra, defina o **tipo** do conjunto de dados para **CassandraTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| keyspace |Nome do keyspace ou esquema na base de dados do Cassandra. |Sim (se **consulta** para **CassandraSource** não está definido). |
| tableName |Nome da tabela na base de dados do Cassandra. |Sim (se **consulta** para **CassandraSource** não está definido). |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
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

Para obter mais informações, consulte [conector de Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties) artigo.

### <a name="cassandra-source-in-copy-activity"></a>Origem de Cassandra na atividade de cópia
Se estiver a copiar dados do Cassandra, definir o **tipo de origem** da atividade copy na atividade **CassandraSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Consulta de SQL-92 ou consulta CQL. Ver [referência CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Ao utilizar a consulta SQL, especifique **keyspace name.table nome** para representar a tabela que pretende consultar. |Não (se for tableName e keyspace num conjunto de dados estão definidos). |
| consistencyLevel |O nível de consistência Especifica o número de réplicas devem responder a uma solicitação de leitura antes de retornar dados para a aplicação cliente. Cassandra verifica o número especificado de réplicas de dados satisfazer a solicitação de leitura. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Ver [configurar a consistência dos dados](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) para obter detalhes. |Não. Valor predefinido é um. |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector de Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties) artigo.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Serviço ligado
Para definir uma MongoDB de serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesMongoDB**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| servidor |Nome anfitrião ou endereço IP do servidor do MongoDB. |Sim |
| porta |Porta TCP que o servidor do MongoDB utiliza para escutar ligações de cliente. |Opcional, valor de predefinido: 27017 |
| authenticationType |Básico ou anónimo. |Sim |
| nome do utilizador |Conta de utilizador para aceder a MongoDB. |Sim (se for utilizada autenticação básica). |
| palavra-passe |A palavra-passe do utilizador. |Sim (se for utilizada autenticação básica). |
| authSource |Nome da base de dados do MongoDB que pretende utilizar para verificar as suas credenciais para autenticação. |Opcional (se for utilizada autenticação básica). predefinição: utiliza a conta de administrador e a base de dados especificada, utilizando a propriedade databaseName. |
| databaseName |Nome da base de dados do MongoDB que pretende aceder. |Sim |
| gatewayName |Nome do gateway que acessa o arquivo de dados. |Sim |
| encryptedCredential |Credencial encriptada pelo gateway. |Opcional |

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [artigo de conector do MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do MongoDB, defina o **tipo** do conjunto de dados para **MongoDbCollection**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| CollectionName |Nome da coleção na base de dados do MongoDB. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Para obter mais informações, consulte [artigo de conector do MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>Origem do MongoDB na atividade de cópia
Se estiver a copiar dados do MongoDB, defina o **tipo de origem** da atividade copy na atividade **MongoDbSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta de SQL-92. Por exemplo: `select * from MyTable`. |Não (se **collectionName** dos **conjunto de dados** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [artigo de conector do MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Serviço ligado
Para definir um Amazon S3 serviço ligado, defina o **tipo** do serviço ligado para **AwsAccessKey**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| accessKeyID |ID da chave de acesso a segredos. |string |Sim |
| secretAccessKey |A chave de acesso a segredos em si. |Cadeia secreta encriptada |Sim |

#### <a name="example"></a>Exemplo
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Para obter mais informações, consulte [artigo de conector Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Amazon S3, defina o **tipo** do conjunto de dados para **AmazonS3**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| bucketName |O nome do registo de S3. |String |Sim |
| chave |A chave de objeto de S3. |String |Não |
| prefixo |Prefixo para a chave de objeto de S3. Objetos cujas chaves iniciados com este prefixo estão selecionados. Aplica-se apenas quando o chave está vazia. |String |Não |
| versão |A versão do objeto de S3 se o controlo de versões de S3 está ativado. |String |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não | |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Os níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não | |


> [!NOTE]
> bucketName + tecla Especifica a localização do objeto S3 em que o registo é o contêiner raiz para objetos de S3 e a chave é o caminho completo para o objeto de S3.

#### <a name="example-sample-dataset-with-prefix"></a>Exemplo: Conjunto de dados de exemplo com o prefixo

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Exemplo: Conjunto de dados de exemplo (com a versão)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Exemplo: Caminhos de dinâmicos para S3
No exemplo, utilizamos valores fixos para propriedades de chave e bucketName do conjunto de dados do Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Pode ter o Data Factory calcular a chave e bucketName dinamicamente no tempo de execução utilizando variáveis de sistema, como do SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Pode fazer o mesmo para a propriedade de prefixo de um conjunto de dados do Amazon S3. Ver [funções de Data Factory e variáveis de sistema](data-factory-functions-variables.md) para obter uma lista de funções e variáveis.

Para obter mais informações, consulte [artigo de conector Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do Amazon S3, definir o **tipo de origem** da atividade copy na atividade **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:


| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Especifica se recursivamente lista S3 objetos sob o diretório. |Verdadeiro/Falso |Não |


#### <a name="example"></a>Exemplo


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Para obter mais informações, consulte [artigo de conector Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Sistema de Ficheiros


### <a name="linked-service"></a>Serviço ligado
Pode ligar um sistema de ficheiros no local a uma fábrica de dados do Azure com o **servidor de ficheiros no local** serviço ligado. A tabela seguinte fornece descrições para os elementos JSON que são específicas para o serviço de servidor de ficheiros no local ligado.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |Certifique-se de que a propriedade de tipo é definida como **OnPremisesFileServer**. |Sim |
| anfitrião |Especifica o caminho de raiz da pasta que pretende copiar. Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Ver definições de serviço e o conjunto de dados de exemplo ligada para obter exemplos. |Sim |
| ID de utilizador |Especifica o ID de utilizador que tem acesso ao servidor. |Não (se escolher encryptedCredential) |
| palavra-passe |Especifique a palavra-passe para o utilizador (ID de utilizador). |Não (se escolher encryptedCredential |
| encryptedCredential |Especifique as credenciais encriptadas que pode obter ao executar o cmdlet New-AzDataFactoryEncryptValue. |Não (se optar por especificar o ID de utilizador e palavra-passe em texto simples) |
| gatewayName |Especifica o nome do gateway que o Data Factory deve utilizar para ligar ao servidor de ficheiros no local. |Sim |

#### <a name="sample-folder-path-definitions"></a>Definições de caminho de pasta de exemplo
| Cenário | Alojar na definição de serviço ligado | folderPath na definição do conjunto de dados |
| --- | --- | --- |
| Pasta local no computador Gateway de gestão de dados: <br/><br/>Exemplos: D:\\ \* ou D:\folder\subfolder\\* |D:\\ \\ (para o Data Management Gateway 2.0 e versões posteriores) <br/><br/> host local (para versões anteriores que o Data Management Gateway 2.0) |. \\ \\ ou pasta\\\\subpasta (para o Data Management Gateway 2.0 e versões posteriores) <br/><br/>D:\\ \\ ou d:\\\\pasta\\\\subpasta (para a versão do gateway abaixo 2.0) |
| Pasta remota partilhada: <br/><br/>Exemplos: \\ \\myserver\\partilhar\\ \* ou \\ \\myserver\\partilhar\\pasta\\subpasta\\* |\\\\\\\\myserver\\\\share |. \\ \\ ou pasta\\\\subpasta |


#### <a name="example-using-username-and-password-in-plain-text"></a>Exemplo: Usando o nome de utilizador e palavra-passe em texto simples

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Exemplo: Usando encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [artigo de conector do sistema de ficheiros](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do sistema de ficheiros, defina o **tipo** do conjunto de dados para **partilha de ficheiros**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Especifica o Subcaminho para a pasta. Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Ver definições de serviço e o conjunto de dados de exemplo ligada para obter exemplos.<br/><br/>Pode combinar essa propriedade com o **partitionBy** ter pasta caminhos baseados no setor de início/fim datas-horas. |Sim |
| fileName |Especifique o nome do arquivo na **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros na pasta.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado é no seguinte formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro para ser usado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros. <br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (caráter individual).<br/><br/>Exemplo 1: "fileFilter": "*. log"<br/>Exemplo 2: "fileFilter": 2016-1-?.txt"<br/><br/>Tenha em atenção que fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. |Não |
| partitionedBy |Pode usar partitionedBy para especificar um folderPath/nome de ficheiro dinâmica para dados de séries temporais. Um exemplo é folderPath parametrizado por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**; e os níveis de suportados são: **Ideal** e **mais rápida**. ver [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> Não é possível utilizar nome de ficheiro e fileFilter em simultâneo.

#### <a name="example"></a>Exemplo

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
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

Para obter mais informações, consulte [artigo de conector do sistema de ficheiros](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do sistema de arquivos, definir o **tipo de origem** da atividade de cópia para **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Para obter mais informações, consulte [artigo de conector do sistema de ficheiros](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Sistema de ficheiros de Sink na atividade de cópia
Se estiver a copiar dados para o sistema de ficheiros, defina o **tipo de sink** da atividade copy na atividade **FileSystemSink**e especifique os seguintes propriedades no **sink** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| copyBehavior |Define o comportamento de cópia quando a origem é BlobSource ou sistema de ficheiros. |**PreserveHierarchy:** Preserva a hierarquia de ficheiros na pasta de destino. Ou seja, o caminho relativo do ficheiro de origem para a pasta de origem é o mesmo que o caminho relativo do ficheiro de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** Todos os ficheiros da pasta de origem são criados no primeiro nível de pasta de destino. Os ficheiros de destino são criados com um nome gerado automaticamente.<br/><br/>**MergeFiles:** Une todos os ficheiros da pasta de origem para um ficheiro. Se não for especificado o nome de blob/nome de ficheiro, o nome de ficheiro mesclada é o nome especificado. Caso contrário, ele é um nome de ficheiro gerado automaticamente. |Não |
auto-

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [artigo de conector do sistema de ficheiros](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Serviço ligado
Para definir um FTP serviço ligado, defina o **tipo** do serviço ligado para **FtpServer**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário | Predefinição |
| --- | --- | --- | --- |
| anfitrião |Nome ou endereço IP do servidor de FTP |Sim |&nbsp; |
| authenticationType |Especificar o tipo de autenticação |Sim |Básica, anônima |
| nome do utilizador |Utilizador que tenha acesso ao servidor de FTP |Não |&nbsp; |
| palavra-passe |Palavra-passe para o utilizador (nome de utilizador) |Não |&nbsp; |
| encryptedCredential |Credencial encriptada para aceder ao servidor FTP |Não |&nbsp; |
| gatewayName |Nome do Gateway de gestão de dados para ligar a um servidor FTP no local |Não |&nbsp; |
| porta |Porta em que o servidor de FTP está à escuta |Não |21 |
| enableSsl |Especifique se pretende utilizar o FTP através de canal SSL/TLS |Não |true |
| enableServerCertificateValidation |Especifique se pretende ativar a validação de certificado do servidor SSL ao utilizar o FTP através do canal SSL/TLS |Não |true |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilizar a autenticação anónima

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exemplo: Usando o nome de utilizador e palavra-passe em texto simples para a autenticação básica

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exemplo: Utilizar a porta, enableSsl, enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exemplo: Utilizar encryptedCredential para autenticação e de gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Para obter mais informações, consulte [conector FTP](data-factory-ftp-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados FTP, defina o **tipo** do conjunto de dados para **partilha de ficheiros**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho de sub-rotina para a pasta. Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Ver definições de serviço e o conjunto de dados de exemplo ligada para obter exemplos.<br/><br/>Pode combinar essa propriedade com o **partitionBy** ter pasta caminhos baseados no setor de início/fim datas-horas. |Sim
| fileName |Especifique o nome do arquivo na **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros na pasta.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro para ser usado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (caráter individual).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro para dados de séries de tempo. Por exemplo, folderPath parametrizado por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**; e os níveis de suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência do binário. Verdadeiro para o modo de binário e ASCII FALSO. Valor predefinido: VERDADEIRO. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizadas em simultâneo.

#### <a name="example"></a>Exemplo

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte [conector FTP](data-factory-ftp-connector.md#dataset-properties) artigo.

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados de um servidor FTP, definir o **tipo de origem** da atividade copy na atividade **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector FTP](data-factory-ftp-connector.md#copy-activity-properties) artigo.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Serviço ligado
Para definir um HDFS serviço ligado, defina o **tipo** do serviço ligado para **Hdfs**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo tem de ser definida como: **Hdfs** |Sim |
| Url |URL para o HDFS |Sim |
| authenticationType |Anónimo, ou Windows. <br><br> Para utilizar **a autenticação Kerberos** para o conector do HDFS, consulte esta secção para configurar o seu ambiente no local em conformidade. |Sim |
| userName |Autenticação de nome de utilizador para Windows. |Sim (para autenticação do Windows) |
| palavra-passe |Palavra-passe para a autenticação do Windows. |Sim (para autenticação do Windows) |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar para o HDFS. |Sim |
| encryptedCredential |[Novo AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) saída da credencial de acesso. |Não |

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilizar a autenticação anónima

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Exemplo: Utilizar a autenticação do Windows

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte o artigo de conector do HDFS.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do HDFS, defina o **tipo** do conjunto de dados para **partilha de ficheiros**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho para a pasta. Exemplo: `myfolder`<br/><br/>Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Por exemplo: para folder\subfolder, especifique a pasta\\\\subpasta e para d:\samplefolder, especifique d:\\\\samplefolder.<br/><br/>Pode combinar essa propriedade com o **partitionBy** ter pasta caminhos baseados no setor de início/fim datas-horas. |Sim |
| fileName |Especifique o nome do arquivo na **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros na pasta.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>Dados. <Guid>. txt (por exemplo:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro para dados de séries de tempo. Exemplo: folderPath parametrizado por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizadas em simultâneo.

#### <a name="example"></a>Exemplo

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte o artigo de conector do HDFS.

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados do HDFS, definir o **tipo de origem** da atividade copy na atividade **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:

**FileSystemSource** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte o artigo de conector do HDFS.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Serviço ligado
Para definir um SFTP serviço ligado, defina o **tipo** do serviço ligado para **Sftp**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| anfitrião | Nome ou endereço IP do servidor SFTP. |Sim |
| porta |Porta em que o servidor SFTP está a escutar. O valor predefinido é: 21 |Não |
| authenticationType |Especifique o tipo de autenticação. Valores permitidos: **Basic**, **SshPublicKey**. <br><br> Consulte a utilizar a autenticação básica e [através de SSH autenticação de chave pública](#using-ssh-public-key-authentication) secções em mais propriedades e exemplos de JSON, respetivamente. |Sim |
| skipHostKeyValidation | Especifique se pretende ignorar a validação da chave de anfitrião. | Não. O valor predefinido: Falso |
| hostKeyFingerprint | Especifique a impressão digital da chave de anfitrião. | Sim se o `skipHostKeyValidation` é definido como false.  |
| gatewayName |Nome do Gateway de gestão de dados para ligar a um servidor SFTP no local. | Sim se copiam dados a partir de um servidor SFTP no local. |
| encryptedCredential | Credencial encriptada para aceder ao servidor SFTP. Gerado automaticamente quando especifica a autenticação básica (nome de utilizador + palavra-passe) ou SshPublicKey autenticação (nome de utilizador + caminho da chave privado ou conteúdo) no Assistente de cópia ou a caixa de diálogo de pop-up do ClickOnce. | Não. Aplicam-se apenas quando se copiam dados a partir de um servidor SFTP no local. |

#### <a name="example-using-basic-authentication"></a>Exemplo: Usando a autenticação básica

Para utilizar a autenticação básica, defina `authenticationType` como `Basic`e especifique as seguintes propriedades além do conector do SFTP genéricas introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| nome do utilizador | Utilizador que tenha acesso ao servidor SFTP. |Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exemplo: **Autenticação básica com a credencial encriptada**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Utilizar a autenticação de chave pública SSH:**

Para utilizar a autenticação básica, defina `authenticationType` como `SshPublicKey`e especifique as seguintes propriedades além do conector do SFTP genéricas introduzidas na última secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- | --- |
| nome do utilizador |Utilizador que tenha acesso ao servidor SFTP |Sim |
| privateKeyPath | Especifique um caminho absoluto para o ficheiro de chave privada pode aceder a esse gateway. | Especifique a `privateKeyPath` ou `privateKeyContent`. <br><br> Aplicam-se apenas quando se copiam dados a partir de um servidor SFTP no local. |
| privateKeyContent | Uma cadeia de caracteres serializada do conteúdo de chave privada. O Assistente de cópia pode ler o ficheiro de chave privada e extrair o conteúdo da chave privado automaticamente. Se estiver a utilizar qualquer outra ferramenta/SDK, use a propriedade privateKeyPath. | Especifique a `privateKeyPath` ou `privateKeyContent`. |
| passPhrase | Especifique a pass frase/palavra-passe para desencriptar a chave privada, se o ficheiro de chave estiver protegido por uma frase de acesso. | Sim, se o ficheiro de chave privada está protegido por uma frase de acesso. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exemplo: **Autenticação de SshPublicKey com o conteúdo da chave privado**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Para obter mais informações, consulte [conector do SFTP](data-factory-sftp-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do SFTP, defina o **tipo** do conjunto de dados para **partilha de ficheiros**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| folderPath |Caminho de sub-rotina para a pasta. Utilizar o caráter de escape "\" para carateres especiais na cadeia de caracteres. Ver definições de serviço e o conjunto de dados de exemplo ligada para obter exemplos.<br/><br/>Pode combinar essa propriedade com o **partitionBy** ter pasta caminhos baseados no setor de início/fim datas-horas. |Sim |
| fileName |Especifique o nome do arquivo na **folderPath** se pretender que a tabela para fazer referência a um ficheiro específico na pasta. Se não especificar qualquer valor para esta propriedade, a tabela aponta para todos os ficheiros na pasta.<br/><br/>Quando o nome de ficheiro não está especificado para um conjunto de dados de saída, o nome do ficheiro gerado seria a seguir este formato: <br/><br/>`Data.<Guid>.txt` (Exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Não |
| fileFilter |Especifique um filtro para ser usado para selecionar um subconjunto de ficheiros em folderPath em vez de todos os ficheiros.<br/><br/>Valores permitidos são: `*` (vários carateres) e `?` (caráter individual).<br/><br/>Exemplos 1: `"fileFilter": "*.log"`<br/>Exemplo 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter se aplica a um conjunto de dados de partilha de ficheiros de entrada. Esta propriedade não é suportada com HDFS. |Não |
| partitionedBy |partitionedBy pode ser utilizado para especificar um folderPath dinâmica, o nome de ficheiro para dados de séries de tempo. Por exemplo, folderPath parametrizado por cada hora de dados. |Não |
| Formato | São suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Definir o **tipo** propriedade em formato para um dos seguintes valores. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. <br><br> Se quiser **copiar ficheiros como-é** entre arquivos baseados em ficheiros (binário cópia), ignore a secção de formato em ambas as definições do conjunto de dados de entrada e saída. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |
| useBinaryTransfer |Especifique se utilizar o modo de transferência do binário. Verdadeiro para o modo de binário e ASCII FALSO. Valor predefinido: VERDADEIRO. Esta propriedade só pode ser utilizada quando o tipo de serviço ligado associado é do tipo: FtpServer. |Não |

> [!NOTE]
> nome de ficheiro e fileFilter não podem ser utilizadas em simultâneo.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte [conector do SFTP](data-factory-sftp-connector.md#dataset-properties) artigo.

### <a name="file-system-source-in-copy-activity"></a>Origem do sistema de ficheiros na atividade de cópia
Se estiver a copiar dados de uma origem SFTP, definir o **tipo de origem** da atividade copy na atividade **FileSystemSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| recursiva |Indica se os dados são lidos recursivamente das subpastas ou apenas a partir da pasta especificada. |TRUE, False (predefinição) |Não |



#### <a name="example"></a>Exemplo

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector do SFTP](data-factory-sftp-connector.md#copy-activity-properties) artigo.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Serviço ligado
Para definir um pedido HTTP de serviço ligado, defina o **tipo** do serviço ligado para **Http**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| url | URL de base para o servidor Web | Sim |
| authenticationType | Especifica o tipo de autenticação. Valores permitidos são: **Anonymous**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Consulte a secções abaixo desta tabela em mais propriedades e exemplos JSON para esses tipos de autenticação, respetivamente. | Sim |
| enableServerCertificateValidation | Especifique se pretende ativar a validação de certificado SSL do servidor, se a origem é o servidor de Web de HTTPS | Não, a predefinição é verdadeiro |
| gatewayName | Nome do Gateway de gestão de dados para ligar a uma origem HTTP no local. | Sim, se copiar dados a partir de uma origem HTTP no local. |
| encryptedCredential | Credencial encriptada para aceder ao ponto final HTTP. Gerado automaticamente quando configura as informações de autenticação no Assistente de cópia ou a caixa de diálogo de pop-up do ClickOnce. | Não. Aplicam-se apenas quando se copiam dados a partir de um servidor HTTP no local. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exemplo: Utilizar a autenticação básica, Digest ou do Windows
Definir `authenticationType` como `Basic`, `Digest`, ou `Windows`e especifique as seguintes propriedades além do conector HTTP genéricas introduzidos acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| nome do utilizador | Nome de utilizador para aceder ao ponto final HTTP. | Sim |
| palavra-passe | Palavra-passe para o utilizador (nome de utilizador). | Sim |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Exemplo: Utilizar a autenticação de ClientCertificate

Para utilizar a autenticação básica, defina `authenticationType` como `ClientCertificate`e especifique as seguintes propriedades além do conector HTTP genéricas introduzidos acima:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| embeddedCertData | O conteúdo de dados binários do ficheiro Personal Information Exchange (PFX) com codificação Base64. | Especifique a `embeddedCertData` ou `certThumbprint`. |
| certThumbprint | O thumbprint do certificado que foi instalado no arquivo de certificados do seu computador de gateway. Aplicam-se apenas quando se copiam dados a partir de uma origem HTTP no local. | Especifique a `embeddedCertData` ou `certThumbprint`. |
| palavra-passe | Palavra-passe associado ao certificado. | Não |

Se usar `certThumbprint` para autenticação e o certificado está instalado no arquivo pessoal do computador local, tem de conceder a permissão de leitura para o serviço de gateway:

1. Inicie o Console de gerenciamento Microsoft (MMC). Adicionar a **certificados** snap-in direcionada para o **computador Local**.
2. Expanda **certificados**, **pessoais**e clique em **certificados**.
3. O certificado do arquivo pessoal com o botão direito e selecione **todas as tarefas**->**gerir chaves privadas...**
3. Sobre o **segurança** separador, adicione a conta de utilizador sob a qual o serviço de anfitrião do Data Management Gateway está em execução com o acesso de leitura para o certificado.

**Exemplo: utilizar o certificado de cliente:** Este serviço ligado liga a fábrica de dados para um servidor de web HTTP no local. Ele usa um certificado de cliente é instalado na máquina com o Data Management Gateway instalado.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exemplo: utilizar o certificado de cliente num arquivo
Este serviço ligado liga a fábrica de dados para um servidor de web HTTP no local. Utiliza um ficheiro de certificado de cliente na máquina com o Data Management Gateway instalado.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Para obter mais informações, consulte [conector HTTP](data-factory-http-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados HTTP, defina o **tipo** do conjunto de dados para **Http**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| relativeUrl | Um URL relativo ao recurso que contém os dados. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição do serviço ligado. <br><br> Para construir a URL dinâmico, pode usar [funções de Data Factory e variáveis de sistema](data-factory-functions-variables.md), exemplo: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Não |
| requestMethod | Método HTTP. Valores permitidos são **Obtenha** ou **POST**. | Não. A predefinição é `GET`. |
| additionalHeaders | Cabeçalhos de pedido HTTP adicionais. | Não |
| requestBody | Corpo de pedido HTTP. | Não |
| Formato | Se quiser simplesmente **recuperar os dados do ponto de extremidade HTTP como-é** sem analisá-lo, ignorar esta definição de formato. <br><br> Se pretender analisar o conteúdo de resposta HTTP durante a cópia, são suportados os seguintes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Para obter mais informações, consulte [formato de texto](data-factory-supported-file-and-compression-formats.md#text-format), [formato Json](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato Orc](data-factory-supported-file-and-compression-formats.md#orc-format), e [formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) secções. |Não |
| Compressão | Especifica o tipo e o nível de compressão dos dados. Tipos suportados são: **GZip**, **Deflate**, **BZip2**, e **ZipDeflate**. Níveis suportados são: **Ideal** e **mais rápida**. Para obter mais informações, consulte [formatos de ficheiro e a compactação no Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Não |

#### <a name="example-using-the-get-default-method"></a>Exemplo: usando o método GET (predefinição)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Exemplo: usando o método POST

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Para obter mais informações, consulte [conector HTTP](data-factory-http-connector.md#dataset-properties) artigo.

### <a name="http-source-in-copy-activity"></a>Origem de HTTP na atividade de cópia
Se estiver a copiar dados de uma origem HTTP, definir o **tipo de origem** da atividade copy na atividade **HttpSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| httpRequestTimeout | O tempo limite (intervalo de tempo) para o pedido HTTP para obter uma resposta. É o tempo limite para obter uma resposta, não o tempo limite para ler os dados de resposta. | Não. Valor predefinido: 00:01:40 |


#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector HTTP](data-factory-http-connector.md#copy-activity-properties) artigo.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Serviço ligado
Para definir um OData de serviço ligado, defina o **tipo** do serviço ligado para **OData**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| url |URL do serviço OData. |Sim |
| authenticationType |Tipo de autenticação utilizado para ligar à origem de OData. <br/><br/> Para a nuvem OData, os valores possíveis são anónimo, básico e OAuth (Observe suporte atualmente, apenas a fábrica de dados do Azure do Azure Active Directory com base em OAuth). <br/><br/> Para OData no local, os valores possíveis são anónimo, básico e Windows. |Sim |
| nome do utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica. |Sim (apenas se estiver a utilizar autenticação básica) |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Sim (apenas se estiver a utilizar autenticação básica) |
| authorizedCredential |Se estiver a utilizar o OAuth, clique em **autorizar** no Assistente de cópia do Data Factory ou no Editor e digitar suas credenciais, em seguida, o valor desta propriedade será gerado automaticamente. |Sim (apenas se estiver a utilizar autenticação OAuth) |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar ao serviço OData no local. Especifique apenas se estiver a copiar dados de origem de OData no local. |Não |

#### <a name="example---using-basic-authentication"></a>Exemplo - usando a autenticação básica
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Exemplo - usando a autenticação anónima

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exemplo - acesso do Windows usando autenticação no local origem OData

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exemplo - utilizar a autenticação OAuth, aceder à origem de OData de cloud
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Para obter mais informações, consulte [conector de OData](data-factory-odata-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do OData, defina o **tipo** do conjunto de dados para **ODataResource**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| caminho |Caminho para o recurso OData |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Para obter mais informações, consulte [conector de OData](data-factory-odata-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de uma origem de OData, definir o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Exemplo | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |"?$select=Name, Description&$top=5" |Não |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector de OData](data-factory-odata-connector.md#copy-activity-properties) artigo.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Serviço ligado
Para definir um ODBC serviço ligado, defina o **tipo** do serviço ligado para **OnPremisesOdbc**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |A credencial de acesso de leitura não parte de cadeia de ligação e uma credencial encriptada opcional. Veja exemplos nas seções a seguir. |Sim |
| Credencial |A parte de credencial de acesso da cadeia de ligação especificada no formato de valores de propriedade específicos de driver. Exemplo: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Não |
| authenticationType |Tipo de autenticação utilizado para ligar ao arquivo de dados ODBC. Os valores possíveis são: Anónimo e básico. |Sim |
| nome do utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação básica. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar ao arquivo de dados ODBC. |Sim |

#### <a name="example---using-basic-authentication"></a>Exemplo - usando a autenticação básica

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Exemplo - usando a autenticação básica com as credenciais encriptadas
Pode criptografar as credenciais a utilizar o [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) cmdlet.

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Exemplo: Utilizar a autenticação anónima

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Para obter mais informações, consulte [conector do ODBC](data-factory-odbc-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do ODBC, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no arquivo de dados ODBC. |Sim |


#### <a name="example"></a>Exemplo

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
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

Para obter mais informações, consulte [conector do ODBC](data-factory-odbc-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados de um arquivo de dados do ODBC, defina o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção :

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: `select * from MyTable`. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Para obter mais informações, consulte [conector do ODBC](data-factory-odbc-connector.md#copy-activity-properties) artigo.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Serviço ligado
Para definir um Salesforce serviço ligado, defina o **tipo** do serviço ligado para **Salesforce**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| environmentUrl | Especifique a instância de URL do Salesforce. <br><br> -Predefinição é "https://login.salesforce.com". <br> -Para copiar dados de proteção de segurança, especifique "https://test.salesforce.com". <br> -Para copiar dados de domínio personalizado, especifique, por exemplo, "https://[domain].my.salesforce.com". |Não |
| nome do utilizador |Especifique um nome de utilizador para a conta de utilizador. |Sim |
| palavra-passe |Especifique uma palavra-passe da conta de utilizador. |Sim |
| securityToken |Especifique um token de segurança da conta de utilizador. Ver [obter token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como repor/obter um token de segurança. Para saber mais sobre os tokens de segurança em geral, veja [segurança e a API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Para obter mais informações, consulte [conector do Salesforce](data-factory-salesforce-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados do Salesforce, defina o **tipo** do conjunto de dados para **RelationalTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tableName |Nome da tabela no Salesforce. |Não (se um **consulta** dos **RelationalSource** for especificado) |

#### <a name="example"></a>Exemplo

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
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

Para obter mais informações, consulte [conector do Salesforce](data-factory-salesforce-connector.md#dataset-properties) artigo.

### <a name="relational-source-in-copy-activity"></a>Origem relacional na atividade de cópia
Se estiver a copiar dados do Salesforce, definir o **tipo de origem** da atividade copy na atividade **RelationalSource**e especifique os seguintes propriedades no **origem** secção:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Uma consulta de SQL-92 ou [linguagem de consulta de objeto do Salesforce (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) consulta. Por exemplo: `select * from MyTable__c`. |Não (se o **tableName** da **conjunto de dados** for especificado) |

#### <a name="example"></a>Exemplo



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> A parte de "__c" o nome da API é necessário para qualquer objeto personalizado.

Para obter mais informações, consulte [conector do Salesforce](data-factory-salesforce-connector.md#copy-activity-properties) artigo.

## <a name="web-data"></a>Dados da Web

### <a name="linked-service"></a>Serviço ligado
Para definir uma Web do serviço de ligado, defina o **tipo** do serviço ligado para **Web**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Url |URL para a origem da Web |Sim |
| authenticationType |Anónimo. |Sim |


#### <a name="example"></a>Exemplo


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Para obter mais informações, consulte [conector de tabela Web](data-factory-web-table-connector.md#linked-service-properties) artigo.

### <a name="dataset"></a>Conjunto de dados
Para definir um conjunto de dados da Web, defina o **tipo** do conjunto de dados para **WebTable**e especifique as seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |Tipo de conjunto de dados. tem de ser definido como **WebTable** |Sim |
| caminho |Um URL relativo ao recurso que contém a tabela. |Não. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição do serviço ligado. |
| índice |O índice da tabela no recurso. Veja Get índice de uma tabela numa seção da página HTML para obter passos para obter o índice de uma tabela numa página HTML. |Sim |

#### <a name="example"></a>Exemplo

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Para obter mais informações, consulte [conector de tabela Web](data-factory-web-table-connector.md#dataset-properties) artigo.

### <a name="web-source-in-copy-activity"></a>Origem da Web na atividade de cópia
Se estiver a copiar dados de uma tabela de web, defina o **tipo de origem** da atividade copy na atividade **WebSource**. Atualmente, quando a origem na atividade de cópia é do tipo **WebSource**, não existem propriedades adicionais são suportadas.

#### <a name="example"></a>Exemplo

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Para obter mais informações, consulte [conector de tabela Web](data-factory-web-table-connector.md#copy-activity-properties) artigo.

## <a name="compute-environments"></a>AMBIENTES DE COMPUTAÇÃO
A tabela seguinte lista os ambientes de computação suportados pela fábrica de dados e as atividades de transformação que podem ser executadas nos mesmos. Clique na ligação para a computação que estiver interessado em ver os esquemas JSON para o serviço ligado para ligá-lo a uma fábrica de dados.

| Ambiente de computação | Atividades |
| --- | --- |
| [Cluster de HDInsight a pedido](#on-demand-azure-hdinsight-cluster) ou [seu próprio cluster do HDInsight](#existing-azure-hdinsight-cluster) |[Atividade personalizada do .NET](#net-custom-activity), [atividade do Hive](#hdinsight-hive-activity), [Pig a atividade](#hdinsight-pig-activity), [atividade MapReduce](#hdinsight-mapreduce-activity), atividade, de transmissão em fluxo Hadoop [atividade do Spark](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Atividade personalizada do .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Atividade de execução de lote do Machine Learning](#machine-learning-batch-execution-activity), [atividade de recursos de atualização de Machine Learning](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [SQL Server](#sql-server-1) |[Procedimento Armazenado](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster do Azure HDInsight a pedido
O serviço Azure Data Factory pode criar automaticamente um cluster do HDInsight baseado em Windows/Linux sob demanda para processar dados. O cluster é criado na mesma região que a conta de armazenamento (linkedServiceName propriedade no JSON) associada ao cluster. Pode executar as seguintes atividades de transformação neste serviço ligado: [atividade personalizada do .NET](#net-custom-activity), [atividade do Hive](#hdinsight-hive-activity), [Pig atividade](#hdinsight-pig-activity), [MapReduce atividade](#hdinsight-mapreduce-activity), atividade, de transmissão em fluxo do Hadoop [atividade do Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição de JSON do Azure de um serviço de ligado de HDInsight a pedido.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como **HDInsightOnDemand**. |Sim |
| clusterSize |Número de nós de trabalho/dados no cluster. O cluster do HDInsight é criado com 2 nós principais, juntamente com o número de nós de trabalho que especificou para esta propriedade. Os nós são do tamanho Standard_D3 com 4 núcleos, para que um cluster de nó de 4 trabalho usa 24 núcleos (4\*4 = 16 núcleos para nós de trabalho, além de 2\*4 = 8 núcleos para nós principais). Ver [clusters do Hadoop de baseados em criar Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes sobre o escalão de Standard_D3. |Sim |
| TimeToLive |O tempo de inatividade permitido para o cluster de HDInsight a pedido. Especifica o tempo que o cluster de HDInsight a pedido fique vivo após a conclusão de uma atividade executar se não existirem não existem outras tarefas ativas no cluster.<br/><br/>Por exemplo, se uma execução de atividade demora 6 minutos e timetolive é definido para 5 minutos, o cluster fique vivo durante 5 minutos após a execução de seis minutos de atividade de processamento. Se outra execução de atividade é executada com a janela de 6 minutos, é processada pelo mesmo cluster.<br/><br/>Criar um cluster do HDInsight a pedido é uma operação dispendiosa (podem demorar algum tempo), por isso, utilize esta definição como necessário para melhorar o desempenho de uma fábrica de dados ao reutilizar um cluster do HDInsight a pedido.<br/><br/>Se definir o valor de timetolive para 0, o cluster é eliminado assim que a atividade executada processados. Por outro lado, se definir um valor elevado, o cluster pode permanecer inativo desnecessariamente resulta em custos elevados. Por conseguinte, é importante que defina o valor apropriado com base nas suas necessidades.<br/><br/>Vários pipelines podem partilhar a mesma instância de cluster de HDInsight a pedido, se o valor da propriedade timetolive está corretamente definido |Sim |
| versão |Versão do cluster do HDInsight. Para obter detalhes, consulte [suportadas versões do HDInsight no Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Não |
| linkedServiceName |Serviço ligado do armazenamento do Azure a ser utilizado pelo cluster sob demanda para armazenar e processar dados. <p>Atualmente, não é possível criar um cluster do HDInsight a pedido que utiliza um Store do Azure Data Lake como o armazenamento. Se pretende armazenar os dados de resultado do processamento num Store do Azure Data Lake do HDInsight, utilize uma atividade de cópia para copiar os dados do armazenamento de Blobs do Azure para o Store do Azure Data Lake.</p>  | Sim |
| additionalLinkedServiceNames |Especifica o serviço de ligado de contas de armazenamento adicional para o HDInsight, para que o serviço Data Factory pode registá-los em seu nome. |Não |
| osType |Tipo de sistema operativo. Valores permitidos são: (Predefinição) do Windows e Linux |Não |
| hcatalogLinkedServiceName |O nome de SQL do Azure vinculada serviço que apontam para a base de dados do HCatalog. O cluster de HDInsight a pedido é criado ao utilizar a base de dados SQL do Azure como o metastore. |Não |

### <a name="json-example"></a>Exemplo de JSON
O seguinte JSON define um serviço de ligado de HDInsight a pedido baseado em Linux. O serviço Data Factory cria automaticamente uma **baseado em Linux** cluster do HDInsight ao processar um setor de dados.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Para obter mais informações, consulte [serviços ligados de computação](data-factory-compute-linked-services.md) artigo.

## <a name="existing-azure-hdinsight-cluster"></a>Cluster de HDInsight do Azure existente
Pode criar um serviço ligado de HDInsight de Azure para registar o seu próprio cluster do HDInsight com o Data Factory. Pode executar as seguintes atividades de transformação de dados sobre este serviço ligado: [atividade personalizada do .NET](#net-custom-activity), [atividade do Hive](#hdinsight-hive-activity), [Pig atividade](#hdinsight-pig-activity), [ Atividade MapReduce](#hdinsight-mapreduce-activity), atividade, de transmissão em fluxo do Hadoop [atividade do Spark](#hdinsight-spark-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição de JSON do Azure de um serviço ligado de HDInsight de Azure.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como **HDInsight**. |Sim |
| clusterUri |O URI do HDInsight cluster. |Sim |
| nome do utilizador |Especifique o nome do utilizador a ser utilizado para ligar a um cluster do HDInsight existente. |Sim |
| palavra-passe |Especifique a palavra-passe da conta de utilizador. |Sim |
| linkedServiceName | Nome do serviço ligado do armazenamento do Azure que se refere-se para o armazenamento de Blobs do Azure utilizado pelo cluster do HDInsight. <p>Atualmente, não é possível especificar que um Store do Azure Data Lake serviço ligado para esta propriedade. Pode aceder aos dados a Store do Azure Data Lake de scripts Hive/Pig se o cluster do HDInsight tem acesso ao Store de Lake dados. </p>  |Sim |

Para versões de clusters de HDInsight suportados, consulte [HDInsight versões suportadas](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Pode criar um serviço ligado do Azure Batch para registar um conjunto do Batch de máquinas virtuais (VMs) com uma fábrica de dados. Pode executar atividades personalizadas do .NET com o Azure Batch ou do Azure HDInsight. Pode executar uma [atividade personalizada do .NET](#net-custom-activity) sobre este serviço ligado.

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição de JSON do Azure de um serviço ligado do Azure Batch.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como **AzureBatch**. |Sim |
| accountName |Nome da conta do Azure Batch. |Sim |
| accessKey |Chave de acesso para a conta do Azure Batch. |Sim |
| poolName |Nome do conjunto de máquinas virtuais. |Sim |
| linkedServiceName |Nome de armazenamento do Azure vinculada serviço associado este serviço ligado do Azure Batch. Este serviço ligado é utilizado para os ficheiros de testes necessários para executar a atividade e armazenar os registos de execução de atividade. |Sim |


#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Criar um serviço ligado do Azure Machine Learning para registar um ponto final com uma fábrica de dados de classificação de lote do Machine Learning. Atividades de transformação de dados de dois que podem ser executados neste serviço ligado: [Atividade de execução de lote do Machine Learning](#machine-learning-batch-execution-activity), [atividade de recursos de atualização de Machine Learning](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Serviço ligado
A tabela seguinte fornece descrições para as propriedades utilizadas na definição de JSON do Azure de um serviço ligado do Azure Machine Learning.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Type |A propriedade de tipo deve ser definida como: **AzureML**. |Sim |
| mlEndpoint |O URL de classificação do lote. |Sim |
| apiKey |API do modelo de área de trabalho publicado. |Sim |

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Criar uma **do Azure Data Lake Analytics** serviço para uma fábrica de dados do Azure de computação de serviço ligado para ligar um Azure Data Lake Analytics antes de utilizar o [atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md) num pipeline.

### <a name="linked-service"></a>Serviço ligado

A tabela seguinte fornece descrições para as propriedades utilizadas na definição de JSON de um serviço ligado do Azure Data Lake Analytics.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Type |A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. |Sim |
| accountName |Nome da conta do Azure Data Lake Analytics. |Sim |
| dataLakeAnalyticsUri |URI do Azure Data Lake Analytics. |Não |
| Autorização |Código de autorização é obtido automaticamente depois de clicar em **autorizar** botão no Editor do Data Factory e concluir o início de sessão OAuth. |Sim |
| subscriptionId |Id de subscrição do Azure |Não (se não for especificado, a subscrição do data factory é utilizada). |
| resourceGroupName |Nome do grupo de recursos do Azure |Não (se não for especificado, grupo de recursos do data factory é utilizado). |
| sessionId |id de sessão a partir da sessão de autorização de OAuth. Cada id de sessão é exclusivo e só pode ser utilizada uma vez. Ao utilizar o Editor do Data Factory, este ID é gerada automaticamente. |Sim |


#### <a name="json-example"></a>Exemplo de JSON
O exemplo seguinte fornece a definição de JSON para um serviço ligado do Azure Data Lake Analytics.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Criar um serviço ligado SQL do Azure e utilizá-lo com o [atividade de procedimento armazenado](#stored-procedure-activity) para invocar um procedimento armazenado a partir de um pipeline do Data Factory.

### <a name="linked-service"></a>Serviço ligado
Para definir uma base de dados do SQL do Azure de serviço ligado, defina o **tipo** do serviço ligado para **AzureSqlDatabase**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para ligar à instância do SQL Database do Azure para a propriedade connectionString. |Sim |

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Ver [conector do SQL Azure](data-factory-azure-sql-connector.md#linked-service-properties) artigo para obter detalhes sobre este serviço ligado.

## <a name="azure-sql-data-warehouse"></a>Armazém de Dados SQL do Azure
Criar um serviço ligado do Azure SQL Data Warehouse e utilizá-lo com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline do Data Factory.

### <a name="linked-service"></a>Serviço ligado
Para definir um Azure SQL Data Warehouse de serviço ligado, defina o **tipo** do serviço ligado para **AzureSqlDW**e especifique os seguintes propriedades no **typeProperties** secção:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| connectionString |Especifique as informações necessárias para ligar à instância do armazém de dados SQL do Azure para a propriedade connectionString. |Sim |

#### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Para obter mais informações, consulte [conector do Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artigo.

## <a name="sql-server"></a>SQL Server
Criar um serviço ligado do SQL Server e utilizá-lo com o [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado a partir de um pipeline do Data Factory.

### <a name="linked-service"></a>Serviço ligado
Vai criar um serviço ligado do tipo **OnPremisesSqlServer** para ligar uma base de dados do SQL Server no local a uma fábrica de dados. A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço de ligado do SQL Server no local.

A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado do SQL Server.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade de tipo deve ser definida como: **OnPremisesSqlServer**. |Sim |
| connectionString |Especifique as informações de connectionString necessárias para se ligar à base de dados de SQL Server no local, utilizando a autenticação SQL ou autenticação do Windows. |Sim |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à base de dados do SQL Server no local. |Sim |
| nome do utilizador |Especifique o nome de utilizador se estiver a utilizar autenticação do Windows. Exemplo: **domainname\\nome de utilizador**. |Não |
| palavra-passe |Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. |Não |

Pode criptografar as credenciais com o **New-AzDataFactoryEncryptValue** cmdlet e utilizá-los na cadeia de ligação, conforme mostrado no exemplo a seguir (**EncryptedCredential** propriedade):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exemplo: JSON para utilizar a autenticação de SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exemplo: JSON para utilizar a autenticação do Windows

Se o nome de utilizador e palavra-passe forem especificados, o gateway utiliza-os para representar a conta de utilizador especificado para ligar à base de dados do SQL Server no local. Caso contrário, o gateway liga para o SQL Server diretamente com o contexto de segurança do Gateway (sua conta de arranque).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Para obter mais informações, consulte [conector do SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) artigo.

## <a name="data-transformation-activities"></a>ATIVIDADES DE TRANSFORMAÇÃO DE DADOS

Atividade | Descrição
-------- | -----------
[Atividade do Hive do HDInsight](#hdinsight-hive-activity) | A atividade do HDInsight Hive no pipeline do Data Factory executa consultas do Hive por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda.
[Atividade Pig do HDInsight](#hdinsight-pig-activity) | A atividade Pig do HDInsight no pipeline do Data Factory executa consultas de Pig por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda.
[Atividade MapReduce do HDInsight](#hdinsight-mapreduce-activity) | A atividade de MapReduce do HDInsight no pipeline do Data Factory executa programas MapReduce por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda.
[Atividade Streaming do HDInsight](#hdinsight-streaming-activity) | A atividade de transmissão em fluxo do HDInsight no pipeline do Data Factory executa programas de transmissão em fluxo do Hadoop de mensagens em fila por conta própria ou cluster do HDInsight baseado em Windows/Linux por demanda.
[Atividade do HDInsight Spark](#hdinsight-spark-activity) | A atividade do Spark do HDInsight no pipeline do Data Factory executa programas do Spark no seu próprio cluster do HDInsight.
[Atividade de Execução em Lote do Machine Learning](#machine-learning-batch-execution-activity) | O Azure Data Factory permite-lhe facilmente criar pipelines que utilizem um serviço de web publicado do Azure Machine Learning para Análise Preditiva. Usando a atividade de execução do Batch num pipeline do Azure Data Factory, pode invocar um serviço web do Machine Learning para fazer previsões sobre os dados no batch.
[Atividade de Recursos de Atualização de Machine Learning](#machine-learning-update-resource-activity) | Ao longo do tempo, os modelos preditivos da classificação experimentações do Machine Learning necessário reestruturar utilizando conjuntos de dados de entrada novo. Depois de terminar com reparametrização, pretende atualizar o serviço web de pontuação com o modelo de Machine Learning retrained. Pode utilizar a atividade de recursos de atualização para atualizar o serviço web com o modelo treinado recentemente.
[Atividade de Procedimento Armazenado](#stored-procedure-activity) | Pode utilizar a atividade de procedimento armazenado num pipeline do Data Factory para invocar um procedimento armazenado em um dos arquivos de dados seguintes: SQL Database do Azure, Azure SQL Data Warehouse, base de dados do SQL Server na sua empresa ou uma VM do Azure.
[Atividade do Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) | Atividade de U-SQL do Data Lake Analytics executa um script de U-SQL num cluster do Azure Data Lake Analytics.
[Atividade personalizada do .NET](#net-custom-activity) | Se precisar de transformar os dados de uma forma que não é suportado pelo Data Factory, pode criar uma atividade personalizada com a sua própria lógica de processamento de dados e utilize a atividade no pipeline. Pode configurar a atividade .NET personalizada a ser realizada usando um serviço do Azure Batch ou um cluster do HDInsight do Azure.


## <a name="hdinsight-hive-activity"></a>Atividade do HDInsight Hive
Pode especificar as seguintes propriedades numa definição de JSON de atividade do Hive. A propriedade de tipo para a atividade tem de ser: **HDInsightHive**. Tem de criar um serviço ligado do HDInsight em primeiro lugar e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como HDInsightHive:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Script |Especifique o inline de script do Hive |Não |
| caminho do script |Store o script do Hive num armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade "script" ou 'scriptPath'. Não podem ser utilizados em conjunto. O nome de ficheiro diferencia maiúsculas de minúsculas. |Não |
| Define |Especifique parâmetros como pares chave/valor para a referenciar dentro do script do Hive com o 'hiveconf' |Não |

Estas propriedades de tipo são específicas para a atividade do Hive. Outras propriedades (fora da secção typeProperties) são suportadas para todas as atividades.

### <a name="json-example"></a>Exemplo de JSON
O JSON seguinte define uma atividade do Hive do HDInsight num pipeline.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Para obter mais informações, consulte [atividade do Hive](data-factory-hive-activity.md) artigo.

## <a name="hdinsight-pig-activity"></a>Atividade Pig do HDInsight
Pode especificar as seguintes propriedades numa definição de JSON de atividade Pig. A propriedade de tipo para a atividade tem de ser: **HDInsightPig**. Tem de criar um serviço ligado do HDInsight em primeiro lugar e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como HDInsightPig:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| Script |Especifique o inline de script Pig |Não |
| caminho do script |Store o script Pig num armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade "script" ou 'scriptPath'. Não podem ser utilizados em conjunto. O nome de ficheiro diferencia maiúsculas de minúsculas. |Não |
| Define |Especifique parâmetros como pares chave/valor para fazer referência no Pig script |Não |

Estas propriedades de tipo são específicas para a atividade Pig. Outras propriedades (fora da secção typeProperties) são suportadas para todas as atividades.

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Para obter mais informações, consulte atividade Pig do artigo.

## <a name="hdinsight-mapreduce-activity"></a>Atividade MapReduce do HDInsight
Pode especificar as seguintes propriedades numa definição de JSON de atividade de MapReduce. A propriedade de tipo para a atividade tem de ser: **HDInsightMapReduce**. Tem de criar um serviço ligado do HDInsight em primeiro lugar e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como HDInsightMapReduce:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| jarLinkedService | Nome do serviço ligado para o armazenamento do Azure que contém o ficheiro JAR. | Sim |
| jarFilePath | Caminho para o ficheiro JAR no armazenamento do Azure. | Sim |
| className | Nome da classe principal no ficheiro JAR. | Sim |
| argumentos | Uma lista de argumentos separados por vírgulas para o programa de MapReduce. No tempo de execução, verá alguns argumentos adicionais (por exemplo: mapreduce.job.tags) do MapReduce framework. Para diferenciar os argumentos com os argumentos do MapReduce, considere utilizar a opção e o valor como argumentos, conforme mostrado no exemplo a seguir (- s, - entrada, – resultado etc., são imediatamente seguidas pelos seus valores de opções) | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Para obter mais informações, consulte [atividade MapReduce](data-factory-map-reduce.md) artigo.

## <a name="hdinsight-streaming-activity"></a>Atividade do HDInsight Streaming
Pode especificar as seguintes propriedades numa definição de JSON de atividade de transmissão em fluxo Hadoop. A propriedade de tipo para a atividade tem de ser: **HDInsightStreaming**. Tem de criar um serviço ligado do HDInsight em primeiro lugar e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como HDInsightStreaming:

| Propriedade | Descrição |
| --- | --- |
| Mapeador de pontos | Nome do executável o mapeador de pontos. No exemplo, cat.exe é o mapeador de executável.|
| reducer | Nome do reducer executável. No exemplo, wc.exe é reducer executável. |
| entrada | Ficheiro de entrada (incluindo a localização) para o mapeador de pontos. No exemplo: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample é o contentor de BLOBs, exemplo/dados/Gutenberg é a pasta e davinci.txt é o blob. |
| saída | Ficheiro de saída (incluindo a localização) para o reducer. O resultado da tarefa de transmissão em fluxo do Hadoop é escrito para a localização especificada para esta propriedade. |
| filePaths | Caminhos para os mapeador de pontos e reducer executáveis. No exemplo: "adfsample/example/apps/wc.exe", adfsample é o contentor de BLOBs/aplicações de exemplo é a pasta e wc.exe é o executável. |
| fileLinkedService | Serviço ligado do armazenamento do Azure que representa o armazenamento do Azure que contém os ficheiros especificados na secção filePaths. |
| argumentos | Uma lista de argumentos separados por vírgulas para o programa de MapReduce. No tempo de execução, verá alguns argumentos adicionais (por exemplo: mapreduce.job.tags) do MapReduce framework. Para diferenciar os argumentos com os argumentos do MapReduce, considere utilizar a opção e o valor como argumentos, conforme mostrado no exemplo a seguir (- s, - entrada, – resultado etc., são imediatamente seguidas pelos seus valores de opções) |
| getDebugInfo | Um elemento opcional. Quando é definido como falha, os registos são transferidos apenas em caso de falha. Quando for definida para todos, os registos serão sempre transferidos, independentemente do Estado de execução. |

> [!NOTE]
> Tem de especificar um conjunto de dados de saída para a atividade de transmissão em fluxo do Hadoop para o **produz** propriedade. Este conjunto de dados pode ser apenas um dataset fictício que é necessário para orientar a agenda do pipeline (hora a hora, diária, etc.). Se a atividade não incluir uma entrada, pode ignorar a especificação de um conjunto de dados de entrada para a atividade para o **entradas** propriedade.

## <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Para obter mais informações, consulte [atividade de transmissão em fluxo Hadoop](data-factory-hadoop-streaming-activity.md) artigo.

## <a name="hdinsight-spark-activity"></a>Atividade do HDInsight Spark
Pode especificar as seguintes propriedades numa definição de JSON de atividade do Spark. A propriedade de tipo para a atividade tem de ser: **HDInsightSpark**. Tem de criar um serviço ligado do HDInsight em primeiro lugar e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como HDInsightSpark:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| rootPath | O contentor de Blobs do Azure e a pasta que contém o ficheiro de Spark. O nome de ficheiro diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo para a pasta raiz do código/pacote Spark. | Sim |
| className | Classe de principal da aplicação Java/Spark | Não |
| argumentos | Uma lista de argumentos da linha de comandos para o programa Spark. | Não |
| proxyUser | A conta de utilizador para representar a execução do programa Spark | Não |
| sparkConfig | Propriedades de configuração de Spark. | Não |
| getDebugInfo | Especifica quando os ficheiros de registo do Spark são copiados para o armazenamento do Azure utilizado pelo cluster do HDInsight (ou) especificado pelo sparkJobLinkedService. Valores permitidos: Nenhum, sempre, ou a falha. Valor predefinido: Nenhum. | Não |
| sparkJobLinkedService | Serviço que contém o Spark, o ficheiro de tarefa, dependências e registos de ligado de armazenamento do Azure.  Se não especificar um valor para esta propriedade, o armazenamento associado com o cluster do HDInsight é utilizado. | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Tenha em atenção os seguintes pontos:

- O **tipo** estiver definida como **HDInsightSpark**.
- O **rootPath** está definida como **adfspark\\pyFiles** onde adfspark é o contentor de Blobs do Azure e pyFiles é uma pasta bem nesse contentor. Neste exemplo, o armazenamento de Blobs do Azure é o que está associado ao cluster do Spark. Pode carregar o ficheiro para um armazenamento do Azure diferente. Se fizer isso, crie um serviço ligado do armazenamento do Azure para ligar essa conta de armazenamento à fábrica de dados. Em seguida, especifique o nome do serviço ligado como um valor para o **sparkJobLinkedService** propriedade. Ver as propriedades de atividade do Spark para obter detalhes sobre esta propriedade e outras propriedades compatíveis com a atividade do Spark.
- O **entryFilePath** está definido para o **test.py**, que é o ficheiro de python.
- O **getDebugInfo** estiver definida como **sempre**, que significa que os ficheiros de registo são sempre gerado (êxito ou falha).

    > [!IMPORTANT]
    > Recomendamos que não definir esta propriedade para sempre num ambiente de produção, a menos que esteja a resolver um problema.
- O **produz** secção tem um conjunto de dados de saída. Tem de especificar um conjunto de dados de saída, mesmo que o programa spark não produz qualquer saída. O conjunto de dados de saída controla a agenda para o pipeline (hora a hora, diária, etc.).

Para obter mais informações sobre a atividade, consulte [atividade do Spark](data-factory-spark.md) artigo.

## <a name="machine-learning-batch-execution-activity"></a>Atividade de Execução de Lote de Machine Learning
Pode especificar as seguintes propriedades num definição JSON de atividade de execução de lote do Azure Machine Learning studio. A propriedade de tipo para a atividade tem de ser: **AzureMLBatchExecution**. Tem de criar uma máquina do Azure pela primeira vez o serviço ligado de aprendizagem e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como AzureMLBatchExecution:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
webServiceInput | O conjunto de dados a serem passados como entrada para o serviço de web do Azure Machine Learning studio. Este conjunto de dados também deve ser incluído nas entradas para a atividade. |Utilize webServiceInput ou webServiceInputs. |
webServiceInputs | Especifica os conjuntos de dados a serem passados como entradas para o serviço de web do Azure Machine Learning studio. Se o serviço web tem várias entradas, utilize a propriedade webServiceInputs em vez de usar a propriedade webServiceInput. Conjuntos de dados que são referenciados pela **webServiceInputs** também têm de ser incluídos na atividade **entradas**. | Utilize webServiceInput ou webServiceInputs. |
webServiceOutputs | Os conjuntos de dados que foram atribuídos como saídas para o serviço de web do Azure Machine Learning studio. O web service retorna dados de saída este conjunto de dados. | Sim |
globalParameters | Especifique valores para os parâmetros de serviço web nesta secção. | Não |

### <a name="json-example"></a>Exemplo de JSON
Neste exemplo, a atividade possui o conjunto de dados **MLSqlInput** como entrada e **MLSqlOutput** como o resultado. O **MLSqlInput** é passado como entrada para o serviço web utilizando o **webServiceInput** propriedade JSON. O **MLSqlOutput** é transmitido como uma saída para o serviço Web utilizando o **webServiceOutputs** propriedade JSON.

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

O exemplo de JSON, o serviço Azure Machine Learning Web implementado utiliza um leitor e um módulo de escritor de dados de/para uma base de dados do SQL do Azure de leitura/escrita. Esse Web service expõe os seguintes quatro parâmetros:  Nome do servidor de base de dados, nome de base de dados, nome de conta de utilizador do servidor e palavra-passe de conta de utilizador Server.

> [!NOTE]
> Apenas entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no fragmento JSON acima, MLSqlInput for uma entrada para a atividade de AzureMLBatchExecution, que é passada como entrada para o serviço Web via parâmetro webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Atividade de Recursos de Atualização de Machine Learning
Pode especificar as seguintes propriedades num definição JSON de atividade de recursos de atualização do Azure Machine Learning studio. A propriedade de tipo para a atividade tem de ser: **AzureMLUpdateResource**. Tem de criar uma máquina do Azure pela primeira vez o serviço ligado de aprendizagem e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como AzureMLUpdateResource:

Propriedade | Descrição | Necessário
-------- | ----------- | --------
trainedModelName | Nome do modelo retrained. | Sim |
trainedModelDatasetName | O conjunto de dados que aponte para o ficheiro iLearner devolvido pela operação de reparametrização. | Sim |

### <a name="json-example"></a>Exemplo de JSON
O pipeline tem duas atividades: **AzureMLBatchExecution** e **AzureMLUpdateResource**. O atividade de execução de lote do Azure Machine Learning studio usa os dados de treinamento como entrada e produz um ficheiro iLearner como saída. A atividade invoca o serviço web de treinamento (experimentação de preparação exposto como um serviço web) com os dados de entrada de treinamento e recebe o ficheiro ilearner a partir do serviço Web. O placeholderBlob é apenas um dataset de saída fictício que é necessário pelo serviço do Azure Data Factory para executar o pipeline.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
Pode especificar as seguintes propriedades numa definição de JSON de atividade de U-SQL. A propriedade de tipo para a atividade tem de ser: **DataLakeAnalyticsU-SQL**. Tem de criar um serviço ligado do Azure Data Lake Analytics e especificar o nome do mesmo como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como DataLakeAnalyticsU-SQL:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| scriptPath |Caminho para a pasta que contém o script de U-SQL. Nome do ficheiro diferencia maiúsculas de minúsculas. |Não (se usar o script) |
| scriptLinkedService |Serviço ligado que liga o armazenamento que contém o script para a fábrica de dados |Não (se usar o script) |
| Script |Especifique o script inline em vez de especificar scriptPath e scriptLinkedService. Por exemplo: "script": "Teste de criar o banco de dados". |Não (se usar scriptPath e scriptLinkedService) |
| degreeOfParallelism |O número máximo de nós em simultâneo utilizada para executar a tarefa. |Não |
| prioridade |Determina quais os trabalhos em fila de espera devem ser selecionados para executar primeiro. Menor o número, maior será a prioridade. |Não |
| parâmetros |Parâmetros para o script de U-SQL |Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte [atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Atividade de Procedimento Armazenado
Pode especificar as seguintes propriedades numa definição de JSON de atividade de procedimento armazenado. A propriedade de tipo para a atividade tem de ser: **SqlServerStoredProcedure**. Tem de criar um dos seguintes serviços ligados e especificar o nome do serviço ligado como um valor para o **linkedServiceName** propriedade:

- SQL Server
- Base de Dados SQL do Azure
- Armazém de Dados SQL do Azure

As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como SqlServerStoredProcedure:

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| storedProcedureName |Especifique o nome do procedimento armazenado na base de dados SQL do Azure ou Azure SQL Data Warehouse que é representado pelo serviço ligado que utilize a tabela de saída. |Sim |
| storedProcedureParameters |Especifique os valores dos parâmetros do procedimento armazenado. Se precisar de passar nulo para um parâmetro, utilize a sintaxe: "param1": null (todas as letras minúsculas). Veja o exemplo seguinte para saber como utilizar esta propriedade. |Não |

Se especificar um conjunto de dados de entrada, tem de estar disponível (no estado "Pronto") para a atividade de procedimento armazenado ser executado. O conjunto de dados de entrada não pode ser consumido no procedimento armazenado como um parâmetro. Só é utilizado para verificar a dependência antes de iniciar a atividade de procedimento armazenado. Tem de especificar um conjunto de dados de saída para uma atividade de procedimento armazenado.

Conjunto de dados de saída especifica a **agenda** para a atividade de procedimento armazenado (hora a hora, semanalmente, mensalmente, etc.). O conjunto de dados de saída tem de utilizar um **serviço ligado** que se refere a uma base de dados do SQL do Azure ou um Azure SQL Data Warehouse ou uma base de dados SQL Server em que pretenda que o procedimento armazenado a executar. O conjunto de dados de saída pode servir como uma forma de transmitir o resultado do procedimento armazenado para processamento por outra atividade subsequente ([encadear atividades](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) no pipeline. No entanto, a fábrica de dados não automaticamente escrever a saída de um procedimento armazenado para este conjunto de dados. É o procedimento armazenado que escreve para uma tabela SQL que o conjunto de dados de saída aponta para. Em alguns casos, o conjunto de dados de saída pode ser um **conjunto de dados fictício**, que é utilizado apenas para especificar a agenda para executar a atividade de procedimento armazenado.

### <a name="json-example"></a>Exemplo de JSON

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Para obter mais informações, consulte [atividade de procedimento armazenado](data-factory-stored-proc-activity.md) artigo.

## <a name="net-custom-activity"></a>Atividade personalizada do .NET
Pode especificar as seguintes propriedades numa atividade personalizada .NET de definição de JSON. A propriedade de tipo para a atividade tem de ser: **DotNetActivity**. Tem de criar um serviço ligado de HDInsight de Azure ou um ligado do Azure Batch service e especificar o nome do serviço ligado como um valor para o **linkedServiceName** propriedade. As seguintes propriedades são suportadas os **typeProperties** secção ao definir o tipo de atividade como DotNetActivity:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| AssemblyName | Nome do assembly. No exemplo, é: **MyDotnetActivity.dll**. | Sim |
| EntryPoint |Nome da classe que implementa a interface IDotNetActivity. No exemplo, é: **Mydotnetactivityns. Mydotnetactivity** onde MyDotNetActivityNS é o espaço de nomes e MyDotNetActivity é a classe.  | Sim |
| PackageLinkedService | Nome do serviço ligado do armazenamento do Azure que aponta para o armazenamento de BLOBs que contém o ficheiro de zip da atividade personalizada. No exemplo, é: **AzureStorageLinkedService**.| Sim |
| PackageFile | Nome do ficheiro zip. No exemplo, é: **customactivitycontainer**. | Sim |
| extendedProperties | Propriedades expandidas, que pode definir e transmitir para o código .NET. Neste exemplo, o **SliceStart** variável é definida como um valor com base na variável do sistema do SliceStart. | Não |

### <a name="json-example"></a>Exemplo de JSON

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Para obter informações detalhadas, consulte [utilizar atividades personalizadas no Data Factory](data-factory-use-custom-activities.md) artigo.

## <a name="next-steps"></a>Próximos Passos
Veja os tutoriais seguintes:

- [Tutorial: criar um pipeline com uma atividade de cópia](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Tutorial: criar um pipeline com uma atividade do hive](data-factory-build-your-first-pipeline-using-editor.md)
