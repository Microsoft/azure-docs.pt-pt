---
title: Funções Data Factory e variáveis do sistema
description: Fornece uma lista de funções Azure Data Factory e variáveis do sistema
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73667658"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Funções de Azure Data Factory e variáveis do sistema
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [variáveis do sistema em data Factory](../control-flow-system-variables.md).

Este artigo fornece informações sobre funções e variáveis com suporte pelo Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variáveis do sistema Data Factory

| Nome da variável | Descrição | Escopo do objeto | Escopo JSON e casos de uso |
| --- | --- | --- | --- |
| WindowStart |Início do intervalo de tempo para a janela de execução da atividade atual |actividade |<ol><li>Especifique consultas de seleção de dados. Consulte os artigos de conector mencionados no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Fim do intervalo de tempo para a janela de execução da atividade atual |actividade |o mesmo que WindowStart. |
| SliceStart |Início do intervalo de tempo para a fatia de dados ser produzida |actividade<br/>DataSet |<ol><li>Especifique os caminhos de pasta dinâmica e os nomes de arquivo ao trabalhar com o [blob do Azure](data-factory-azure-blob-connector.md) e os [conjuntos de arquivos do sistema de arquivo](data-factory-onprem-file-system-connector.md).</li><li>Especifique as dependências de entrada com data factory funções na coleção de entradas da atividade.</li></ol> |
| SliceEnd |Fim do intervalo de tempo para a fatia de dados atual. |actividade<br/>DataSet |o mesmo que SliceStart. |

> [!NOTE]
> Atualmente data factory requer que o agendamento especificado na atividade corresponda exatamente à agenda especificada na disponibilidade do conjunto de resultados de saída. Portanto, WindowStart, WindowEnd e SliceStart e SliceEnd sempre são mapeados para o mesmo período de tempo e uma única fatia de saída.
> 

### <a name="example-for-using-a-system-variable"></a>Exemplo de uso de uma variável de sistema
No exemplo a seguir, ano, mês, dia e hora de **SliceStart** são extraídos em variáveis separadas que são usadas pelas propriedades **FolderPath** e **filename** .

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

## <a name="data-factory-functions"></a>Funções de Data Factory
Você pode usar funções no data factory juntamente com variáveis do sistema para as seguintes finalidades:

1. Especificação de consultas de seleção de dados (consulte os artigos de conector referenciados pelo artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) .
   
   A sintaxe para invocar uma função de data factory é: **$$\<de função >** para consultas de seleção de dados e outras propriedades na atividade e nos DataSets.  
2. Especificar dependências de entrada com data factory funções na coleção de entradas de atividade.
   
    $ $ Não é necessário para especificar expressões de dependência de entrada.     

No exemplo a seguir, a propriedade **sqlReaderQuery** em um arquivo JSON é atribuída a um valor retornado pela função `Text.Format`. Este exemplo também usa uma variável de sistema chamada **WindowStart**, que representa a hora de início da janela de execução da atividade.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Confira o tópico [cadeias de caracteres de formato de data e hora personalizadas](https://msdn.microsoft.com/library/8kb3ddd4.aspx) que descrevem diferentes opções de formatação que você pode usar (por exemplo: tox yyyy). 

### <a name="functions"></a>Funções
As tabelas a seguir listam todas as funções no Azure Data Factory:

| Categoria | Função | Parâmetros | Descrição |
| --- | --- | --- | --- |
| Hora |AddHours (X, Y) |X: DateTime <br/><br/>Y: int |Adiciona Y horas ao determinado tempo X. <br/><br/>Exemplo: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Hora |Addminutos (X, Y) |X: DateTime <br/><br/>Y: int |Adiciona Y minutos a X.<br/><br/>Exemplo: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Hora |StartOfHour (X) |X: DateTime |Obtém a hora de início da hora representada pelo componente de hora de X. <br/><br/>Exemplo: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |Subdias (X, Y) |X: DateTime<br/><br/>Y: int |Adiciona Y dias a X. <br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 2 dias = 9/17/2013 12:00:00 PM.<br/><br/>Você pode subtrair dias também especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths (X, Y) |X: DateTime<br/><br/>Y: int |Adiciona Y meses a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Você pode subtrair meses também especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |Subtrimestres (X, Y) |X: DateTime <br/><br/>Y: int |Adiciona Y * 3 meses a X.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |Addsemanas (X, Y) |X: DateTime<br/><br/>Y: int |Adiciona Y * 7 dias a X<br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 1 semana = 9/22/2013 12:00:00 PM<br/><br/>Você pode subtrair semanas também especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears (X, Y) |X: DateTime<br/><br/>Y: int |Adiciona Y anos a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Você pode subtrair anos também especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Dia (X) |X: DateTime |Obtém o componente de dia de X.<br/><br/>Exemplo: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek (X) |X: DateTime |Obtém o componente do dia da semana de X.<br/><br/>Exemplo: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear (X) |X: DateTime |Obtém o dia do ano representado pelo componente de ano de X.<br/><br/>Exemplos:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth (X) |X: DateTime |Obtém os dias do mês representados pelo componente de mês do parâmetro X.<br/><br/>Exemplo: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay (X) |X: DateTime |Obtém a data e hora que representa o fim do dia (componente de dia) de X.<br/><br/>Exemplo: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth (X) |X: DateTime |Obtém o fim do mês representado pelo componente de mês do parâmetro X. <br/><br/>Exemplo: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (data e hora que representa o fim do mês de setembro) |
| Date |StartOfDay (X) |X: DateTime |Obtém o início do dia representado pelo componente de dia do parâmetro X.<br/><br/>Exemplo: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |De (X) |X: cadeia de caracteres |Analise a cadeia de caracteres X para uma data e hora. |
| DateTime |Tiques (X) |X: DateTime |Obtém a propriedade tiques do parâmetro X. Uma marca é igual a 100 nanossegundos. O valor dessa propriedade representa o número de tiques decorridos desde 12:00:00-noite, 1º de janeiro de 0001. |
| Texto |Formato (X) |X: variável de cadeia de caracteres |Formata o texto (use `\\'` combinação para `'` caractere de escape).|

> [!IMPORTANT]
> Ao usar uma função dentro de outra função, você não precisa usar **$$** prefixo para a função interna. Por exemplo: $ $Text. Format (' PartitionKey EQ \\' my_pkey_filter_value\\' e RowKey GE \\' {0: YYYY-MM-DD HH: mm: SS}\\' ', time. AddHours (SliceStart,-6)). Neste exemplo, observe que **$$** prefixo não é usado para a função **time. AddHours** . 

#### <a name="example"></a>Exemplo
No exemplo a seguir, os parâmetros de entrada e saída para a atividade de Hive são determinados usando a função `Text.Format` e a variável de sistema SliceStart. 

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

No exemplo a seguir, o parâmetro DateTime para a atividade de procedimento armazenado é determinado usando o texto. Função Format e a variável SliceStart. 

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
Para ler dados do dia anterior em vez do dia representado pelo SliceStart, use a função AddDays, conforme mostrado no exemplo a seguir: 

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

Consulte o tópico [cadeias de caracteres de formato de data e hora personalizadas](https://msdn.microsoft.com/library/8kb3ddd4.aspx) que descreve diferentes opções de formatação que você pode usar (por exemplo: yy versus yyyy). 

