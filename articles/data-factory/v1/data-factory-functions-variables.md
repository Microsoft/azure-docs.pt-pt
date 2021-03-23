---
title: Funções de fábrica de dados e variáveis do sistema
description: Fornece uma lista de funções de Fábrica de Dados Azure e variáveis do sistema
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 498f40a1783903b5dca0a2fe3204cc6aa25a2fec
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786435"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Fábrica de Dados Azure - Funções e Variáveis do Sistema
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [as variáveis do Sistema na Data Factory.](../control-flow-system-variables.md)

Este artigo fornece informações sobre funções e variáveis apoiadas pela Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variáveis do sistema data factory

| Nome da Variável | Descrição | Âmbito do objeto | Casos de âmbito e utilização JSON |
| --- | --- | --- | --- |
| JanelaStart |Intervalo de início do intervalo de tempo para a janela de execução da atividade atual |atividade |<ol><li>Especifique consultas de seleção de dados. Consulte os artigos de conector referenciados no artigo atividades de [movimento de dados.](data-factory-data-movement-activities.md)</li> |
| WindowEnd |Intervalo de fim de tempo para a janela de execução da atividade atual |atividade |o mesmo que o WindowStart. |
| SliceStart |Intervalo de início do tempo para a produção de fatia de dados |atividade<br/>conjunto de dados |<ol><li>Especifique caminhos dinâmicos de pasta e nomes de ficheiros enquanto trabalha com [conjuntos de dados do](data-factory-onprem-file-system-connector.md) [Azure Blob](data-factory-azure-blob-connector.md) e do Sistema de Ficheiros .</li><li>Especifique as dependências de entradas com funções de fábrica de dados na recolha de entradas de atividade.</li></ol> |
| SliceEnd |Intervalo de fim de tempo para a atual fatia de dados. |atividade<br/>conjunto de dados |o mesmo que o SliceStart. |

> [!NOTE]
> Atualmente, a fábrica de dados requer que o calendário especificado na atividade corresponda exatamente ao calendário especificado na disponibilidade do conjunto de dados de saída. Portanto, o WindowStart, o WindowEnd e o SliceStart e o SliceEnd mapeiam sempre para o mesmo período de tempo e uma única fatia de saída.
> 

### <a name="example-for-using-a-system-variable"></a>Exemplo para a utilização de uma variável do sistema
No exemplo seguinte, ano, mês, dia e hora do **SliceStart** são extraídos em variáveis separadas que são usadas por **propriedades de pastaPapa** e **fileName.**

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
Pode utilizar funções na fábrica de dados juntamente com variáveis do sistema para as seguintes finalidades:

