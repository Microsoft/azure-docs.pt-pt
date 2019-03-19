---
title: As funções de fábrica de dados e variáveis do sistema | Documentos da Microsoft
description: Fornece uma lista de funções do Azure Data Factory e variáveis de sistema
documentationcenter: ''
author: sharonlo101
manager: craigg
services: data-factory
ms.assetid: b6b3c2ae-b0e8-4e28-90d8-daf20421660d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5293c44a3e4494593e069ab45fbc38806c6999ee
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57976781"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Fábrica de dados do Azure - funções e variáveis de sistema
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [variáveis do sistema no Data Factory](../control-flow-system-variables.md).

Este artigo fornece informações sobre as funções e variáveis suportadas pelo Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variáveis de sistema de fábrica de dados

| Nome da variável | Descrição | Âmbito de objeto | Âmbito JSON e casos de utilização |
| --- | --- | --- | --- |
| WindowStart |Início do intervalo de tempo para a janela de execução da atividade atual |atividade |<ol><li>Especifique as consultas de seleção de dados. Veja os artigos de conector referenciados no [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo.</li> |
| WindowEnd |Fim do intervalo de tempo para a janela de execução da atividade atual |atividade |mesmo que WindowStart. |
| SliceStart |Início do intervalo de tempo para o setor de dados a ser produzido |atividade<br/>Conjunto de dados |<ol><li>Especifique os caminhos de pasta dinâmico e nomes de ficheiros durante o trabalho com [BLOBs do Azure](data-factory-azure-blob-connector.md) e [conjuntos de dados do sistema de ficheiros](data-factory-onprem-file-system-connector.md).</li><li>Especifica dependências de entrada com as funções de fábrica de dados na coleção de entradas de atividade.</li></ol> |
| SliceEnd |Fim do intervalo de tempo para o setor de dados atual. |atividade<br/>Conjunto de dados |mesmo que SliceStart. |

> [!NOTE]
> Atualmente a fábrica de dados requer que o cronograma especificado na atividade exatamente corresponde à agenda especificada na disponibilidade do conjunto de dados de saída. Por conseguinte, WindowStart, WindowEnd e SliceStart e SliceEnd sempre mapeiam para o mesmo período de tempo e um setor de saída individual.
> 

### <a name="example-for-using-a-system-variable"></a>Exemplo de uso de uma variável do sistema
No exemplo seguinte, ano, mês, dia e hora do **SliceStart** são extraídos em variáveis separadas, que são utilizadas pelo **folderPath** e **fileName** propriedades.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funções de fábrica de dados
Pode usar funções da fábrica de dados, juntamente com variáveis do sistema para os seguintes fins:

1. Especificar as consultas de seleção de dados (veja os artigos de conector referenciados pela [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo.
   
   A sintaxe para invocar uma função de fábrica de dados é: **$$ <function>** para consultas de seleção de dados e outras propriedades na atividade e conjuntos de dados.  
2. Especificar dependências de entrada com as funções de fábrica de dados na coleção de entradas de atividade.
   
    US $$ não é necessária para a especificação de expressões de dependência de entrada.     

No exemplo a seguir **sqlReaderQuery** propriedade num ficheiro JSON é atribuída um valor devolvido pelo `Text.Format` função. Este exemplo também usa uma variável do sistema com o nome **WindowStart**, que representa a hora de início da janela de execução da atividade.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Ver [data de personalizado e cadeias de caracteres de formato de tempo](https://msdn.microsoft.com/library/8kb3ddd4.aspx) tópico que descreve as diferentes opções de formatação, pode utilizar (por exemplo: ay versus aaaa). 

### <a name="functions"></a>Funções
As tabelas seguintes listam todas as funções no Azure Data Factory:

| Categoria | Função | Parâmetros | Descrição |
| --- | --- | --- | --- |
| Hora |AddHours(X,Y) |X: DateTime <br/><br/>Y int |Adiciona as horas de Y para o momento de X. <br/><br/>Exemplo: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Hora |AddMinutes(X,Y) |X: DateTime <br/><br/>Y int |Adiciona Y minutos a X.<br/><br/>Exemplo: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Hora |StartOfHour(X) |X: Datetime |Obtém a hora de início para a hora representada pelo componente de hora de X. <br/><br/>Exemplo: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X: DateTime<br/><br/>Y int |Adiciona Y dias a X. <br/><br/>Exemplo: 9/15/2013:00 12:00 + 2 dias = 9/17 //Build/ de 2013:00 12:00.<br/><br/>Pode subtrair dias demasiado especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X: DateTime<br/><br/>Y int |Adiciona os meses de Y a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Pode subtrai demasiado meses ao especificar Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X: DateTime <br/><br/>Y int |Adiciona Y * X em três meses.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X: DateTime<br/><br/>Y int |Adiciona Y * 7 dias para X<br/><br/>Exemplo: 9/15/2013:00 12:00 + 1 9 = semana/22 //Build/ de 2013:00 12:00<br/><br/>Pode subtrai demasiado semanas ao especificar Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X: DateTime<br/><br/>Y int |Adiciona os anos de Y a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Pode subtrai demasiado anos ao especificar Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Day(X) |X: DateTime |Obtém o componente de dia de X.<br/><br/>Exemplo: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(X) |X: DateTime |Obtém o dia do componente da semana de X.<br/><br/>Exemplo: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: DateTime |Obtém o dia do ano, representado pelo componente de ano de X.<br/><br/>Exemplos:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X: DateTime |Obtém os dias no mês representado pelo componente de mês do parâmetro X.<br/><br/>Exemplo: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay(X) |X: DateTime |Obtém a data-hora que representa o fim do dia (componente de dia) de X.<br/><br/>Exemplo: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth(X) |X: DateTime |Obtém o fim do mês representado pelo componente de mês do parâmetro X. <br/><br/>Exemplo: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (data e hora que representa o fim do mês de Setembro) |
| Date |StartOfDay(X) |X: DateTime |Obtém o início do dia representado pelo componente de dia do parâmetro X.<br/><br/>Exemplo: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |FROM(X) |X: String |Analisar a cadeia de caracteres X para uma hora de data. |
| DateTime |Ticks(X) |X: DateTime |Obtém os ticks propriedade do parâmetro X. Uma escala é igual a 100 nanossegundos. O valor desta propriedade representa o número de tiques decorridos desde 12: 00:00 meia-noite, 1 de Janeiro, 0001. |
| Texto |Format(X) |X: Variável de cadeia de caracteres |Formata o texto (use `\\'` combinação para o escape `'` carateres).|

> [!IMPORTANT]
> Quando utilizar uma função dentro de outra função, não é necessário usar **$$** prefixo para a função interna. Por exemplo: $$Text.Format ("PartitionKey eq \\' my_pkey_filter_value\\' e RowKey ge \\' {0: DD-MM-AAAA HH: mm:}\\', Time.AddHours (SliceStart, -6)). Neste exemplo, tenha em atenção que **$$** prefixo não é utilizado para o **Time.AddHours** função. 

#### <a name="example"></a>Exemplo
No exemplo a seguir, são determinados parâmetros de entrada e saídos para a atividade do Hive com o `Text.Format` função e a variável de sistema do SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Exemplo 2

No exemplo a seguir, o parâmetro de DateTime para a atividade de procedimento armazenado é determinado usando o texto. Função de formato e a variável do SliceStart. 

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
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Exemplo 3
Para ler os dados do dia anterior, em vez de dia representado pelo SliceStart, use a função de AddDays, conforme mostrado no exemplo a seguir: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Ver [data de personalizado e cadeias de caracteres de formato de tempo](https://msdn.microsoft.com/library/8kb3ddd4.aspx) tópico que descreve as diferentes opções de formatação, pode utilizar (por exemplo: aa versus aaaa). 