1. Especificar consultas de seleção de dados (ver artigos de conector referenciados pelo artigo [de Atividades de Movimento de Dados.](data-factory-data-movement-activities.md)
   
   A sintaxe para invocar uma função de fábrica de dados é: **$$\<function>** para consultas de seleção de dados e outras propriedades na atividade e conjuntos de dados.  
2. Especificar as dependências de entradas com funções de fábrica de dados na recolha de entradas de atividade.
   
    $$ não é necessário para especificar expressões de dependência de entrada.     

Na amostra seguinte, a propriedade **sqlReaderQuery** num ficheiro JSON é atribuída a um valor devolvido pela `Text.Format` função. Esta amostra também usa uma variável do sistema chamada **WindowStart,** que representa a hora de início da janela de execução da atividade.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Consulte o tópico de cordas de data e formato de tempo personalizado que descreve [diferentes](/dotnet/standard/base-types/custom-date-and-time-format-strings) opções de formatação que pode utilizar (por exemplo: ay vs. yyyy). 

### <a name="functions"></a>Funções
As tabelas a seguir enumeram todas as funções na Azure Data Factory:

| Categoria | Função | Parâmetros | Descrição |
| --- | --- | --- | --- |
| Hora |AddHours (X,Y) |X: DataTime <br/><br/>Y: int |Adiciona Y horas ao tempo X dado. <br/><br/>Exemplo: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Hora |AddMinutes (X,Y) |X: DataTime <br/><br/>Y: int |Adiciona minutos Y a X.<br/><br/>Exemplo: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Hora |StartOfHour(X) |X: Hora da data |Obtém a hora de partida para a hora representada pelo componente de hora de X. <br/><br/>Exemplo: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Data |AddDays (X,Y) |X: DataTime<br/><br/>Y: int |Adiciona os dias Y a X. <br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 2 dias = 9/17/2013 12:00:00 PM.<br/><br/>Também pode subtrair dias especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Data |AddMonths (X,Y) |X: DataTime<br/><br/>Y: int |Adiciona Y meses a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Também pode subtrair meses especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Data |AddQuarters (X,Y) |X: DataTime <br/><br/>Y: int |Adiciona Y * 3 meses a X.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Data |AddWeeks (X,Y) |X: DataTime<br/><br/>Y: int |Adiciona Y * 7 dias a X<br/><br/>Exemplo: 9/15/2013 12:00:00 + 1 semana = 22/2013 12:00:00:00<br/><br/>Também pode subtrair semanas especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Data |AddYears (X,Y) |X: DataTime<br/><br/>Y: int |Adiciona Y anos a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Também pode subtrair anos especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Data |Dia (X) |X: DataTime |Obtém o componente do dia de X.<br/><br/>Exemplo: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Data |DayOfWeek (X) |X: DataTime |Recebe o componente do dia da semana de X.<br/><br/>Exemplo: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Data |DayOfYear (X) |X: DataTime |Recebe o dia do ano representado pela componente do ano de X.<br/><br/>Exemplos:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Data |DaysInMonth(X) |X: DataTime |Recebe os dias do mês representados pela componente mensal do parâmetro X.<br/><br/>Exemplo: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Data |Fim do Dia(X) |X: DataTime |Obtém a data-hora que representa o final do dia (componente do dia) de X.<br/><br/>Exemplo: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Data |EndOfMonth(X) |X: DataTime |Recebe o final do mês representado por componente mensal do parâmetro X. <br/><br/>Exemplo: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (data que representa o final do mês de setembro) |
| Data |Início da Jornada (X) |X: DataTime |Obtém o início do dia representado pelo componente do dia do parâmetro X.<br/><br/>Exemplo: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |De(X) |X: Corda |Parse string X para uma hora de data. |
| DateTime |Carrapatos(X) |X: DataTime |Obtém a propriedade do parâmetro X. Um carrapato é igual a 100 nanosegundos. O valor deste imóvel representa o número de carraças que decorreram desde as 12:00:00 da meia-noite, 1 de janeiro de 0001. |
| Texto |Formato (X) |X: Variável de corda |Formatos do texto (utilizar `\\'` a combinação para escapar ao `'` carácter).|

> [!IMPORTANT]
> Quando utilizar uma função dentro de outra função, não precisa de utilizar **$$** o prefixo para a função interna. Por exemplo: $$Text.Formato ('PartitionKey eq \\ \\ 'my_pkey_filter_value' e RowKey ge \\ '{0: yyyy-MM-dd HH:mm:mm:ss} \\ '', Time.AddHours (SliceStart, -6)). Neste exemplo, note que **$$** o prefixo não é utilizado para a função **Time.AddHours.** 

#### <a name="example"></a>Exemplo
No exemplo seguinte, os parâmetros de entrada e saída para a atividade da Colmeia são determinados utilizando a função e a `Text.Format` variável do sistema SliceStart. 

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

No exemplo seguinte, o parâmetro DataTime para a Atividade do Procedimento Armazenado é determinado através da utilização do Texto. Função de formato e a variável SliceStart. 

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
Para ler dados do dia anterior em vez do dia representado pelo SliceStart, utilize a função AddDays como mostrado no exemplo seguinte: 

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

Consulte o tópico de cordas de formato de data e hora personalizado que descreve [diferentes](/dotnet/standard/base-types/custom-date-and-time-format-strings) opções de formatação que pode utilizar (por exemplo: yy vs. yyyy).